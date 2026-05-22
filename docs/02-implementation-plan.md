# Plan-Orchestrate Result

**Plan**: `docs/01-project-plan.md`
**Lang**: `typescript` *(inferred from plan text — greenfield project, no file markers yet)*
**ECC mode**: `legacy`
**Steps**: 8
**Scope**: all

---

## Steps Overview

| # | Title | Tags | Chain |
|---|---|---|---|
| 1 | Create the Base Project | `impl` | `tdd-guide,typescript-reviewer` |
| 2 | Create the Base Types | `impl` | `tdd-guide,typescript-reviewer` |
| 3 | Create Mock Data | `impl` | `tdd-guide,typescript-reviewer` |
| 4 | Create the Pull Request Classifier | `impl` | `tdd-guide,typescript-reviewer` |
| 5 | Generate the Markdown Report | `impl` | `tdd-guide,typescript-reviewer` |
| 6 | Save the Report to /reports | `impl` | `tdd-guide,typescript-reviewer` |
| 7 | Add Configuration | `impl` | `tdd-guide,typescript-reviewer` |
| 8 | Prepare GitHub API Integration | `impl, security` | `tdd-guide,typescript-reviewer,security-reviewer` |

---

## Step 1 — Create the Base Project

**Intent**: Scaffold a minimal Node.js + TypeScript CLI project using pnpm and tsx. No external APIs. The only output is `src/cli.ts` printing a known string.
**Tags**: `impl`
**Chain rationale**: Pure scaffold step. `tdd-guide` writes tests first then implements; `typescript-reviewer` closes for TS idioms, tsconfig correctness, and script definitions.

```bash
/orchestrate custom "tdd-guide,typescript-reviewer" "[Plan: docs/01-project-plan.md#step-1] Scaffold Node.js + TypeScript CLI project next-action-pr using pnpm and tsx; create src/cli.ts printing \"Next Action PR Report Generator\"; add dev and report pnpm scripts; minimal tsconfig.json; no GitHub or Confluence API; Acceptance: pnpm install succeeds; pnpm dev prints expected string; pnpm tsc --noEmit exits clean"
```

---

## Step 2 — Create the Base Types

**Intent**: Define all internal TypeScript types for the PR model — literal union types and two interfaces (`PullRequestItem`, `ClassifiedPullRequest`). No logic implemented yet.
**Tags**: `impl`
**Chain rationale**: Type-definition step with strict structural requirements. `tdd-guide` writes type-guard tests; `typescript-reviewer` validates literal unions, interface extension, and strict null handling.

```bash
/orchestrate custom "tdd-guide,typescript-reviewer" "[Plan: docs/01-project-plan.md#step-2] Create src/types.ts with literal union types PullRequestReviewDecision/PullRequestMergeState/PullRequestChecksStatus/PullRequestCategory/NextActionOwner; PullRequestItem with 11 fields; ClassifiedPullRequest extending PullRequestItem adding category/reason/nextActionOwner/isStale/underlyingCategory/underlyingReason; no logic; Acceptance: pnpm tsc --noEmit clean; all fields match spec exactly; ClassifiedPullRequest extends PullRequestItem"
```

---

## Step 3 — Create Mock Data

**Intent**: Create a static array of 10+ `PullRequestItem` fixtures covering all classification scenarios using fixed ISO date strings — not computed from `Date.now()` — so tests stay deterministic.
**Tags**: `impl`
**Chain rationale**: Data fixture step. `tdd-guide` validates all 8 required scenarios are representable; `typescript-reviewer` checks type conformance and date format.

```bash
/orchestrate custom "tdd-guide,typescript-reviewer" "[Plan: docs/01-project-plan.md#step-3] Create src/data/mockPullRequests.ts exporting 10+ PullRequestItem covering ready-to-merge, waiting-review, changes-requested, failing-checks, branch-behind, draft, stale (createdAt >60 days), and inactive scenarios; all dates fixed ISO strings not computed from current date; Acceptance: pnpm tsc --noEmit clean; pnpm report prints \"Loaded 10 mock pull requests\"; all 8 scenarios present in data"
```

---

## Step 4 — Create the Pull Request Classifier

**Intent**: Implement a pure `classifyPullRequest(pr, options?)` applying an 8-rule base-category precedence chain, staleness overlay, and `nextActionOwner` derivation. Clock access only via `options.now`.
**Tags**: `impl`
**Chain rationale**: Core logic step with deterministic rules — high TDD value. `tdd-guide` drives all 8 base rules + staleness overlay + owner derivation before implementation; `typescript-reviewer` validates purity, no side-effects, strict typing.

```bash
/orchestrate custom "tdd-guide,typescript-reviewer" "[Plan: docs/01-project-plan.md#step-4] Implement pure classifyPullRequest(pr, options?) in src/classifier/classifyPullRequest.ts; apply 8-rule base-category precedence (draft→changesRequested→failingChecks→dirty→behind→approvedClean→reviewRequired→unknown); staleness overlay using options.now; derive nextActionOwner from final category; never call Date.now() directly; Acceptance: unit tests cover all 8 base rules and staleness overlay; pnpm report prints correct counts; tsc --noEmit clean"
```

---

## Step 5 — Generate the Markdown Report

**Intent**: Implement `generateMarkdownReport(items, options?)` producing a Confluence-friendly Markdown string with title, timestamp, summary table, and five category sections. All time calculations injected via `options.now`.
**Tags**: `impl`
**Chain rationale**: Output-formatting step with structural requirements per section. `tdd-guide` snapshot-tests against fixed `options.now`; `typescript-reviewer` validates function signature, `underlyingCategory` rendering in Old table, and Markdown escaping.

```bash
/orchestrate custom "tdd-guide,typescript-reviewer" "[Plan: docs/01-project-plan.md#step-5] Implement generateMarkdownReport(items, options?) in src/report/generateMarkdownReport.ts; Confluence-friendly Markdown with title, generated timestamp, summary table, and 5 category sections; Old PRs table includes underlyingCategory as \"Current Status\" column; all age/timestamp calcs use options.now; Acceptance: output deterministic in snapshot tests with fixed options.now; all 5 sections render; tsc --noEmit clean"
```

---

## Step 6 — Save the Report to /reports

**Intent**: Wire `cli.ts` end-to-end: load mocks → classify → generate Markdown → create `reports/` if absent → write timestamped `.md` file → print output path.
**Tags**: `impl`
**Chain rationale**: CLI integration step connecting all prior modules. `tdd-guide` tests the file-write path with a temp directory; `typescript-reviewer` validates `fs` usage, timestamp formatting, and import wiring.

```bash
/orchestrate custom "tdd-guide,typescript-reviewer" "[Plan: docs/01-project-plan.md#step-6] Update src/cli.ts to load mockPullRequests, classify all PRs, generate Markdown report, create reports/ directory if absent, write reports/next-action-pr-YYYYMMDD-HHMM.md, and print the output path; no GitHub API; Acceptance: pnpm report creates file under reports/ with correct filename pattern; file contains valid Markdown tables; missing reports/ dir is created automatically"
```

---

## Step 7 — Add Configuration

**Intent**: Introduce `src/config.ts` with `loadConfig()` reading `next-action-pr.config.json` from project root, falling back to sensible defaults. Wire `projectName` and `staleDays` into `cli.ts`.
**Tags**: `impl`
**Chain rationale**: Config layer step. `tdd-guide` tests default fallback and override paths; `typescript-reviewer` validates JSON schema inference, optional chaining on missing fields, no new coupling into classifier or reporter.

```bash
/orchestrate custom "tdd-guide,typescript-reviewer" "[Plan: docs/01-project-plan.md#step-7] Create next-action-pr.config.example.json with projectName/repository/report fields; implement src/config.ts loadConfig() reading next-action-pr.config.json with sensible defaults when file absent; update cli.ts to consume projectName and staleDays from config; Acceptance: pnpm report uses config values; absent config.json falls back to defaults without error; tsc --noEmit clean"
```

---

## Step 8 — Prepare GitHub API Integration

**Intent**: Create `src/github/fetchPullRequests.ts` using Octokit. `GITHUB_TOKEN` read from env. GitHub types isolated in `src/github/`. Classifier and report generator untouched.
**Tags**: `impl, security`
**Chain rationale**: `impl + security` — GITHUB_TOKEN is a secret from env. `tdd-guide` tests token-absent error path and type mapping; `typescript-reviewer` validates Octokit usage and isolation boundary; `security-reviewer` closes the chain, auditing token handling and absence of token leakage into logs or report output.

```bash
/orchestrate custom "tdd-guide,typescript-reviewer,security-reviewer" "[Plan: docs/01-project-plan.md#step-8] Create src/github/fetchPullRequests.ts using Octokit; read GITHUB_TOKEN from env; fetch open PRs and map response to PullRequestItem; keep GitHub-specific types isolated in src/github/; do not modify classifier or report generator; Acceptance: absent GITHUB_TOKEN throws descriptive error without leaking value; returned items satisfy PullRequestItem type; classifier and reporter unit tests unchanged"
```

---

## Batch Execution

```bash
/orchestrate custom "tdd-guide,typescript-reviewer" "[Plan: docs/01-project-plan.md#step-1] Scaffold Node.js + TypeScript CLI project next-action-pr using pnpm and tsx; create src/cli.ts printing \"Next Action PR Report Generator\"; add dev and report pnpm scripts; minimal tsconfig.json; no GitHub or Confluence API; Acceptance: pnpm install succeeds; pnpm dev prints expected string; pnpm tsc --noEmit exits clean"

/orchestrate custom "tdd-guide,typescript-reviewer" "[Plan: docs/01-project-plan.md#step-2] Create src/types.ts with literal union types PullRequestReviewDecision/PullRequestMergeState/PullRequestChecksStatus/PullRequestCategory/NextActionOwner; PullRequestItem with 11 fields; ClassifiedPullRequest extending PullRequestItem adding category/reason/nextActionOwner/isStale/underlyingCategory/underlyingReason; no logic; Acceptance: pnpm tsc --noEmit clean; all fields match spec exactly; ClassifiedPullRequest extends PullRequestItem"

/orchestrate custom "tdd-guide,typescript-reviewer" "[Plan: docs/01-project-plan.md#step-3] Create src/data/mockPullRequests.ts exporting 10+ PullRequestItem covering ready-to-merge, waiting-review, changes-requested, failing-checks, branch-behind, draft, stale (createdAt >60 days), and inactive scenarios; all dates fixed ISO strings not computed from current date; Acceptance: pnpm tsc --noEmit clean; pnpm report prints \"Loaded 10 mock pull requests\"; all 8 scenarios present in data"

/orchestrate custom "tdd-guide,typescript-reviewer" "[Plan: docs/01-project-plan.md#step-4] Implement pure classifyPullRequest(pr, options?) in src/classifier/classifyPullRequest.ts; apply 8-rule base-category precedence (draft→changesRequested→failingChecks→dirty→behind→approvedClean→reviewRequired→unknown); staleness overlay using options.now; derive nextActionOwner from final category; never call Date.now() directly; Acceptance: unit tests cover all 8 base rules and staleness overlay; pnpm report prints correct counts; tsc --noEmit clean"

/orchestrate custom "tdd-guide,typescript-reviewer" "[Plan: docs/01-project-plan.md#step-5] Implement generateMarkdownReport(items, options?) in src/report/generateMarkdownReport.ts; Confluence-friendly Markdown with title, generated timestamp, summary table, and 5 category sections; Old PRs table includes underlyingCategory as \"Current Status\" column; all age/timestamp calcs use options.now; Acceptance: output deterministic in snapshot tests with fixed options.now; all 5 sections render; tsc --noEmit clean"

/orchestrate custom "tdd-guide,typescript-reviewer" "[Plan: docs/01-project-plan.md#step-6] Update src/cli.ts to load mockPullRequests, classify all PRs, generate Markdown report, create reports/ directory if absent, write reports/next-action-pr-YYYYMMDD-HHMM.md, and print the output path; no GitHub API; Acceptance: pnpm report creates file under reports/ with correct filename pattern; file contains valid Markdown tables; missing reports/ dir is created automatically"

/orchestrate custom "tdd-guide,typescript-reviewer" "[Plan: docs/01-project-plan.md#step-7] Create next-action-pr.config.example.json with projectName/repository/report fields; implement src/config.ts loadConfig() reading next-action-pr.config.json with sensible defaults when file absent; update cli.ts to consume projectName and staleDays from config; Acceptance: pnpm report uses config values; absent config.json falls back to defaults without error; tsc --noEmit clean"

/orchestrate custom "tdd-guide,typescript-reviewer,security-reviewer" "[Plan: docs/01-project-plan.md#step-8] Create src/github/fetchPullRequests.ts using Octokit; read GITHUB_TOKEN from env; fetch open PRs and map response to PullRequestItem; keep GitHub-specific types isolated in src/github/; do not modify classifier or report generator; Acceptance: absent GITHUB_TOKEN throws descriptive error without leaking value; returned items satisfy PullRequestItem type; classifier and reporter unit tests unchanged"
```

---

> **Note**: Step 8 waits for GitHub token approval. Steps 1–7 run immediately.
