# Requirements Document — Spotlight

**Team:** Shashwat Mishra, Rohan Shetty  
**Advisor:** Dr. Hrishikesh Vinayak Bhide  
**Version:** 0.1 - 2026-11-18 (draft for team and advisor review)

## 1. Version and Change Log

| Version | Date | Change | Author / review |
| --- | --- | --- | --- |
| 0.1 | 2026-11-18 | Consolidates Weeks 3–8; assigns REQ and NFR IDs. D2 NFR-01–04 are retired as IDs and mapped to CON-01, CON-03, CON-04 and CON-02 respectively. | Team draft; approval pending |

IDs remain stable; withdrawn items retain their IDs and reasons. AI extension and usability target remain proposed.

## 2. Purpose and Scope

Spotlight is a local-first desktop developer productivity application. Its goal is to unify authorized engineering context and generate grounded answers from retrieved records. The release covers local Git and GitHub indexing, keyword search, original-source navigation, synchronization and optional AI context. It does not require a cloud backend or shared accounts.

## 3. Stakeholders and User Goals

| Role | Category | Goal / source |
| --- | --- | --- |
| Software developer | Primary | Find and verify records (US-01/02) |
| Engineering team lead | Secondary | Trace work items and optionally summarize context (US-03) |
| Integration maintainer | Secondary | Diagnose synchronization failures (US-04) |
| Repository owner / IT security administrator | Hidden | Prevent unauthorized retrieval (US-05) |

## 4. Glossary

| Term | Definition |
| --- | --- |
| Source | Configured local repository or remote development platform connection. |
| Engineering record | Normalized file, commit, PR or issue with source-qualified identity. |
| PR | Pull request: proposed code changes and associated review context. |
| Work-item reference | Explicit project-scoped issue identifier in a record. |
| Index / FTS5 | Local searchable representation of records; SQLite full-text search extension. |
| BM25 | Keyword relevance ranking; lower FTS5 score is better. |
| Sync / cursor | Refresh operation and saved position for incremental retrieval. |
| Profile | Single local application identity, not a hosted account. |
| RAG / LLM | Retrieval-augmented generation / large language model; generate text from selected evidence. |
| Citation | Source-qualified record ID linking generated text to evidence. |
| Vault | OS credential store; secrets are not database fields. |
| Developer / team lead | Search user / engineering reviewer of related work. |
| Maintainer / repository owner | Connection operator / party responsible for access permission. |

## 5. Functional Requirements

Every statement below is a draft requirement. Verification is planned, not executed.

| ID | Requirement | Priority | Source | Verification |
| --- | --- | --- | --- | --- |
| REQ-01 | The system shall return authorized matching engineering records from at least two indexed sources, ranked by relevance, with title, source, type and original reference. | Must | US-01; AC-01.1 | Fixture search |
| REQ-02 | The system shall open the original reference of a selected authorized record; reject unsafe URI schemes and paths outside approved roots. | Must | US-02 | Navigation test |
| REQ-03 | The system shall retrieve commits and pull requests sharing an explicit project-scoped work-item identifier, without duplicate record IDs. | Should | US-03 | Fixture relationship test |
| REQ-04 | The system shall synchronize a configured source, reporting status, count, completion time and last successful time; make all successfully committed records searchable. | Must | US-04; AC-02.1 | Integration test |
| REQ-05 | The system shall on a no-change synchronization, report zero added or updated records and create zero duplicates. | Should | AC-02.2; US-04 | Repeat sync test |
| REQ-06 | The system shall on authentication failure, mark synchronization failed, identify authentication as cause within 5 seconds of the error, and change zero existing records or cursor values. | Must | AC-02.3; US-04 | Mock 401 test |
| REQ-07 | The system shall return zero results and an explicit no-match message for a query with no matches. | Should | AC-01.2; US-01 | UI fixture test |
| REQ-08 | The system shall generate an optional contextual answer from at most 10 authorized retrieved records, with source-qualified citations for substantive answers. | Should | US-03 AI extension; AC-03.1 | Mock model and review |
| REQ-09 | The system shall invoke the model zero times when no relevant authorized records exist; return no_context with no answer. | Should | AC-03.2 | Mock call count |
| REQ-10 | The system shall reject a substantive model response with missing or unknown citation IDs, displaying no generated answer. | Should | AC-03.4 | Invalid citation test |
| REQ-11 | The system shall permit adding, reconnecting and disabling configured local Git or GitHub sources; disabling suppresses the source from subsequent retrieval. | Should | US-04; US-05 | Configuration test |

## 6. Non-Functional Requirements

Performance: NFR-05–07; security: NFR-08/09/13; reliability: NFR-10/12; usability: NFR-11. Tests use a documented target workstation and authorized fixtures.

| ID | Requirement | Priority | Source | Verification |
| --- | --- | --- | --- | --- |
| NFR-05 | The system shall return known matching records (within request limit) within 2 seconds for a 10,000-record index; return empty results within 2 seconds for a nonmatching query. | Must | AC-01.1/2 | Timed fixture test |
| NFR-06 | The system shall process 100 new or changed records within 60 seconds under normal API conditions, with zero duplicates. | Should | AC-02.1 | Timed sync fixture |
| NFR-07 | The system shall return an answer or an explicit failure/no-context outcome within 30 seconds of an AI request. | Should | AC-03.1 | Timed model test |
| NFR-08 | The system shall reject 100% of invalid launch-token requests and mismatched profiles; exclude denied or unverified remote sources, including permission checks older than 5 minutes. | Must | US-05 | Authorization matrix |
| NFR-09 | The system shall make zero cloud model calls without per-request consent and destination policy approval for every selected record. | Must | AC-03.3; US-05 | Mock egress test |
| NFR-10 | The system shall show an index-unavailable error within 5 seconds, log a safe error and leave integration configuration unchanged. | Must | AC-01.3 | Fault injection |
| NFR-11 | The system shall enable at least 4 of 5 first-time developers to search and open a matching source within 2 minutes without assistance. | Could | US-01/02; proposed usability target | Observed usability test |
| NFR-12 | The system shall keep ordinary search available when the optional model fails; zero automatic cloud fallback calls. | Should | US-01; US-05 | Model outage test |
| NFR-13 | The system shall store zero plaintext credentials in the database, repository or application logs; omit prompt text and record bodies from logs. | Must | US-05; CON-04 | Secret scan and log inspection |

## 7. MoSCoW Summary

- **Must:** REQ-01, REQ-02, REQ-04, REQ-06, NFR-05, NFR-08, NFR-09, NFR-10, NFR-13.
- **Should:** REQ-03, REQ-05, REQ-07, REQ-08, REQ-09, REQ-10, REQ-11, NFR-06, NFR-07, NFR-12.
- **Could:** NFR-11.
- **Won’t (this release):** shared workspaces, hosted accounts, agentic write actions, mandatory semantic embeddings, fine-tuning and additional issue-tracker integrations.

Security gates are mandatory even when AI itself is optional: disable AI unless its gates pass.

## 8. Inherited Constraints

| ID | Area | Constraint |
| --- | --- | --- |
| CON-01 | Economic | $0 recurring infrastructure target; optional paid AI deferred unless approved. |
| CON-02 | Professional | Two students; working prototype by Spring 2027. |
| CON-03 | Legal | Use public or explicitly authorized test repositories; comply with API terms and software licenses. |
| CON-04 | Security | Read-only scoped access, credentials outside source control, local data by default; external AI requires explicit authorization. |

## 9. Assumptions and Open Questions

| ID | Assumption / question | Owner | Proposed due |
| --- | --- | --- | --- |
| OQ-01 | Confirm AI extension and prioritization with team/advisor. | Shashwat | 2026-12-04 |
| OQ-02 | Select local model and benchmark on actual workstation; cloud only when approved. | Shashwat | 2027-01-22 |
| OQ-03 | Validate OS vault and five-minute authorization window with repository owners. | Shashwat | 2027-01-22 |
| OQ-04 | Confirm usability threshold and recruit five first-time testers. | Rohan | 2027-02-05 |
| OQ-05 | Confirm source setup API and fixture scope; GitHub issues represent initial issue tracking. | Rohan | 2026-12-04 |
| OQ-06 | Confirm course dates and $0 budget with advisor; test hardware specifications not yet measured. | Rohan | 2026-12-04 |

