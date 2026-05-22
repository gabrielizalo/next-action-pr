# Next Action PR - Project Plan

## Project Summary

Next Action PR is an independent tool designed to generate automated Pull Request health reports for engineering teams.

The first version will be a CLI application that reads Pull Request data, classifies each PR by its current status and next action owner, and generates a Markdown report that can be copied into Confluence.

Later versions can publish directly to Confluence and run automatically using GitHub Actions.

---

## Core Idea

The goal is not to create another personal Pull Request dashboard.

The real value is generating a shared, action-oriented report that helps the team understand:

- Which PRs are ready to merge
- Which PRs are waiting for review
- Which PRs need developer updates
- Which PRs are old
- Who owns the next action

Main product idea:

```txt
GitHub PRs → Classification → Markdown Report → Confluence
```

First MVP:

```
Mock PRs → Classification → Markdown Report
```

Future version:

```
GitHub API → Classification → Markdown/HTML Report → Confluence
```

------

## Why This Idea Is Still Valuable

GitHub already has Pull Request views, and some browser extensions also provide PR dashboards.

However, those tools are mostly personal views or dashboards inside GitHub.

Next Action PR focuses on a different problem:

> Creating a clear, shared, Confluence-friendly report that helps the whole team understand who needs to take action next.

The key differentiator is:

```
Next Action Owner
```

Not just:

```
PR status
```

The report should clearly show whether the next action belongs to:

- Developer
- Reviewer
- Merger
- Team lead / owner
- Unknown

------

## Strategic Decision

Next Action PR should be built as an independent project, not inside the company repository.

Reasons:

- It can be published in my GitHub portfolio
- It can be presented as an independent initiative
- It avoids coupling with the company application
- It can be reused by other teams later
- It can evolve into a product or reusable GitHub Action

The company can use it through configuration, tokens, or GitHub Actions without the tool being part of the main application codebase.

------

## MVP Scope

### MVP 0.1

The first version should:

1. Use mock Pull Request data
2. Classify PRs into actionable categories
3. Generate a Markdown report
4. Save the report into a `/reports` folder
5. Allow the report to be copied and pasted manually into Confluence

No GitHub API integration yet.

No Confluence API integration yet.

------

## Initial Architecture

The project should follow this flow:

```
Data Source
↓
Classifier
↓
Report Generator
↓
Markdown File
```

### Data Source

Initial version:

```
Mock data
```

Future version:

```
GitHub API
```

### Classifier

Responsible for deciding:

- Category
- Reason
- Next action owner

### Report Generator

Responsible for converting classified PRs into a Confluence-friendly Markdown report.

------

## Recommended Project Structure

```
next-action-pr/
├── src/
│   ├── cli.ts
│   ├── config.ts
│   ├── types.ts
│   ├── data/
│   │   └── mockPullRequests.ts
│   ├── classifier/
│   │   └── classifyPullRequest.ts
│   └── report/
│       └── generateMarkdownReport.ts
├── reports/
├── .env.example
├── next-action-pr.config.example.json
├── package.json
├── tsconfig.json
└── README.md
```

Future folders:

```
src/github/
└── fetchPullRequests.ts

src/publishers/
└── confluencePublisher.ts
```

------

## Pull Request Categories

### READY_TO_MERGE

PRs that are approved, not draft, checks are passing, and there are no merge blockers.

Next action owner:

```
Merger
```

Example meaning:

```
This PR is ready. Someone with permissions should merge it.
```

------

### WAITING_FOR_REVIEW

PRs that are ready from the developer side but still need review or approval.

Next action owner:

```
Reviewer
```

Example meaning:

```
The developer has finished the work. The team needs to review it.
```

------

### WAITING_FOR_DEVELOPER

PRs that require action from the PR author.

Possible reasons:

- Draft PR
- Changes requested
- Failing checks
- Merge conflicts
- Branch behind

Next action owner:

```
Developer
```

Example meaning:

```
The PR author needs to update this PR before it can move forward.
```

------

### OLD

PRs that have been open for more than the configured stale threshold.

Default rule:

```
OLD: PR created more than 60 days ago
```

Next action owner:

```
Team Lead / PR Owner
```

Example meaning:

```
This PR has been open for too long and should be reviewed to decide whether it should be completed, merged, closed, or split into a smaller PR.
```

Typical actions:

- Confirm if the PR is still relevant
- Ask the author for an update
- Decide whether it should be closed
- Decide whether it should be split or recreated
- Prioritize review if it is still needed

---

### UNKNOWN

Fallback category for cases that do not clearly match any rule.

Next action owner:

```
Unknown
```

------

## Markdown Report Format

The report should be Confluence-friendly and easy to copy and paste.

Expected structure:

```
# Next Action PR Report

Generated: 2026-05-17 09:00

## Summary

| Category | Count |
|---|---:|
| Ready to Merge | 2 |
| Waiting for Review | 5 |
| Waiting for Developer | 3 |
| Old | 1 |
| Unknown | 1 |

## Ready to Merge

| PR | Title | Author | Age | Last Update |
|---|---|---|---|---|
| [#123](https://github.com/org/repo/pull/123) | Fix agency config | Alice | 2d | today |

## Waiting for Review

| PR | Title | Author | Reviewers | Age | Last Update |
|---|---|---|---|---|---|
| [#124](https://github.com/org/repo/pull/124) | Update audit filters | Bob | Dave, Mark | 4d | 4d |

## Waiting for Developer

| PR | Title | Author | Reason | Age | Last Update |
|---|---|---|---|---|---|
| [#125](https://github.com/org/repo/pull/125) | Fix mugbook upload | Charlie | Changes requested | 32d | 32d |

## Old

| PR | Title | Author | Current Status | Reason | Age | Last Update |
|---|---|---|---|---|---|---|
| [#100](https://github.com/org/repo/pull/100) | Legacy cleanup | Dana | Waiting for Review | Open for more than 60 days | 72d | 15d ago |

## Unknown

| PR | Title | Author | Current Status | Reason | Age | Last Update |
|---|---|---|---|---|---|---|
| [#101](https://github.com/org/repo/pull/101) | Investigate unclear PR state | Dana | Unknown | Unable to classify | 8d | 2d ago |
```



------

## Development Plan

The project should be built step by step.

The goal is to learn and keep control of the architecture instead of asking to generate the full application at once.

------

### Step 1: Create the Base Project

#### Goal

Create a clean Node.js + TypeScript CLI project.

#### Prompt

```
Create a new Node.js TypeScript CLI project called next-action-pr.

Requirements:
- Use pnpm.
- Use TypeScript.
- Add tsx to run TypeScript files directly.
- Add a basic tsconfig.json.
- Add a src/cli.ts file that prints "Next Action PR Report Generator".
- Add scripts:
  - "dev": "tsx src/cli.ts"
  - "report": "tsx src/cli.ts"
- Do not add GitHub API integration yet.
- Keep the project simple and minimal.
```

#### Manual Validation

```
pnpm install
pnpm dev
```

Expected output:

```
Next Action PR Report Generator
```

------

### Step 2: Create the Base Types

#### Goal

Define the internal Pull Request model.

#### Prompt

```
Add the initial TypeScript types for the Next Action PR project.

Create src/types.ts with:
- PullRequestReviewDecision
- PullRequestMergeState
- PullRequestCategory
- PullRequestItem
- ClassifiedPullRequest

The PullRequestItem should include:
- number
- title
- url
- author
- createdAt
- updatedAt
- isDraft
- reviewDecision
- mergeStateStatus
- requestedReviewers
- checksStatus

The ClassifiedPullRequest should extend PullRequestItem and include:
- category
- reason
- nextActionOwner

Do not implement any logic yet.
```

#### Manual Validation

```
pnpm tsc --noEmit
```

------

### Step 3: Create Mock Data

#### Goal

Allow development without a GitHub token.

#### Prompt

```
Create mock Pull Request data for development.

Add src/data/mockPullRequests.ts exporting an array of PullRequestItem.

Include at least 10 PRs covering these scenarios:
- ready to merge
- waiting for review
- changes requested
- failing checks
- branch behind
- draft PR
- old PR over 60 days
- inactive PR with no updates for over 15 days

Use realistic titles and authors.
Do not connect to GitHub yet.
```

#### Manual Validation

Update `src/cli.ts` temporarily to print:

```
Loaded 10 mock pull requests
```

Run:

```
pnpm report
```

Expected output:

```
Loaded 10 mock pull requests
```

------

### Step 4: Create the Pull Request Classifier

#### Goal

Convert raw PRs into actionable PRs.

#### Prompt

```
Implement the Pull Request classifier.

Create src/classifier/classifyPullRequest.ts.

It should export a function classifyPullRequest(pr: PullRequestItem, options?: { staleDays?: number }): ClassifiedPullRequest.

Rules:
- If the PR was created more than staleDays ago, category should be OLD, reason "Open for more than {staleDays} days", nextActionOwner "Team Lead / PR Owner".
- If the PR is draft, category should be WAITING_FOR_DEVELOPER, reason "Draft PR".
- If reviewDecision is APPROVED and mergeStateStatus is CLEAN and checksStatus is SUCCESS, category should be READY_TO_MERGE.
- If reviewDecision is CHANGES_REQUESTED, category should be WAITING_FOR_DEVELOPER, reason "Changes requested".
- If checksStatus is FAILURE, category should be WAITING_FOR_DEVELOPER, reason "Checks failing".
- If mergeStateStatus is DIRTY, category should be WAITING_FOR_DEVELOPER, reason "Merge conflicts".
- If mergeStateStatus is BEHIND, category should be WAITING_FOR_DEVELOPER, reason "Branch behind".
- If reviewDecision is REVIEW_REQUIRED, category should be WAITING_FOR_REVIEW.
- Include nextActionOwner as:
  - "Merger" for READY_TO_MERGE
  - "Reviewer" for WAITING_FOR_REVIEW
  - "Developer" for WAITING_FOR_DEVELOPER
  - "Team Lead / PR Owner" for OLD
  - "Unknown" otherwise

Keep the function pure and easy to test.
```

#### Manual Validation

Update `src/cli.ts` to classify the mock PRs and print counts:

```
Ready to merge: X
Waiting for review: Y
Waiting for developer: Z
Old: W
```

Run:

```
pnpm report
```

------

### Step 5: Generate the Markdown Report

#### Goal

Create a Markdown report that can be copied into Confluence.

#### Prompt

```
Create a Markdown report generator.

Create src/report/generateMarkdownReport.ts.

It should export generateMarkdownReport(items: ClassifiedPullRequest[], options?: { projectName?: string }): string.

The report should include:
- Title: "{projectName} Pull Request Health Report"
- Generated timestamp
- Summary table
- Ready to Merge table
- Waiting for Review table
- Waiting for Developer table
- Old PRs table
- Unknown table

Each PR row should include:
- PR number as a Markdown link
- title
- author
- age in days
- last updated in days
- reason when applicable

Make sure the Markdown tables are Confluence-friendly.
```

#### Manual Validation

Print the generated Markdown in the console and verify that the tables look correct.

Run:

```
pnpm report
```

------

### Step 6: Save the Report to `/reports`

#### Goal

Generate a physical Markdown file.

#### Prompt

```
Update src/cli.ts so it:
- Loads mockPullRequests
- Classifies them
- Generates the Markdown report
- Creates a reports directory if it does not exist
- Writes the report to reports/next-action-pr-YYYYMMDD-HHMM.md
- Prints the output file path when finished

Do not add GitHub API yet.
```

#### Manual Validation

Run:

```
pnpm report
```

Expected output:

```
Report generated: reports/next-action-pr-YYYYMMDD-HHMM.md
```

Then open:

```
reports/next-action-pr-YYYYMMDD-HHMM.md
```

Copy the content and paste it into Confluence.

------

### Step 7: Add Configuration

#### Goal

Avoid hardcoded values.

#### Prompt

```
Add a JSON configuration file for Next Action PR.

Create next-action-pr.config.example.json with:
- projectName
- repository.owner
- repository.name
- report.staleDays
- report.includeDrafts

Create src/config.ts with a loadConfig function.
The function should:
- Look for next-action-pr.config.json in the project root
- If it does not exist, use sensible defaults
- Validate required fields when GitHub integration is enabled later
- Keep it simple for now

Update cli.ts to use projectName, staleDays from config.
```

#### Example Config

Create:

```
next-action-pr.config.json
```

With:

```
{
  "projectName": "Example Project",
  "repository": {
    "owner": "owner-example",
    "name": "repo-example"
  },
  "report": {
    "staleDays": 60,
    "includeDrafts": true
  }
}
```

Run:

```
pnpm report
```

------

### Step 8: Prepare GitHub API Integration

This step should wait until the GitHub token is approved.

#### Goal

Replace mock data with real GitHub Pull Requests.

#### Future Prompt

```
Add GitHub API integration using Octokit.

Create src/github/fetchPullRequests.ts.

It should:
- Read GITHUB_TOKEN from environment variables.
- Use repository owner and name from config.
- Fetch open pull requests from GitHub.
- Map GitHub PR data into our internal PullRequestItem type.
- Include number, title, url, author, createdAt, updatedAt, isDraft, requested reviewers, review decision if available, merge state if available, and basic checks status if available.
- Keep the GitHub-specific types isolated inside the github folder.
- Do not change the classifier or report generator.
```

------

## Why Mock Data First

The project is not blocked while waiting for the GitHub token.

Mock data allows us to build and validate:

- Architecture
- Data types
- Classification rules
- Markdown output
- Confluence copy/paste format
- CLI experience

When the token is available, only the data source changes:

```
mockPullRequests
```

Becomes:

```
fetchPullRequests()
```

The classifier and report generator should remain the same.

------

## Expected First Milestone

The first meaningful milestone is:

```
A CLI that generates a Confluence-friendly Markdown report from mock Pull Request data.
```

Command:

```
pnpm report
```

Output:

```
reports/next-action-pr-YYYYMMDD-HHMM.md
```

Usage:

```
Copy Markdown → Paste into Confluence
```

------

## Roadmap

```
MVP 0.1
CLI + mock data + Markdown report

MVP 0.2
Config file + better classifier

MVP 0.3
GitHub API integration

MVP 0.4
Real report from company repo

MVP 0.5
Confluence publisher

MVP 0.6
GitHub Action scheduled every 6 hours

MVP 1.0
Reusable GitHub Action / public portfolio-ready project
```

------

## Final Decision

Start now, but not with GitHub API yet.

Start with:

```
Project setup
Types
Mock data
Classifier
Markdown report
Configuration
```

Then, when the token is approved:

```
GitHub API integration
```

Then:

```
Confluence publishing
```

Then:

```
GitHub Action automation
```

This keeps the project controlled, understandable, and portfolio-ready.



