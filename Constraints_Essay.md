# Spotlight - Design Constraints Essay

Team Members: Shashwat Mishra and Rohan Shetty

## Economic

Spotlight is a two-student capstone project, so we propose a target of $0 in recurring infrastructure costs during prototype development. We will prioritize open-source libraries, local indexing, and free API allowances to avoid requiring paid hosting or AI subscriptions. Optional AI features will be deferred if their operating costs exceed the prototype budget.

## Professional

Our two-person team must deliver a working prototype within the Fall 2026 and Spring 2027 senior design sequence. The initial scope will prioritize unified search across Git repositories, commits, pull requests, and issue-tracking tickets rather than attempting to integrate every developer platform. This allows us to apply our full-stack development experience while reserving time for integration testing and documentation.

## Legal

Spotlight may index proprietary source code and internal engineering tickets, so testing will use public repositories or repositories for which the team has explicit authorization. Third-party integrations must comply with their API terms and applicable software licenses, including the GitHub Terms of Service if GitHub is integrated.

## Security

Because Spotlight handles potentially confidential engineering data, integrations will request only the read permissions needed for search and will keep authentication credentials out of source control. Indexed data will remain local by default, and any optional external AI processing of private content will require explicit user authorization. This creates a trade-off between security and professional development time because local indexing and secure credential storage require additional implementation effort, but we will prioritize these safeguards over adding more integrations.

