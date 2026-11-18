# Test and Validation Plan — Spotlight

Team: Shashwat Mishra, Rohan Shetty | Advisor: Dr. Hrishikesh Vinayak Bhide

Version 0.1 • 2026-11-18 • Draft for team review. Traces to Requirements_Document.md v0.1 and Technical_Specification.md v0.1 (updated to v0.2 for test numbering). Proposed test coordinator: Rohan; both members test their own modules. No cases executed.

## 1. Scope

CS 5002, January-April 2027. Test access and integrity before search, then AI. All 24 Week 9 groups are preserved; TC-nn.m are individual cases. AI Should cases are planned, but mandatory security gates must pass before enabling AI.

| Component / owner | Requirement IDs |
| --- | --- |
| C1 Desktop Interface / Rohan | REQ-02/07/11; NFR-11 |
| C2 Search Service / Shashwat | REQ-01/03/07; NFR-05/08/10 |
| C3 Synchronization Service / Shashwat | REQ-04/05/06; NFR-06 |
| C4 Source Adapters / Shashwat | REQ-04/06/11; CON-03 |
| C5 Access Control / Shashwat | NFR-08/09/13; CON-04 |
| C6 Local Index and State Store / Rohan | REQ-01/04/05; NFR-05/10; CON-01 |
| C7 AI Context Engine / Shashwat | REQ-08/09/10; NFR-07/09/12 |
| Both / planning and compliance | CON-01–04 |

Out of scope: hosted accounts, shared workspaces, agentic writes, mandatory embeddings, fine-tuning and additional issue trackers (Week 9 Won’t); NFR-11 Could is a conditional pilot target. No validation of arbitrary LLM factual claims or immediate remote revocation within the five-minute cache window. Test our integration with SQLite, GitHub, Electron, OS vault and Ollama, not their internal implementations.

## 2. Verification and validation strategy

Team verification checks all REQ, NFR and CON IDs using deterministic fixtures, API contracts and D2 failures. Stakeholder validation uses first-time developers for search/navigation, a team lead for AI usefulness, and advisor/repository owner review for scope and data policy. Model checks vary evidence, question and provider failure; five repeated staging trials per question record citation validity, groundedness and latency. Proposed validation: at least four of five answers supported by supplied evidence; no unsupported answer is treated as fact. This is supplemental, not a new release requirement.

| Layer | Approx. count | Focus |
| --- | --- | --- |
| Unit | 40 | Go ranking, normalization, policy, schema |
| Integration | 30 | SQLite, mock GitHub/model, local API |
| System | 12 | Electron workflows and errors |
| Load/fault | 8 | 10k corpus, deadlines, rollback |
| Acceptance | 2 sessions | Developer and team-lead feedback |

## 3. Success criteria (metrics)

| Metric | Target | Measurement | ID |
| --- | --- | --- | --- |
| Search results | All expected matches within limit; four required fields | Compare fixture IDs and fields | REQ-01 |
| Navigation | Correct reference; zero unsafe launches | Mock OS opener log | REQ-02 |
| Relationships | Exact project-scoped IDs; zero duplicates | Compare expected ID set | REQ-03 |
| Sync status | Committed records searchable; status/count/timestamps correct | API + SQLite snapshot | REQ-04 |
| No-change sync | 0 added/updated, 0 duplicate keys | Row count and unique keys | REQ-05 |
| Authentication failure | Failed within 5 s of error; 0 record/cursor changes | Monotonic clock; snapshot | REQ-06 |
| No match | 0 results; explicit message | UI / API inspection | REQ-07 |
| AI context | At most 10 authorized records; source-qualified citations | Mock request and response | REQ-08 |
| No evidence | 0 model calls; no_context; null answer | Mock request count | REQ-09 |
| Invalid citations | 0 displayed answers with missing/unknown IDs | Mock payload and UI | REQ-10 |
| Configuration | Add/reconnect/disable; disabled results absent | API and search | REQ-11 |
| Search latency | ≤2 s on 10,000 records (matching and empty) | 20 fixed trials; wall clock each | NFR-05 |
| Sync latency | 100 records ≤60 s; 0 duplicates | Mock normal API; clock | NFR-06 |
| AI deadline | Answer or explicit failure ≤30 s | End-to-end clock | NFR-07 |
| Authorization | 100% invalid tokens/profile rejected; >5 min excluded | API and controlled clock | NFR-08 |
| Cloud egress | 0 calls without consent and all-record policy | Mock cloud request log | NFR-09 |
| Index failure | Error ≤5 s; safe log; config unchanged | Fault injection and snapshot | NFR-10 |
| Usability | ≥4/5 first-time users finish ≤120 s unaided | Observer timer; conditional pilot | NFR-11 |
| AI isolation | Search available; 0 automatic cloud calls | Fail model then search | NFR-12 |
| Secret handling | 0 plaintext secrets; 0 prompt/body logs | Scan fake sentinel values | NFR-13 |
| Cost | 0 USD recurring mandatory infrastructure | Inspect deployment and costs | CON-01 |
| Schedule | Prototype by Spring 2027; two owners | M21/M22 review | CON-02 |
| Legal | 100% fixtures authorized; license inventory reviewed | Fixture manifest and review | CON-03 |
| Local/read-only | 0 source mutations or unauthorized exports | Mock methods; git status; vault scan | CON-04 |

Thresholds inherit Week 9; trial counts and fixture details are proposed test procedures. Record every trial, including failures; no averaging away individual deadline failures.

## 4. Test cases

Reset fixtures and logs before each case unless stated. Use local profile local, valid launch token, sources S1/S2, denied source S3, and the fixture manifest in Section 5. Search = POST /api/v1/search; sync = POST /api/v1/sync; context = POST /api/v1/context; sources = POST /api/v1/sources. Each API call includes the required fields from Week 9. Expected error names follow that specification. Owners follow Section 1; both review evidence.

| ID | Description | Steps (in order) | Expected result | Status |
| --- | --- | --- | --- | --- |
| TC-01.1 | Two-source search (REQ-01) | 1. Seed S1/S2. 2. search SPOT-42. | Exact expected IDs, ranked; title/source/type/reference present. | Not Run |
| TC-01.2 | Rank and ties (REQ-01) | 1. Seed equal-score rows with different dates/IDs. 2. repeat search twice. | Exact references first; ties use updatedAt/row_id; same order. | Not Run |
| TC-01.3 | Denied source / limit (REQ-01) | 1. Add matching S3. 2. search with limit 1. 3. repeat limit 0. | 1 authorized result; S3 absent; invalid limit 400. | Not Run |
| TC-02.1 | Open HTTPS reference (REQ-02) | 1. Select S2 PR. 2. activate original-source action. | Opener called once with exact approved HTTPS URL. | Not Run |
| TC-02.2 | Open local reference (REQ-02) | 1. Select S1 file in approved root. 2. open. | Opener called once with canonical approved path. | Not Run |
| TC-02.3 | Unsafe navigation (REQ-02) | 1. Attempt javascript URI and ../ outside root. 2. revoke source and retry. | 0 launches; visible rejection for each. | Not Run |
| TC-03.1 | Related work (REQ-03) | 1. Query SPOT-42 in S2. 2. compare commit and PR IDs. | Exact related IDs; no duplicate. | Not Run |
| TC-03.2 | Cross-project collision (REQ-03) | 1. Seed OTHER project with same numeric identifier. 2. query SPOT-42. | No unrelated project records in relationship set. | Not Run |
| TC-04.1 | Successful sync (REQ-04) | 1. Mock 100 new records. 2. sync S2. 3. poll status. 4. search new token. | success; count 100; completion/last-success times set; all 100 indexed. | Not Run |
| TC-04.2 | Status only (REQ-04) | 1. Call action=status twice after run. 2. compare run count. | Same terminal result; 0 extra runs. | Not Run |
| TC-04.3 | Concurrent / bad batch (REQ-04) | 1. Hold run open. 2. start another. 3. then return missing required ID. | Second 409; first failed; no staged records committed. | Not Run |
| TC-05.1 | Repeat unchanged (REQ-05) | 1. Sync S2. 2. snapshot. 3. repeat same cursor/data. | success count 0; unchanged record keys/count. | Not Run |
| TC-05.2 | Duplicate page (REQ-05) | 1. Mock repeated stable ID with changed title. 2. sync. | One row for the stable key; updated title; zero duplicates. | Not Run |
| TC-06.1 | 401 after page (REQ-06) | 1. Seed old rows. 2. return one staged page then 401. 3. query status. | failed/auth cause ≤5 s after 401; records/cursor unchanged. | Not Run |

## 4. Test cases (continued)

| ID | Description | Steps (in order) | Expected result | Status |
| --- | --- | --- | --- | --- |
| TC-06.2 | 403 rejection (REQ-06) | 1. Mock 403 permission/auth rejection. 2. sync. | Failed, not retried as transient; no record/cursor changes; source suppressed. | Not Run |
| TC-06.3 | Recover reconnect (REQ-06) | 1. After 401, reconnect with valid test token. 2. sync 100 rows. | Prior failure retained; successful run commits; no duplicates. | Not Run |
| TC-07.1 | No match (REQ-07) | 1. Search zz_no_match. 2. open UI results. | 0 records and explicit no-match message. | Not Run |
| TC-07.2 | Invalid not empty (REQ-07) | 1. Search empty string. 2. compare with no-match result. | 400 INVALID_QUERY, distinct from zero matches. | Not Run |
| TC-08.1 | Bounded answer (REQ-08) | 1. Seed 12 authorized matches. 2. ask SPOT-42. 3. mock valid cited answer. | ≤10 context items, authorized only; displayed answer cites supplied IDs. | Not Run |
| TC-08.2 | Permission changes (REQ-08) | 1. Revoke selected source after retrieval before model call. | No model call; request denied; no denied citation. | Not Run |
| TC-09.1 | No evidence (REQ-09) | 1. Ask zz_no_match. 2. inspect model log. | no_context; null answer; 0 calls. | Not Run |
| TC-09.2 | Denied-only evidence (REQ-09) | 1. Ask unique S3 token with source denied. | no_context; null answer; 0 calls. | Not Run |
| TC-10.1 | Valid citation (REQ-10) | 1. Mock response citing supplied S2 record ID. 2. request context. | Answer shown with resolvable source-qualified citation. | Not Run |
| TC-10.2 | Invalid citation (REQ-10) | 1. Repeat with absent citation and unknown ID. | 502 INVALID_MODEL_RESPONSE each; no displayed answer. | Not Run |
| TC-11.1 | Configure sources (REQ-11) | 1. Add S1 local root and S2 GitHub mock. 2. reconnect S2. | Approved after verification; source IDs retained on reconnect. | Not Run |
| TC-11.2 | Disable or bad locator (REQ-11) | 1. Disable S2. 2. search unique token. 3. add invalid path. | S2 absent; invalid locator 400; no bad source enabled. | Not Run |
| TC-12.1 | Matching load (NFR-05) | 1. Seed 10k. 2. search SPOT-42 20 times, reset timer each. | Every trial ≤2 s and exact matches within limit. | Not Run |
| TC-12.2 | Empty load (NFR-05) | 1. Same corpus. 2. search zz_no_match 20 times. | Every trial ≤2 s and zero results. | Not Run |

## 4. Test cases (continued)

| ID | Description | Steps (in order) | Expected result | Status |
| --- | --- | --- | --- | --- |
| TC-12.3 | Concurrent sync (NFR-05) | 1. During staged sync, repeat seeded query 20 times. | Each ≤2 s; only committed snapshot results. | Not Run |
| TC-13.1 | 100-record load (NFR-06) | 1. Mock normal API, 100 new rows. 2. time sync. | ≤60 s; 100 committed; zero duplicate keys. | Not Run |
| TC-13.2 | Slow external (NFR-06) | 1. Mock blocked page beyond deadline. 2. sync. | Failure by run deadline; prior rows/cursor preserved. | Not Run |
| TC-14.1 | Normal AI time (NFR-07) | 1. Mock valid answer after 1 s. 2. time context from submit. | Answer with citations ≤30 s. | Not Run |
| TC-14.2 | Timeout (NFR-07) | 1. Mock provider never returns. 2. time outcome. | Explicit failure ≤30 s; no answer. | Not Run |
| TC-15.1 | Token matrix (NFR-08) | 1. Call all four routes with missing, invalid and valid token. 2. wrong profile where applicable. | 100% invalid/missing 401; mismatched profile 403; no protected output. | Not Run |
| TC-15.2 | Stale remote (NFR-08) | 1. Set lastVerifiedAt 4:59 then 5:01 ago. 2. search remote token. | Fresh approved visible; stale excluded. | Not Run |
| TC-15.3 | Revocation / vault failure (NFR-08) | 1. Revoke S2. 2. deny vault access. 3. attempt search and context. | Denied/unverified results absent; no protected egress. | Not Run |
| TC-16.1 | No consent (NFR-09) | 1. Select cloud mock with consent=false. 2. ask matching question. | 0 cloud calls; access denied. | Not Run |
| TC-16.2 | Mixed destination policy (NFR-09) | 1. Consent=true. 2. one of selected records disallows provider. | 0 cloud calls; whole request denied. | Not Run |
| TC-16.3 | Approved cloud (NFR-09) | 1. Consent=true. 2. all selected records explicitly approved. 3. call mock. | One permitted call; only approved evidence; no automatic fallback. | Not Run |
| TC-17.1 | Index unavailable (NFR-10) | 1. Inject storage unavailable. 2. search. 3. time UI error and inspect log. | 503 INDEX_UNAVAILABLE and visible error ≤5 s; safe log; config unchanged. | Not Run |
| TC-17.2 | Recovery (NFR-10) | 1. Restore index after previous fixture. 2. repeat query. | Expected matches; configuration unchanged. | Not Run |
| TC-17.3 | Read error during search (NFR-10) | 1. Inject query read failure after validation. | No partial success; error ≤5 s; safe log; config unchanged. | Not Run |

## 4. Test cases (continued)

| ID | Description | Steps (in order) | Expected result | Status |
| --- | --- | --- | --- | --- |
| TC-18.1 | First-time task (NFR-11) | 1. If pilot enabled, give five new developers task: find SPOT-42 PR and open it. 2. time unaided. | At least 4/5 complete ≤120 s. | Not Run |
| TC-18.2 | Failure recording (NFR-11) | 1. During pilot, record timeout or incorrect PR as unsuccessful. | No failed/assisted attempt counted as success. | Not Run |
| TC-19.1 | Model unavailable (NFR-12) | 1. Stop mock local model. 2. ask question. 3. then search SPOT-42. | Explicit AI failure; ordinary search returns expected results; 0 cloud calls. | Not Run |
| TC-19.2 | Invalid model response (NFR-12) | 1. Return malformed JSON. 2. retry ordinary search. | AI error; normal search still works; zero cloud fallback. | Not Run |
| TC-20.1 | Vault storage (NFR-13) | 1. Enter fake secret TEST_SECRET_01. 2. inspect SQLite and repo. | 0 sentinel matches; vault reference only. | Not Run |
| TC-20.2 | Log redaction (NFR-13) | 1. Sync/auth fail and ask unique prompt/body sentinel. 2. scan logs. | 0 secret, prompt or body sentinel matches. | Not Run |
| TC-20.3 | Vault unavailable (NFR-13) | 1. Disable vault. 2. request sync. 3. inspect output/log/database. | Fail closed; 0 plaintext credential writes or exposure. | Not Run |
| TC-21.1 | Local infrastructure (CON-01) | 1. Run packaged app with no hosted database/backend. 2. inspect deployment bill. | Core runs locally; $0 mandatory recurring infrastructure. | Not Run |
| TC-21.2 | Paid provider disabled (CON-01) | 1. Remove cloud config/API key. 2. run search and sync mock. | Core works; 0 paid model calls. | Not Run |
| TC-22.1 | Plan review (CON-02) | 1. At M21 compare T1–T12 evidence and owners to Week 8. | Two owners; working prototype demonstrated by Apr 23 target; otherwise Fail and log gap. | Not Run |
| TC-22.2 | Schedule slip (CON-02) | 1. At milestone review, simulate late critical task. 2. inspect reserve and revised plan. | Owner and date assigned; no unapproved scope expansion. | Not Run |
| TC-23.1 | Fixture provenance (CON-03) | 1. Inspect manifest for each repo and dependency license. | 100% authorized/public fixtures and reviewed license entries. | Not Run |
| TC-23.2 | Unknown provenance (CON-03) | 1. Add fixture without permission record. 2. run fixture admission review. | Fixture excluded until authorized. | Not Run |
| TC-24.1 | Read-only integration (CON-04) | 1. Sync mock and local repo. 2. inspect HTTP methods and git working tree. | 0 source mutation calls; tree unchanged. | Not Run |
| TC-24.2 | Unauthorized export (CON-04) | 1. Try denied cloud context and inspect vault/DB/logs. | 0 external payloads; 0 plaintext credentials. | Not Run |

## 5. Test environment

Proposed baseline, not measured hardware. At M15 record exact OS build, CPU/RAM, Go/Node/Electron/Chromium/SQLite/Ollama versions and model digest in tests/environment.md; pin dependency lockfiles and use the same build for all timed trials. No separate browser is needed except original-source navigation.

| Item | Development | Staging | Pilot |
| --- | --- | --- | --- |
| Host | Windows 11 24H2 x64; Ubuntu 24.04 CI | Windows 11 24H2 x64; target 4 cores, 16 GB RAM, SSD | Developer workstation; record actual specs |
| Runtime | Go 1.25.x, Node 22.x, SQLite with FTS5; Electron pinned at M15 | Same lockfiles; packaged app; Chromium version from Electron | Tagged staging build |
| Model | Mock HTTP; fake vault | Ollama local; model/version TBD; mock cloud | Local only unless separately approved |
| Network | Loopback; mock 401/403/429/503/timeout | Normal API fixture: 100 ms/page, no faults; real GitHub smoke by permission | Own network; offline search check |
| Tools | Go test, HTTP mock, temporary SQLite, UI automation and stopwatch | Same; fault injection and logs | Observer sheet; approved synthetic repos |

Seed specification (to implement at T1/T3): 10,000 total records, S1 local Git and S2 GitHub each 4,995 approved records, S3 10 denied records. Exactly 12 approved records contain SPOT-42 (six each); S3 contains one SPOT-42 and unique denied-only token. Seed six explicitly related S2 records (three commits, three PRs) and one other-project collision; store expected IDs/rank ties in fixtures/expected.json. Include valid local file, HTTPS PR URL, unsafe URI, nonexistent path, zz_no_match absent from all rows. Mock 100 new records with stable IDs, paginated responses and repeat/no-change mode. Seed fake secrets/prompt/body sentinels, never real confidential data. Use fresh temporary database and reset controlled clock/logs per case. Fixture generator, mock endpoints and exact model selection are planned, not yet available.

## 6. Pass and fail criteria

Pass only if every expected result and applicable metric holds; Fail if any condition misses or crashes; Blocked if external setup prevents execution; Not Run until executed. Blocked is never a pass. Use fixed samples; capture raw timings, request logs, snapshots and screenshots under the test ID. Sev 1 = leak, unauthorized access/write or corruption; Sev 2 = Must failure or missed mandatory threshold; Sev 3 = Should defect with workaround; Sev 4 = cosmetic.

Release gate: 100% Must cases pass (TC-01/02/04/06/12/15/16/17/20); all four constraint groups verified; 0 open Sev 1/2; ≥90% of applicable Should cases pass and all mandatory metrics pass. Any failed Should case has a documented owner and advisor-approved release exception. Optional AI may be disabled only with team/advisor agreement; security-gate tests remain mandatory. TC-18 is conditional, reported deferred if not run. No blocked Must cases at release.

## 7. Schedule

Dates follow Week 8 Implementation_Plan.md, M15–M22; proposed Spring dates require calendar confirmation. Unit and integration checks run during each sprint and on pull requests; system and acceptance run before demos. Risk-first order protects access, integrity and then AI.

| Milestone | Test activity | Owner | Evidence |
| --- | --- | --- | --- |
| M15 Jan 22 | T1/T2 schema, access and secret tests (TC-15/20), mock seed and CI | Both | Schema/CI logs; denied access |
| M16 Feb 5 | T3/T4 adapter read and source setup (TC-11/23/24); UI safety TC-02 | Both | Mock HTTP logs, fixture manifest |
| M17 Feb 19 | T5 sync success, failures, duplicate and timing (TC-04/05/06/13) | Shashwat | Run logs and DB snapshots |
| M18 Mar 5 | T6/T7 search, relationships, navigation, no-match and index failure (TC-01/02/03/07/12/17) | Both | Search fixture report and timings |
| M19 Mar 26 | T8/T9 AI context, consent, citations, deadline, outage (TC-08/09/10/14/16/19) | Both | Mock egress logs; cited answer demo |
| M20 Apr 9 | T10/T11 system regression, all Must, performance/security; conditional TC-18 pilot and team-lead review | Rohan / Shashwat | Test report, observer sheets, defect list |
| M21 Apr 23 | T12 final retest and constraint review TC-21–24; advisor demonstration | Both | Signed review if obtained, release checklist |
| M22 Apr 30 | Handoff final report, test sources and evidence; review deferred cases | Both / advisor | Tagged build and test report |

## 8. Definition of done

Feature checklist (all applicable items required):

[ ] Mapped requirement and test IDs; unit, integration and edge cases pass.

[ ] Other team member reviews code; safe handling and no secrets confirmed.

[ ] API and diagrams updated if changed; reproducible test evidence committed without private data.

[ ] Zero open Sev 1/2 defects; proposed unit coverage ≥80% for core Go modules.

Project checklist:

[ ] All release gates in Section 6 met, including constraints and security.

[ ] Test report records every status, actual metric, environment/version and unresolved defect.

[ ] Requirements, technical specification, README and test plan cross-references agree.

[ ] Stakeholder validation recorded if conducted; deferred items explicitly listed.

[ ] Advisor reviews final results and signs off; team verifies handoff.

Open items: team approval of cases/metrics, exact hardware and dependency versions, selected model and permission policy, pilot recruitment and advisor review. No test results, sign-off or implementation completion are asserted.
