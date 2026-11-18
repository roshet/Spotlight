# Technical Specification Document - Spotlight

**Team:** Shashwat Mishra, Rohan Shetty  
**Advisor:** Dr. Hrishikesh Vinayak Bhide  
**Version:** 0.1 - 2026-11-18 (draft for team and advisor review)

**Traces to:** Requirements_Document.md v0.1 (2026-11-18). Terms follow its glossary.

## 1. Version and Change Log

| Version | Date | Change | Author / review |
| --- | --- | --- | --- |
| 0.1 | 2026-11-18 | Consolidates D0–D2 and Week 8 plan. Maps new requirement IDs to existing interfaces and planned test groups. No architectural redesign. | Team draft; approval pending |

## 2. Architecture Summary

Spotlight is a layered local client-server application with a synchronization pipeline. [D0 block diagram](Design_Diagrams/D0_Block.png), [data flow](Design_Diagrams/D0_Data_Flow.png), [AI flow](Design_Diagrams/D0_AI_Flow.png), [D1 data model](Design_Diagrams/D1_Data_Model.png), [D2 sync](Design_Diagrams/D2_Sync_Flow.png) and [D2 access](Design_Diagrams/D2_Access_Flow.png) are authoritative figures. The Electron desktop shell (C1) calls a Go backend over loopback HTTP using a per-launch session token retained in the main process. C2 Search Service retrieves authorized records from C6 Local Index and State Store (SQLite/FTS5). C3 Synchronization Service stages normalized records from C4 Source Adapters and commits a batch transactionally. C5 Access Control checks local profile, repository access and credential vault. C7 AI Context Engine retrieves up to ten records via C2, verifies policy through C5 and sends bounded evidence to a configured local or approved cloud model. Answers are ephemeral and source-linked. No hosted database or server is required.

## 3. Interfaces and Data Formats

### 3.1 Component boundaries (D0 IDs retained)

| Interface | Input (name:type) | Output (name:type) | Format / protocol | Error handling / owner |
| --- | --- | --- | --- | --- |
| I1 Actor ↔ C1 | query:string; question:string?; filters:map; sourceId:string; action:enum | results:list; answer:object?; status:object; message:string | Text / structured view; desktop interaction | C1: show input validation or operation error. |
| I2 C1 ↔ C2 | query:string; filters:map; profileId:string | results:Record[]; error:Error? | JSON; local request/response | C2: return INDEX_UNAVAILABLE or invalid query; C1 displays. |
| I3 C1 ↔ C3 | sourceId:string; action:enum(sync/status) | status:enum; count:int; completedAt:timestamp?; error:Error? | JSON; local request/response | C3: report failure and last successful time; C1 displays. |
| I4 C3 ↔ C4 | sourceId:string; cursor:string? | records:Record[]; nextCursor:string?; error:Error? | Typed objects; internal call | C4 maps source failures; C3 aborts uncommitted batch. |
| I5 C4 ↔ external sources | read operation:enum; repo/path:string; cursor:string?; credential:string? | raw records:list; status:int? | Native Git/filesystem read or HTTPS API JSON | C4 handles missing path, 401/403, rate limit, timeout; no source writes. |
| I6 C3 ↔ C6 | batch:Record[]; sourceId:string; status:object; cursor:string? | committed:bool; count:int; error:Error? | Typed objects / indexed rows; local storage transaction | C6 rolls back incomplete record batch; C3 records failed attempt separately. |
| I7 C2 ↔ C6 | query:string; filters:map | candidates:Record[]; error:Error? | Typed objects / indexed rows; local query | C6 reports unavailable index; C2 logs and returns error. |
| I8 C2 ↔ C5 | profileId:string; candidateSourceIds:string[] | allowedSourceIds:string[]; error:Error? | Typed objects; internal call | C5 denies unknown or revoked sources; C2 suppresses denied results. |
| I9 C4 ↔ C5 | profileId:string; sourceId:string; requestedScope:enum(read) | allowed:bool; credential:string?; error:Error? | Typed objects; internal call | C5 denies unapproved scope or missing credential; C4 stops read. |
| I10 C5 ↔ OS vault | profileId:string; credentialRef:string; operation:enum | credential:string?; outcome:enum | Native OS credential interface | C5 fails closed; secrets never included in logs or index. |
| I11 C1 ↔ OS/browser | sourceRef:string; action:enum(open) | opened:bool; error:Error? | URI / local path; OS launch interface | C1 validates allowed URI/path and reports launch failure. |
| I12 C1 ↔ C7 | question:string; profileId:string; providerId:string; consent:bool | answer:string?; citations:string[]; status:enum; error:Error? | JSON; local request/response | C7 returns NO_CONTEXT, AI_DISABLED or MODEL_UNAVAILABLE; C1 preserves ordinary search. |
| I13 C7 ↔ C2 | query:string; profileId:string; limit:int | results:Record[]; error:Error? | Typed objects; internal call | C2 enforces access checks; C7 stops generation on retrieval failure or zero records. |
| I14 C7 ↔ model provider | question:string; context:ContextItem[]; maxOutputTokens:int | answer:string; citedIds:string[]; error:Error? | JSON; local model interface or HTTPS API | C7 handles timeout, rate limit, malformed response; no automatic cloud fallback. |
| I15 C7 ↔ C5 | profileId:string; providerId:string; recordIds:string[]; consent:bool | allowedRecordIds:string[]; allowed:bool; credentialRef:string?; error:Error? | Typed objects; internal call | C5 denies absent consent, unapproved destination, or unverified permissions; C7 sends no denied records. |

### 3.2 Shared message types

JSON UTF-8, timestamps UTC RFC3339, identifiers nonempty strings up to 256 characters. Record = {id:string, sourceId:string, type:enum(commit,pull_request,issue,file), title:string, body:string, sourceRef:string, updatedAt:timestamp, workItemRefs:string[]}. Error = {code:string,message:string}; messages exclude secrets and private text. Filters = {type?:Record.type,sourceId?:string}. A question mark denotes optional. Counts are nonnegative integers. Citation ID encodes the sourceId/id pair; it is not a globally unique external ID alone. ContextItem = {id:string,excerpt:string} with excerpt ≤800 characters. Internal calls are typed Go structs. I7 also takes allowedSourceIds and limit as refined in D1. I6 failed-run commits status only, not records. I10 set takes a secret; get returns transient credential only. I14 uses a remaining deadline in milliseconds, maxOutputTokens 1–512, and up to ten context items. All calls on I15 fail closed if any context record is denied.

### 3.3 Local API endpoints

All routes require the launch token and active profile; 401 for missing/invalid token, 403 for mismatched profile. No remote listener. Version prefix /api/v1; changing required fields, response semantics or error contract requires a new major version. Errors use the relevant envelope with Error object, empty results or null answer, never secrets.

| Method/path | Input (required unless ?) | Output | Errors |
| --- | --- | --- | --- |
| POST /api/v1/search (I2) | query:string 1–500; profileId:string; filters?:Filters; limit?:int 1–100 default 50 | 200 {results:Record[],error:Error\|null} | 400 INVALID_QUERY/FILTER; 503 INDEX_UNAVAILABLE |
| POST /api/v1/context (I12) | question:string 1–1000; profileId:string; providerId:string; consent:bool | 200 {answer:string\|null,citations:string[],status:ok or no_context,error:null} | 400 INVALID_QUERY/PROVIDER; 403 ACCESS_DENIED; 503 AI_DISABLED/MODEL_UNAVAILABLE/INDEX_UNAVAILABLE; 502 INVALID_MODEL_RESPONSE; 504 MODEL_TIMEOUT |
| POST /api/v1/sync (I3) | sourceId:string; action:sync or status | 200 {status:idle/running/success/failed,count:int,completedAt?:timestamp,lastSuccessfulAt?:timestamp,error:Error?} | 400 invalid action; 403 denied; 404 source; 409 busy; 503 store/source failure |
| POST /api/v1/sources (D2 proposed adjunct) | action:add/reconnect/disable; sourceId:string; kind?:local_git/github; locator?:string; credential?:string; add requires kind/locator | 200 {sourceId:string,status:approved/denied/unverified,error:Error?} | 400 bad locator; 403 denied; 503 vault/store failure |

Example (I2 request and empty response):

```json
{"query":"SPOT-42","profileId":"local","limit":50}
```
```json
{"results":[],"error":null}
```

Provider configuration is an analogous local D2 proposed method; its exact route and schema remain open (OQ-02/05). I11 uses a constrained OS opener, not a public API; source permission is rechecked before opening. Logs use timestamp, component, operation, errorCode and requestId only.

### 3.4 Data model and behavior

| Entity (D1) | Key data and constraints |
| --- | --- |
| Profile | profile_id PK; single local identity |
| Source | source_id PK, profile_id FK, kind, locator, cursor, credential_ref, last_success; D2 approval state, lastVerifiedAt, permittedCloudProviders |
| EngineeringRecord | row_id PK, source_id FK, external_id, type, title, body, source_ref, updated_at; unique(source_id, external_id) |
| WorkItemReference | (row_id, ref) composite PK; project-scoped ref index |
| SyncRun | run_id PK, source_id FK, status, count, completed_at, error_code |
| FTS5 | Derived rowid maps record; title/body/refs indexed |

C2 A1 escapes query operators, restricts allowed sources, ranks exact references before BM25, deduplicates and uses updatedAt/row_id tie-breaks. C3 B2 stages up to 10,000 records, one run per source, retries transient errors once within 60 seconds, rolls back on failure, records failed run separately. C4 B3 maps read-only Git and HTTPS responses to canonical records. C5 B4 denies stale (>5 min) or unverified remote permissions. C7 A2 bounds model context to ten excerpts and 512 output tokens, validates cited IDs and rechecks permissions; citation membership does not establish factual accuracy. C1 B1 ignores stale responses and displays plain text. See D1 A1/A2 and D2 B1–B4/E1–E7 for detailed edge cases.

## 4. Technology Stack

| Layer | Choice | Justification |
| --- | --- | --- |
| Desktop / UI | Electron, React, TypeScript | Team web experience; source navigation and local UX (REQ-02, NFR-11). |
| Backend | Go net/http | Single local process with bounded requests (NFR-05, CON-02). |
| Data / search | SQLite, FTS5, BM25 | No database host; atomic commits and indexed retrieval (CON-01, REQ-04/05). |
| Integrations | Local Git, GitHub HTTPS API | Read-only source adapters (CON-03, REQ-04). |
| AI | RAG, Ollama local runtime; optional approved cloud API | Bounded context and fallback to search (REQ-08, NFR-09/12). |
| Security | OS vault, loopback launch token | No plaintext secrets or public listener (NFR-08/13). |
| Hosting / packaging | Local workstation | No mandatory infrastructure (CON-01). |
| Testing | Go tests, mock HTTP/model, UI fixtures | Repeatable failures and timing checks (NFR-05/10). |

## 5. Known Constraints

| ID | Constraint | Rules out | Forces |
| --- | --- | --- | --- |
| CON-01 | $0 recurring infrastructure target; optional paid AI deferred unless approved. | Hosted database or mandatory paid model | Local SQLite and optional local runtime |
| CON-02 | Two students; working prototype by Spring 2027. | Broad integrations, independent deployments | Local Git/GitHub first; bounded scope and T1–T12 |
| CON-03 | Use public or explicitly authorized test repositories; comply with API terms and software licenses. | Unauthorized datasets or mutation outside scope | Read-only adapters, fixture authorization and license review |
| CON-04 | Read-only scoped access, credentials outside source control, local data by default; external AI requires explicit authorization. | Default cloud export and plaintext secrets | OS vault, authorization and consent gate |

## 6. Traceability Matrix

Status describes design maturity, not implementation. Test groups TC-01 onward are reserved for Week 10; no test execution is claimed. All requirements map to existing design elements; pending validation is noted in status.

| Req ID | Description | Design Component (D0/D1/D2) | Status | Test Case |
| --- | --- | --- | --- | --- |
| REQ-01 | Return authorized matching engineering records from at least two indexed sources, ranked by relevance, with title, source, type and original reference. | D1 A1, FTS5, I2/I7; D2 E2-B | Designed | TC-01 |
| REQ-02 | Open the original reference of a selected authorized record; reject unsafe URI schemes and paths outside approved roots. | D2 B1, I11; E1-I/X | Designed | TC-02 |
| REQ-03 | Retrieve commits and pull requests sharing an explicit project-scoped work-item identifier, without duplicate record IDs. | D1 A1, WorkItemReference; E2-B | Designed | TC-03 |
| REQ-04 | Synchronize a configured source, reporting status, count, completion time and last successful time; make all successfully committed records searchable. | D2 B2, I3/I4/I6, SyncRun; sync figure | Designed | TC-04 |
| REQ-05 | On a no-change synchronization, report zero added or updated records and create zero duplicates. | D1 unique(source_id, external_id); D2 B2, E6-B | Designed | TC-05 |
| REQ-06 | On authentication failure, mark synchronization failed, identify authentication as cause within 5 seconds of the error, and change zero existing records or cursor values. | D2 B2/B3, I9, SyncRun; E3-X | Designed | TC-06 |
| REQ-07 | Return zero results and an explicit no-match message for a query with no matches. | D1 A1, I2; D2 B1, E1-B | Designed | TC-07 |
| REQ-08 | Generate an optional contextual answer from at most 10 authorized retrieved records, with source-qualified citations for substantive answers. | D1 A2, I12–I15; D0 AI flow | In progress | TC-08 |
| REQ-09 | Invoke the model zero times when no relevant authorized records exist; return no_context with no answer. | D1 A2/I13; D2 E7-B | In progress | TC-09 |
| REQ-10 | Reject a substantive model response with missing or unknown citation IDs, displaying no generated answer. | D1 A2/I14; D2 E7-I | In progress | TC-10 |
| REQ-11 | Permit adding, reconnecting and disabling configured local Git or GitHub sources; disabling suppresses the source from subsequent retrieval. | D2 configuration adjunct, B4/I9/I10 | In progress | TC-11 |
| NFR-05 | Return known matching records (within request limit) within 2 seconds for a 10,000-record index; return empty results within 2 seconds for a nonmatching query. | D1 A1/FTS5; D2 E2-B/T | Designed | TC-12 |
| NFR-06 | Process 100 new or changed records within 60 seconds under normal API conditions, with zero duplicates. | D2 B2, I6; E3-T/E6-B | Designed | TC-13 |
| NFR-07 | Return an answer or an explicit failure/no-context outcome within 30 seconds of an AI request. | D1 A2/I14; D2 E7-T | In progress | TC-14 |
| NFR-08 | Reject 100% of invalid launch-token requests and mismatched profiles; exclude denied or unverified remote sources, including permission checks older than 5 minutes. | D2 B4, I8/I9; E5-T/I/X | Designed | TC-15 |
| NFR-09 | Make zero cloud model calls without per-request consent and destination policy approval for every selected record. | D1 A2/I15; D2 access figure, E5-I | In progress | TC-16 |
| NFR-10 | Show an index-unavailable error within 5 seconds, log a safe error and leave integration configuration unchanged. | D1 I2/I7; D2 E2-X/E6-X | Designed | TC-17 |
| NFR-11 | Enable at least 4 of 5 first-time developers to search and open a matching source within 2 minutes without assistance. | D2 B1, I1/I11; target awaiting validation | Planned | TC-18 |
| NFR-12 | Keep ordinary search available when the optional model fails; zero automatic cloud fallback calls. | D1 A2; D2 E7-X | Designed | TC-19 |
| NFR-13 | Store zero plaintext credentials in the database, repository or application logs; omit prompt text and record bodies from logs. | D2 I10/B4, logging; E5-X | Designed | TC-20 |
| CON-01 | $0 recurring infrastructure target; optional paid AI deferred unless approved. | D0-01/08; D1 C6/A2 | Designed | TC-21 |
| CON-02 | Two students; working prototype by Spring 2027. | D0-02; D2 B3; Week 8 plan | Designed | TC-22 |
| CON-03 | Use public or explicitly authorized test repositories; comply with API terms and software licenses. | D2 B3/I5/I9 | Designed | TC-23 |
| CON-04 | Read-only scoped access, credentials outside source control, local data by default; external AI requires explicit authorization. | D2 B4/I10/I15; D1 A2 | Designed | TC-24 |

## 7. Advisor Review

**Status: pending; no review of these documents is evidenced.** Professor Bhide previously accepted the project scope and agreed to advise the team; that is not approval of this specification. Proposed agenda: validate the Must set, AI consent and model choice, five-minute permission window, performance fixtures, and schedule.

| Field | Record |
| --- | --- |
| Date and attendees | Pending actual meeting |
| Discussion | Pending; proposed agenda above |
| Changes produced | None recorded; update both change logs after review |

