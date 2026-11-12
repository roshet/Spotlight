# Spotlight - Ethics and Responsible-Use Statement

**CS 5001 Senior Design I - University of Cincinnati**  
**Project:** Spotlight  
**Team members:** Rohan Shetty and Shashwat Mishra  
**Faculty advisor:** Professor Bhide  
**Date:** November 11, 2026

Spotlight is a developer-productivity prototype intended to reduce context switching by bringing a limited set of software-development records - such as repository metadata, commits, pull requests, and issue tickets - into one searchable interface. The current public repository contains planning documents and biographies but no implemented application, dependency manifest, or deployed service. Accordingly, this statement distinguishes current evidence from controls that will be implemented and verified as development proceeds. No section below should be read as claiming that an unimplemented control has already been tested.

## 1. Privacy summary

Spotlight may process nonpublic engineering metadata, so the primary privacy risk is exposing information from repositories or issue trackers to people who are not authorized to see it. The design principle is data minimization: index only information needed for the selected productivity workflows, preserve the source system's authorization boundaries, and delete indexed data when a user disconnects a source.

| Data item | Source | Purpose | Consent / authorization | Storage | Access | Retention |
|---|---|---|---|---|---|---|
| Account ID and connected workspace/repository IDs | User and connected provider | Associate an index with its authorized owner and selected sources | Connection screen identifies provider, requested scopes, and selected repositories/projects; user confirms before indexing | Application database | Account owner and scoped backend service | Remove on disconnect; target purge within 30 days |
| Integration access token, if required | OAuth or other provider-authorized credential flow | Read only the engineering records the user authorizes | Provider authorization screen; request minimum practical read scopes | Server-side environment or managed secret store; never committed to Git or stored in browser local storage | Integration service only | Revoke on disconnect and delete stored copy immediately |
| Repository ID, name, URL, visibility | Authorized Git provider | Scope search and link results to their source | Explicit provider authorization plus repository selection | Metadata index | Users authorized for that source repository | Remove when connection is deleted; target purge within 30 days |
| Commit hash, message, author display name, timestamp | Authorized Git provider | Connect code changes to related project context | Covered by authorized repository access and disclosed during connection | Metadata index | Users authorized for the source repository | Same lifecycle as indexed repository |
| Pull-request ID, title, status, URL, author | Authorized code-review provider | Find review context and connect work to code changes | Covered by source-specific authorization | Metadata index | Users authorized for the source repository | Same lifecycle as indexed repository |
| Ticket ID, title, status, URL, limited description | Authorized issue tracker | Relate work items to code changes | Separate issue-tracker connection and selected project scope | Metadata index; description subject to minimization review | Users authorized for the connected project | Remove on disconnect; target purge within 30 days |
| Search query | Developer using Spotlight | Return matching indexed records | Search input plus notice that queries are processed | Request memory only by default; no persistent query history planned | Requesting user and backend service | Discard at end of request |
| Operational event: timestamp, route, result code, request ID | Spotlight service | Diagnose failures and abuse without retaining private query content | Disclosed in privacy notice at initial use | Restricted application log; authorization headers, tokens, and query text excluded | Authorized project operators | Proposed 7-day rolling deletion |

**Data minimization and excluded fields.** The initial prototype will not intentionally collect private message bodies, raw source-code uploads, precise location, full IP addresses in the application analytics schema, or user email addresses beyond information a provider requires for authentication. Raw IP address is specifically excluded from the proposed application event schema because route, status, and request-ID diagnostics do not justify retaining it. This is a design exclusion rather than a claim that a previously collected field was removed. Infrastructure providers may still process network metadata under their own policies.

If later functionality requires repository file contents, full ticket descriptions, Slack/Teams messages, or other higher-sensitivity data, the team will first document why the field is needed, update the data-flow diagrams and this inventory, request only the required permissions, and revise retention rules before enabling collection.

**Consent and deletion.** The proposed connection flow is: Connect provider -> display requested scopes and indexed categories -> provider authorization -> user selects repositories/projects -> indexing begins. A Disconnect and delete indexed data action will revoke credentials where the provider supports revocation and initiate deletion of the connection's token and indexed rows. At the end of the capstone project, the team will revoke test credentials, delete the hosted index/database and backups through the selected provider controls, and record completion of the deletion process. The 30-day indexed-data and 7-day log windows are current design targets and must be verified once deletion mechanisms exist.

**Ethical, diversity, and cultural considerations.** Spotlight should not present a search result as an authoritative explanation of why a code change occurred. Results should retain source links and timestamps and label inferred relationships as uncertain. Developer teams use different languages, naming conventions, issue practices, and character sets; the interface should preserve original titles and Unicode characters and should not assume that tickets or repository metadata are English-only. Usability testing should include users with different workflows rather than optimizing only for the team's own habits.

## 2. Security summary

Spotlight may connect to private engineering systems, so authorization boundaries are a core requirement rather than an optional feature. The table below addresses the three common capstone security gaps directly.

| Capstone gap | Mechanism | Current result / evidence |
|---|---|---|
| Default credentials | Do not ship a packaged administrator password. Use environment-specific setup and refuse production startup when required secrets are absent. | No deployed configuration exists to test yet; this must be checked before deployment. |
| Unauthenticated endpoints | Authentication will be required for connection, search, indexing, and deletion routes. Each request must also enforce source/workspace authorization; unauthenticated requests should return 401 and cross-source requests should return 403. | No application routes are currently committed, so endpoint tests are not yet applicable. |
| Secrets in source control | Keep tokens in server-side environment variables or a managed secret store; ignore `.env*` except documented example templates; rotate any exposed credential. | Team-reported targeted full-history pattern search on November 11, 2026 returned zero matches for the searched patterns. This was a limited pattern-based check, not proof that no secret could exist. |

**Authentication and transport targets.** Prefer provider-managed OAuth with narrowly scoped read permissions. Where short-lived tokens are available, use them; otherwise honor the provider's actual expiry and revocation model rather than inventing one. If Spotlight introduces its own authenticated web session, the current design target is a maximum 60-minute session with Secure, HttpOnly, SameSite cookies. Externally hosted traffic must use HTTPS. Secrets remain server-side. Search endpoints should initially be rate-limited to approximately 60 authenticated requests per user per minute and adjusted after testing. Logs should redact authorization headers, tokens, and private query text.

**Git-history secret search performed.** On November 11, 2026, Rohan Shetty reported running the following in the Spotlight Git repository:

```powershell
git log --all --oneline
git log --all -p | Select-String -Pattern `
'ghp_','github_pat_','sk-','AKIA','password\s*=','api[_-]?key\s*=','secret\s*='
```

The second command produced zero matches for those strings/patterns. This checks available Git history for a limited set of common credential forms, but differently formatted secrets could evade it. No exposed credential or remediation action was reported from this check. Before deployment, the team should additionally run a dedicated history scanner such as Gitleaks and inspect history for `.env` files and private-key filenames.

| Self-audit question | Answer as of November 11, 2026 |
|---|---|
| Are any default credentials enabled? | No deployed configuration exists yet. Production deployment is blocked until this is checked. |
| Can anonymous users reach search or indexing routes? | No routes are implemented in the repository yet. When implemented, automated tests must verify 401 responses. |
| Can one user retrieve another user's repositories/projects? | Not yet testable. Authorization tests must use at least two distinct accounts/sources and verify 403 responses. |
| Are credentials and private query contents absent from logs? | No application logging middleware exists yet. Sample logs must be reviewed after implementation. |
| Was Git history searched for secrets? | Yes. A targeted pattern search was reported with zero matches; a dedicated scanner remains a pre-deployment follow-up. |
| Are HTTPS and rate limits enabled? | Not yet verifiable because there is no deployed application. Hosted configuration and 429 behavior must be tested before release. |

## 3. Licensing summary

As of November 11, 2026, the public Spotlight repository contains planning documents and team biographies but no application source tree, `package.json`, lockfile, `requirements.txt`, compiled dependencies, dataset, or asset folder. Therefore, there are currently no third-party software dependencies for which a package version or SPDX license can truthfully be listed. This section records the complete current inventory rather than assigning hypothetical licenses to tools that have not been adopted.

| Item / category | Version | License / SPDX | Permissive or copyleft | Obligation / decision |
|---|---|---|---|---|
| Spotlight team-authored planning documents and future original code | No release | No license selected; default copyright / all rights reserved | Neither | Do not assume redistribution rights. The team must choose an explicit project license before any release intended for reuse. |
| Git-provider and issue-tracker APIs under consideration | Providers not finalized | Provider API/contract terms, not an SPDX software license | Not classified | Review OAuth scopes, rate limits, caching/deletion rules, branding, and redistribution restrictions before integration. |
| Application dependencies | None introduced in current repository | Not applicable yet | Not applicable yet | When dependencies are added, inventory exact names and versions from manifests/lockfiles and verify SPDX identifiers and notice obligations. |
| Test data / repository examples | None introduced | Not applicable yet | Not applicable yet | Prefer team-created fixtures or explicitly authorized sample records; do not republish private source data. |
| Fonts, icons, images, adapted snippets | None introduced | Not applicable yet | Not applicable yet | Record creator/source, exact license, attribution requirements, and usage location when any asset is added. |

**Project license and compatibility.** Spotlight has not yet selected an open-source license. Until an explicit license file is adopted, team-authored work should be treated as all rights reserved. Because no third-party code dependencies are currently committed, there is no present software-license incompatibility to resolve. Once dependencies are introduced, the team will compare their licenses and notice obligations before selecting or finalizing Spotlight's own license.

**Copyleft decision.** No copyleft dependency is currently present because no application dependencies are committed. If GPL, LGPL, AGPL, or other copyleft material is later considered, the team will document the specific distribution, linking, or network-service obligations and decide whether to comply, replace the component, or change the project's licensing plan before release.

## 4. Attribution list

The repository currently contains team-authored planning material. No third-party library, copied tutorial implementation, external dataset, font, icon, or image is evidenced in the current repository. The team will maintain this section and duplicate the attribution list in `README.md` section 5 or a `NOTICE` file as implementation begins.

| Type | Source / author | URL / method | License / restrictions | Use / status |
|---|---|---|---|---|
| Spotlight project concept and planning material | Rohan Shetty and Shashwat Mishra | Team-authored project repository: https://github.com/roshet/Spotlight | Team-owned; project license undecided | Project definition, ethics statement, and supporting documentation |
| Third-party libraries | None currently introduced | Future manifests and lockfiles will be the source of record | Pending per-package SPDX verification | No installed usage evidenced in current repository |
| Adapted tutorial/repository code | None reported | Future adapted code must name the original repository/tutorial and commit or page | Source license must be checked before adaptation | No adaptation currently asserted |
| Datasets | None currently introduced | Future test data should be team-created or explicitly authorized | Usage restrictions must be documented | No imported dataset currently asserted |
| Icons, images, fonts | None currently introduced | Future asset folder/design files will identify source and author | Asset-specific license and attribution required | No third-party asset currently asserted |

For every dependency or borrowed component later added, the attribution record must include the canonical source URL, pinned version or commit, author, SPDX identifier where applicable, license obligations, and the location in Spotlight where it is used.

## 5. AI-use disclosure

> "Generative AI tools may be used for drafting and code assistance but not for the design decisions or written analysis that are assessed in this course."

The team used generative AI only for assistance that falls on the "Generally OK" side of the course framework. No AI tool was used as a substitute for the team's architecture, data model, product priorities, privacy/security decisions, or final design rationale.

### Generally OK

| Tool | Used by | Task | Column | How verified |
|---|---|---|---|---|
| ChatGPT | Rohan Shetty | Assisted with document organization, Markdown formatting, and preparation of the ethics-statement draft. | Generally OK - drafting / editing assistance | Checked against the assignment instructions and the team's actual project status; unsupported implementation claims were removed; final wording and factual claims require team review. |
| ChatGPT | Shashwat Mishra | Reviewed the ethics-statement draft against the Assignment 11 rubric; helped tighten wording, organize tables, and prepare the Markdown/DOCX/PDF submission files. | Generally OK - drafting / editing assistance | Compared claims with the current Spotlight repository and existing team decisions; retained explicit "not yet implemented" language where evidence was unavailable; team members review the final document before submission. |

### Not a substitute for assessed work

No AI use is disclosed in this category. The team did not delegate architecture selection, the data model, project priorities, threat/security decisions, licensing decisions, or the assessed design rationale to an AI tool.

**Team confirmation:** We acknowledge the AI uses disclosed above. We confirm that the final architecture, data model, priorities, design decisions, and design rationale are the team's own and that each member reviewed the final submission for factual accuracy.

Rohan Shetty - Signature: Rohan Shetty  Date: 11/11/2026

Shashwat Mishra - Signature: Shashwat Mishra  Date: 11/11/2026
