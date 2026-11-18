# Spotlight - Task List, Milestones, Timeline and Effort Matrix

**Team:** Shashwat Mishra, Rohan Shetty  
**Advisor:** Dr. Hrishikesh Vinayak Bhide

Planning baseline drafted November 18, 2026, after the original October 28 deadline. Dates marked * are provisional, not official course deadlines; Week 1 calendar and Spring 2027 calendar were not supplied. Fall status reflects only known evidence, not verified Canvas submissions. Spring dates, hours, owners and risks are proposed for team/advisor approval; no actual effort is claimed. AI requirements AC-03 are proposed pending team approval. All Spring task dates are 2027.

## 1. Task List (Assignment #5)

T1. Validate the Local Index and State Store (C6) SQLite/FTS5 driver, schema and migrations. Owner: Rohan Shetty.
T2. Develop Access Control (C5) session authentication, OS vault and source authorization. Owner: Shashwat Mishra.
T3. Develop Source Adapters (C4) for local Git and GitHub read-only records (I4/I5/I9). Owner: Shashwat Mishra.
T4. Develop Desktop Interface (C1) shell, validated IPC, source configuration and logging views. Owner: Rohan Shetty.
T5. Develop Synchronization Service (C3) staged ingestion, rollback and status reporting (I3/I6). Owner: Shashwat Mishra.
T6. Develop Search Service (C2) authorized FTS5 retrieval and ranking (A1/I2/I7/I8). Owner: Shashwat Mishra.
T7. Develop Desktop Interface (C1) synchronization, search results and source navigation (I11). Owner: Rohan Shetty.
T8. Develop AI Context Engine (C7) bounded RAG, provider policy and citation checks (A2/I12–I15). Owner: Shashwat Mishra.
T9. Develop Desktop Interface (C1) AI question, consent, answer and citation states (I12). Owner: Rohan Shetty.
T10. Test all seven components end to end against US-01–05, AC-01/02/03 and D2 edge cases. Owner: Rohan Shetty.
T11. Validate Search Service (C2), Synchronization Service (C3), and AI Context Engine (C7) timing and security targets. Owner: Shashwat Mishra.
T12. Refine Desktop Interface (C1) and document the integrated Spotlight prototype, test evidence and demo. Owner: Rohan Shetty.


## 2. Milestones List (Assignment #6)

Dates and evidence checks below are planned unless explicitly marked otherwise. Advisor verification is requested, not presumed.

| ID | Date | Milestone | Verification / outcome | Verifier | Status |
| --- | --- | --- | --- | --- | --- |
| M1 | Sep 2, 2026 | Professional biographies | Both biographies present in repository | Team / instructor | Draft available; submission unverified |
| M2 | Sep 9, 2026* | Team formation | Team roster and project description present; Team Table entry checked | Team / instructor | Draft available; Team Table unverified |
| M3 | Sep 18, 2026 | Advisor and constraints | Advisor acceptance, essay and contract; explicit contract approval | Advisor / instructor | Partial: essay and contract committed; approval pending |
| M4 | Sep 28, 2026 | User stories | Five stories, use cases and criteria in PDF and repository | Team / instructor | Draft available; submission unverified |
| M5 | Oct 2, 2026 | D0 | Seven-component design and interface specifications | Team / instructor | Draft available; submission unverified |
| M6 | Oct 8, 2026 | D1 | Data model, algorithms, API and stack | Team / instructor | Draft available; submission unverified |
| M7 | Oct 14, 2026 | D2 and peer review | Consolidated packet, traceability and peer notes | Team / instructor | D2 committed; peer feedback pending |
| M8 | Oct 28, 2026 | Planning packet | Task list, timeline, effort and risk register | Team / instructor | Current draft; late |
| M9 | Nov 4, 2026* | Technical specification | Requirements and traceability reviewed | Advisor / instructor | Pending / verify |
| M10 | Nov 11, 2026* | Test plan | Acceptance tests linked to requirements | Team / instructor | Pending / verify |
| M11 | Nov 18, 2026* | Week 11 deliverable / presentation | Week 11 work and presentation evidence checked | Instructor | Week 11 work reported done; presentation pending |
| M12 | Nov 25, 2026* | Week 12 checkpoint | Confirm official deliverable and collect evidence | Instructor | Unverified course requirement |
| M13 | Dec 2, 2026* | Week 13 checkpoint | Confirm official deliverable and resolve design gaps | Instructor | Unverified course requirement |
| M14 | Dec 9, 2026* | Week 14 report | Fall report includes approved plan and design evidence | Advisor / instructor | Planned; verify deadline |
| M15 | Jan 22, 2027 | Local foundation | Schema migration, FTS fixture, denied-access test pass | Both / advisor | Planned |
| M16 | Feb 5, 2027 | Source connection | Git/GitHub fixture read and desktop source setup demonstrated | Both / advisor | Planned |
| M17 | Feb 19, 2027 | Atomic ingestion | 100-record sync; rollback and duplicate tests pass | Both / advisor | Planned |
| M18 | Mar 5, 2027 | Core search MVP | Authorized keyword results, source opening and 10k-record fixture | Both / advisor | Planned |
| M19 | Mar 26, 2027 | AI context MVP | Cited answer, no-context and denied-cloud cases demonstrated | Both / advisor | Planned |
| M20 | Apr 9, 2027 | Validation gate | Acceptance tests and timing results recorded | Both / advisor | Planned |
| M21 | Apr 23, 2027 | Final prototype | Demo, documentation and known limitations reviewed | Both / advisor | Planned |
| M22 | Apr 30, 2027* | Release / course handoff | Final submission package accepted for handoff | Advisor / instructor | Proposed; verify course date |

## 3. Table 1 - Timeline (Assignment #6)

Each task reserves its final two weekdays (2 of 10 planned weekdays = 20%) for integration, review and recovery; no new scope in those days. Confirm holidays with the official calendar; Jan 18 holiday may require adjustment. Protect milestone-day review inside each buffer, March 8–12 as a no-new-work reserve (confirm spring break), and April 26–30 exclusively for release/handoff. Winter break Dec 10–Jan 10 is unscheduled. T7/T9 use frozen API contracts and mocks while backend work proceeds. Critical dependency chain: T2 → T3 → T5 → T6 → T8 → T10/T11 → T12 (parallel T1, T7 and T9 also gate downstream work).

| ID | Task / owner | Start | Finish | Depends on | Protected buffer |
| --- | --- | --- | --- | --- | --- |
| T1 | Validate the Local Index and State Store (C6) SQLite/FTS5 driver, schema and migrations. Owner: Rohan Shetty | Jan 11 | Jan 22 | None | Jan 21–22 |
| T2 | Develop Access Control (C5) session authentication, OS vault and source authorization. Owner: Shashwat Mishra | Jan 11 | Jan 22 | None | Jan 21–22 |
| T3 | Develop Source Adapters (C4) for local Git and GitHub read-only records (I4/I5/I9). Owner: Shashwat Mishra | Jan 25 | Feb 5 | T2 | Feb 4–5 |
| T4 | Develop Desktop Interface (C1) shell, validated IPC, source configuration and logging views. Owner: Rohan Shetty | Jan 25 | Feb 5 | T2 | Feb 4–5 |
| T5 | Develop Synchronization Service (C3) staged ingestion, rollback and status reporting (I3/I6). Owner: Shashwat Mishra | Feb 8 | Feb 19 | T1,T3 | Feb 18–19 |
| T6 | Develop Search Service (C2) authorized FTS5 retrieval and ranking (A1/I2/I7/I8). Owner: Shashwat Mishra | Feb 22 | Mar 5 | T1,T2,T5 | Mar 4–5 |
| T7 | Develop Desktop Interface (C1) synchronization, search results and source navigation (I11). Owner: Rohan Shetty | Feb 22 | Mar 5 | T4,T5; D1 I2 contract | Mar 4–5 |
| T8 | Develop AI Context Engine (C7) bounded RAG, provider policy and citation checks (A2/I12–I15). Owner: Shashwat Mishra | Mar 15 | Mar 26 | T2,T6 | Mar 25–26 |
| T9 | Develop Desktop Interface (C1) AI question, consent, answer and citation states (I12). Owner: Rohan Shetty | Mar 15 | Mar 26 | T7; D1 I12 contract | Mar 25–26 |
| T10 | Test all seven components end to end against US-01–05, AC-01/02/03 and D2 edge cases. Owner: Rohan Shetty | Mar 29 | Apr 9 | T6,T7,T8,T9 | Apr 8–9 |
| T11 | Validate Search Service (C2), Synchronization Service (C3), and AI Context Engine (C7) timing and security targets. Owner: Shashwat Mishra | Mar 29 | Apr 9 | T6,T7,T8,T9 | Apr 8–9 |
| T12 | Refine Desktop Interface (C1) and document the integrated Spotlight prototype, test evidence and demo. Owner: Rohan Shetty | Apr 12 | Apr 23 | T10,T11 | Apr 22–23 |
| M1 | Professional biographies | — | Sep 2, 2026 | Milestone verification | Task buffers / gate review |
| M2 | Team formation | — | Sep 9, 2026* | Milestone verification | Task buffers / gate review |
| M3 | Advisor and constraints | — | Sep 18, 2026 | Milestone verification | Task buffers / gate review |
| M4 | User stories | — | Sep 28, 2026 | Milestone verification | Task buffers / gate review |
| M5 | D0 | — | Oct 2, 2026 | Milestone verification | Task buffers / gate review |
| M6 | D1 | — | Oct 8, 2026 | Milestone verification | Task buffers / gate review |
| M7 | D2 and peer review | — | Oct 14, 2026 | Milestone verification | Task buffers / gate review |
| M8 | Planning packet | — | Oct 28, 2026 | Milestone verification | Task buffers / gate review |
| M9 | Technical specification | — | Nov 4, 2026* | Milestone verification | Task buffers / gate review |
| M10 | Test plan | — | Nov 11, 2026* | Milestone verification | Task buffers / gate review |
| M11 | Week 11 deliverable / presentation | — | Nov 18, 2026* | Milestone verification | Task buffers / gate review |
| M12 | Week 12 checkpoint | — | Nov 25, 2026* | Milestone verification | Task buffers / gate review |
| M13 | Week 13 checkpoint | — | Dec 2, 2026* | Milestone verification | Task buffers / gate review |
| M14 | Week 14 report | — | Dec 9, 2026* | Milestone verification | Task buffers / gate review |
| M15 | Local foundation | — | Jan 22, 2027 | Milestone verification | Task buffers / gate review |
| M16 | Source connection | — | Feb 5, 2027 | Milestone verification | Task buffers / gate review |
| M17 | Atomic ingestion | — | Feb 19, 2027 | Milestone verification | Task buffers / gate review |
| M18 | Core search MVP | — | Mar 5, 2027 | Milestone verification | Task buffers / gate review |
| M19 | AI context MVP | — | Mar 26, 2027 | Milestone verification | Task buffers / gate review |
| M20 | Validation gate | — | Apr 9, 2027 | Milestone verification | Task buffers / gate review |
| M21 | Final prototype | — | Apr 23, 2027 | Milestone verification | Task buffers / gate review |
| M22 | Release / course handoff | — | Apr 30, 2027* | Milestone verification | Task buffers / gate review |

## 4. Table 2 - Effort Matrix (Assignment #6)

Proposed person-hours, including testing and review; not timesheet data. Asterisk marks the primary owner. Shashwat: 219 h; Rohan: 208 h; total 427 h. Difference: 11 h. Review actual hours weekly; if disparity exceeds 10% of total, move fixture preparation, documentation or test execution to the lower-loaded member without changing primary ownership unless mutually agreed. Estimates are preliminary single-point assumptions, not owner-provided PERT estimates.

| ID | Task | Shashwat (h) | Rohan (h) | Total (h) |
| --- | --- | --- | --- | --- |
| T1 | Validate the Local Index and State Store (C6) SQLite/FTS5 driver, schema and migrations. | 4 | 24* | 28 |
| T2 | Develop Access Control (C5) session authentication, OS vault and source authorization. | 26* | 5 | 31 |
| T3 | Develop Source Adapters (C4) for local Git and GitHub read-only records (I4/I5/I9). | 26* | 6 | 32 |
| T4 | Develop Desktop Interface (C1) shell, validated IPC, source configuration and logging views. | 5 | 25* | 30 |
| T5 | Develop Synchronization Service (C3) staged ingestion, rollback and status reporting (I3/I6). | 28* | 8 | 36 |
| T6 | Develop Search Service (C2) authorized FTS5 retrieval and ranking (A1/I2/I7/I8). | 26* | 8 | 34 |
| T7 | Develop Desktop Interface (C1) synchronization, search results and source navigation (I11). | 8 | 28* | 36 |
| T8 | Develop AI Context Engine (C7) bounded RAG, provider policy and citation checks (A2/I12–I15). | 28* | 8 | 36 |
| T9 | Develop Desktop Interface (C1) AI question, consent, answer and citation states (I12). | 8 | 24* | 32 |
| T10 | Test all seven components end to end against US-01–05, AC-01/02/03 and D2 edge cases. | 14 | 30* | 44 |
| T11 | Validate Search Service (C2), Synchronization Service (C3), and AI Context Engine (C7) timing and security targets. | 26* | 12 | 38 |
| T12 | Refine Desktop Interface (C1) and document the integrated Spotlight prototype, test evidence and demo. | 20 | 30* | 50 |
| Total |  | 219 | 208 | 427 |

## 5. Work Breakdown Structure

1. Foundation and source connectivity
T1. Validate the Local Index and State Store (C6) SQLite/FTS5 driver, schema and migrations. Owner: Rohan Shetty.
T2. Develop Access Control (C5) session authentication, OS vault and source authorization. Owner: Shashwat Mishra.
T3. Develop Source Adapters (C4) for local Git and GitHub read-only records (I4/I5/I9). Owner: Shashwat Mishra.
T4. Develop Desktop Interface (C1) shell, validated IPC, source configuration and logging views. Owner: Rohan Shetty.
2. Synchronization and search
T5. Develop Synchronization Service (C3) staged ingestion, rollback and status reporting (I3/I6). Owner: Shashwat Mishra.
T6. Develop Search Service (C2) authorized FTS5 retrieval and ranking (A1/I2/I7/I8). Owner: Shashwat Mishra.
T7. Develop Desktop Interface (C1) synchronization, search results and source navigation (I11). Owner: Rohan Shetty.
3. AI context integration
T8. Develop AI Context Engine (C7) bounded RAG, provider policy and citation checks (A2/I12–I15). Owner: Shashwat Mishra.
T9. Develop Desktop Interface (C1) AI question, consent, answer and citation states (I12). Owner: Rohan Shetty.
4. Validation and delivery
T10. Test all seven components end to end against US-01–05, AC-01/02/03 and D2 edge cases. Owner: Rohan Shetty.
T11. Validate Search Service (C2), Synchronization Service (C3), and AI Context Engine (C7) timing and security targets. Owner: Shashwat Mishra.
T12. Refine Desktop Interface (C1) and document the integrated Spotlight prototype, test evidence and demo. Owner: Rohan Shetty.


## 6. Risk Register

Likelihood and impact: L = low, M = medium, H = high. Technical schedule risks are tied to the task buffers and validation gates above. Team and external risks use the protected reserve blocks; Rohan coordinates reassignment and advisor contact.

| ID | Category / risk | L | I | Strategy | Mitigation / schedule link | Owner | Trigger |
| --- | --- | --- | --- | --- | --- | --- | --- |
| R1 | Technical - FTS or driver performance misses 2 s target | M | H | Reduce | Verify FTS support T1; benchmark T11; use indexed filters before adding features | Rohan Shetty | Driver fails FTS fixture or search exceeds 2 s |
| R2 | Technical - Local LLM exceeds RAM or 30 s deadline | H | H | Reduce | T8 test small licensed model; retain search and explicit timeout; no automatic cloud fallback | Shashwat Mishra | Model cannot load or exceeds deadline |
| R3 | Technical - Private records or secrets leak | M | H | Avoid | T2 deny by default, vault; T8 consent and destination policy; T11 negative tests | Shashwat Mishra | Unauthorized record returned or provider call attempted |
| R4 | External - GitHub rate limit / expired credentials | M | M | Reduce | T3 mock API and public fixtures; T5 bounded retry and rollback | Shashwat Mishra | 401/403/429 or timeout |
| R5 | Team - Exams, co-op or missed deadlines reduce capacity | H | H | Reduce | Weekly owner review; protected task buffers, Mar 8–12 reserve and Apr 26–30 handoff reserve; reassign tasks | Rohan Shetty | Milestone slips or member unavailable > 2 weekdays |
| R6 | External - Advisor feedback or course calendar changes | M | H | Reduce | Biweekly advisor check-ins; confirm actual dates before baselining; reserve handoff week | Rohan Shetty | Approval outstanding or official date differs |
| R7 | Technical - Partial sync corrupts index | M | H | Reduce | T5 transactional writes, T10 rollback and restart fixture | Shashwat Mishra | Record count/cursor changes after failed sync |
