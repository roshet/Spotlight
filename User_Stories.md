# User Stories and Use Cases — Spotlight

**Team:** Shashwat Mishra, Rohan Shetty

## Stakeholder Map

| Category | Stakeholder | Why they are in this category |
|---|---|---|
| Primary | Software Developer | Directly uses Spotlight to search for and retrieve technical information distributed across development platforms. |
| Secondary | Engineering Team Lead | Uses engineering context to understand development activity and identify relationships between work items and code changes. |
| Secondary | Integration Maintainer | Responsible for maintaining connections with development platforms and troubleshooting data synchronization issues. |
| Hidden | Repository Owner / IT Security Administrator | May not directly use Spotlight but is responsible for protecting confidential source code and engineering information from unauthorized access. |

## User Stories

**US-01 (Primary)**

As a software developer working across multiple development platforms,  
I want to search for technical information across my connected Git repositories, pull requests, and issue-tracking systems from a single application,  
so that I can spend less time switching between platforms to find relevant engineering context.

**US-02 (Primary)**

As a software developer investigating a technical issue,  
I want to access the original source of a search result,  
so that I can verify the retrieved information and continue my work within the appropriate development platform.

**US-03 (Secondary)**

As an engineering team lead reviewing development activity,  
I want to identify commits and pull requests associated with a specific issue or work item,  
so that I can understand the implementation progress and technical context behind that work item.

**US-04 (Secondary)**

As an integration maintainer responsible for connected development platforms,  
I want to identify when a data synchronization operation fails and understand the reason for the failure,  
so that I can resolve integration issues and maintain an up-to-date search index.

**US-05 (Hidden — Security and Compliance)**

As a repository owner responsible for protecting confidential engineering information,  
I want Spotlight to restrict the retrieval of private repository data to authorized sources and users,  
so that proprietary source code and engineering information are not exposed to unauthorized individuals.

## INVEST Self-Check

I = Independent, N = Negotiable, V = Valuable, E = Estimable, S = Small, T = Testable.

| ID | I | N | V | E | S | T | Notes |
|---|---|---|---|---|---|---|---|
| US-01 | Y | Y | Y | Y | Y | Y | Focuses on retrieving engineering information through unified search without prescribing a particular user interface or search algorithm. |
| US-02 | Y | Y | Y | Y | Y | Y | Separates source navigation from search functionality and can be tested independently using existing search records. |
| US-03 | Y | Y | Y | Y | Y | Y | Limits relationship discovery to explicit references between issues, commits, and pull requests rather than requiring automatic inference of all engineering relationships. |
| US-04 | Y | Y | Y | Y | Y | Y | Focuses on detecting and communicating synchronization failures rather than implementing automated recovery for every possible integration error. |
| US-05 | Y | Y | Y | Y | Y | Y | Focuses on authorized data retrieval and can be tested using permitted and restricted repositories without requiring an enterprise-wide security platform. |

## Use Cases

### UC-01: Search Engineering Context

**Expands:** US-01

**Primary Actor:** Software Developer

**Secondary Actors:** Spotlight System, Local Search Index, Connected Development Platforms

**Preconditions:**

1. Spotlight is installed and running on the developer's computer.
2. At least two supported development data sources have been connected to Spotlight.
3. The connected sources have been successfully indexed at least once.
4. The developer has permission to access the indexed engineering information.

**Main Success Flow:**

1. **Actor:** Submits a search query containing a keyword, issue identifier, or technical term.
2. **System:** Validates the search query and retrieves matching records from the local search index.
3. **Actor:** Reviews the initial search results and optionally specifies a source or record type to narrow the results.
4. **System:** Applies the selected filters and ranks matching records based on their relevance to the search query.
5. **Actor:** Selects a relevant search result to obtain additional technical context.
6. **System:** Displays the available information associated with the selected record, including its title, source, record type, and original source reference.
7. **Actor:** Requests to navigate to the original record.
8. **System:** Opens the corresponding record in its original development platform or local repository.

**Alternate Flow (No Matching Results):**

1. At step 2, the system determines that no indexed records match the submitted search query.
2. The system informs the developer that no matching results were found.
3. The developer modifies the query or removes existing filters.
4. The system executes the updated search request.

**Exception Flow (Search Index Unavailable):**

1. At step 2, the system detects that the local search index is unavailable or cannot be accessed.
2. The system terminates the search operation and displays an error identifying the search index as unavailable.
3. The system logs the error and informs the developer that the index may need to be rebuilt or synchronized.
4. Previously connected data sources and integration configurations remain unchanged.

**Postcondition:**

The developer has retrieved relevant engineering information from the indexed development sources and can access the corresponding original records. The search operation does not modify any source repository, pull request, or issue-tracking record.

**Acceptance Criteria:**

**AC-01.1 (Main Success Flow)**

Given a local search index containing up to 10,000 engineering records from at least two connected development sources, with known records containing a specific search keyword,  
When the developer submits a query containing that keyword,  
Then the system returns the matching records within 2 seconds, with each result displaying its title, source, record type, and original source reference.

**AC-01.2 (Alternate Flow)**

Given a functioning search index that contains no records matching a particular search query,  
When the developer submits that query,  
Then the system returns zero results and displays a message indicating that no matching records were found within 2 seconds.

**AC-01.3 (Exception Flow)**

Given that the local search index is unavailable,  
When the developer submits a search query,  
Then the system displays an error identifying the unavailable search index within 5 seconds, records the failure in an application log, and leaves existing integration configurations unchanged.

### UC-02: Synchronize a Connected Development Source

**Expands:** US-04

**Primary Actor:** Integration Maintainer

**Secondary Actors:** Spotlight System, External Development Platform API, Local Search Index

**Preconditions:**

1. At least one supported development platform has been configured as a data source in Spotlight.
2. The integration has stored authentication credentials where required.
3. The local search index is available for reading and writing.
4. An internet connection is available for communicating with the external development platform.

**Main Success Flow:**

1. **Actor:** Initiates a synchronization operation for a connected development source.
2. **System:** Validates the integration configuration and authenticates with the external development platform.
3. **System:** Retrieves available engineering records, such as commits, pull requests, and issues.
4. **System:** Converts retrieved records into a standardized internal representation and inserts or updates the corresponding records in the local search index.
5. **Actor:** Requests the synchronization status of the connected source.
6. **System:** Displays the synchronization outcome, completion timestamp, and number of records processed.
7. **Actor:** Submits a search query to verify that newly synchronized records are available.
8. **System:** Returns matching records from the updated search index.

**Alternate Flow (No New Records):**

1. At step 3, the system determines that the connected development source contains no new or modified records since the previous successful synchronization.
2. The system completes the synchronization without creating duplicate records.
3. The system updates the synchronization timestamp and reports that zero records were added or updated.

**Exception Flow (Invalid or Expired Credentials):**

1. At step 2, the external development platform rejects the authentication attempt because the supplied credentials are invalid or expired.
2. The system stops the synchronization operation for the affected source.
3. The system records the synchronization failure and identifies authentication as the cause.
4. The system preserves previously indexed records without adding or modifying records from the failed synchronization.
5. The integration maintainer is informed that the authentication credentials must be updated before another synchronization attempt.

**Postcondition:**

The local search index contains the successfully retrieved engineering information, and the connected source has an updated synchronization status and completion timestamp. If synchronization fails, the failure is recorded and previously indexed data remains unchanged.

**Acceptance Criteria:**

**AC-02.1 (Main Success Flow)**

Given a configured development source with valid authentication credentials and 100 new or modified engineering records, with the external API available and responding normally,  
When the integration maintainer initiates synchronization,  
Then the system processes all 100 records within 60 seconds, creates no duplicate records, updates the last successful synchronization timestamp, and makes the synchronized records available through search.

**AC-02.2 (Alternate Flow)**

Given a connected development source with no new or modified records since the previous successful synchronization,  
When the integration maintainer initiates another synchronization,  
Then the system reports a successful synchronization with zero added or updated records and creates no duplicate records.

**AC-02.3 (Exception Flow)**

Given a connected development source with expired or invalid authentication credentials,  
When the external development platform returns an authentication error during synchronization,  
Then the system marks the synchronization as failed, identifies authentication as the cause within 5 seconds of receiving the error, and adds, modifies, or deletes zero existing indexed records.
