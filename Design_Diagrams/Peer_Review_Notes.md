# Peer Design Review Notes — Spotlight

**Team:** Shashwat Mishra, Rohan Shetty  
**Review session:** TODO (date, class section)
**Reviewing teams:** TODO (list every team that reviewed us)

## Action Items

The following action items were identified through an internal review of Spotlight's system design. External peer review feedback will be added once available.

| ID | Criterion | Issue | Action | Owner | Priority | Due | Done? |
|---|---|---|---|---|---|---|---|
| IR-01 | Completeness | The AI Context Engine was not explicitly covered by the original user stories. | Update US-03 and add UC-03 with measurable acceptance criteria for AI-generated context. | Shashwat Mishra | Must-fix | Before implementation | Addressed in proposed revision |
| IR-02 | Risk | Confidential engineering records could be exposed to external LLM providers. | Require explicit authorization before transmitting records to cloud-based LLM providers and support local inference. | Shashwat Mishra | Must-fix | Before implementation | Addressed in design |
| IR-03 | Feasibility | Maintaining cloud infrastructure could increase development effort and recurring costs. | Use SQLite for local data storage and FTS5 for keyword-based retrieval. | Rohan Shetty | Must-fix | Before implementation | Addressed in design |
| IR-04 | Risk | Failed synchronization could leave the local search index in an inconsistent state. | Define atomic synchronization with transaction rollback and preserve previously indexed records upon failure. | Shashwat Mishra | Must-fix | Before implementation | Addressed in design |

### External Peer Feedback

| ID | Criterion | Issue (raised by team) | Action | Owner | Priority | Due | Done? |
|---|---|---|---|---|---|---|---|
| PR-01 | Pending | Pending | Pending | Pending | Pending | Pending | No |

## Summary

**Criterion that drew the most internal review findings:** Risk.

| Criterion | Internal findings |
|---|---:|
| Completeness | 1 |
| Feasibility | 1 |
| Clarity | 0 |
| Risk | 2 |

**Design decisions addressed during internal review:**

* Added explicit requirements and acceptance criteria for the AI Context Engine.
* Defined authorization checks and cloud LLM consent requirements.
* Selected SQLite to maintain a local-first architecture without recurring database hosting costs.
* Defined synchronization failure paths and transaction rollback behavior.

Changes resulting specifically from external peer review have not yet been documented.

## Issues We Raised While Reviewing Other Teams

| Team Reviewed | Top Issue 1 | Top Issue 2 |
|---|---|---|
| Pending documentation | Pending documentation | Pending documentation |
