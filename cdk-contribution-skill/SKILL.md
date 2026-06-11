---
name: cdk-contribution-skill
description: Guide AWS CDK contributions from GitHub issue analysis to PR-ready code. Use when contributing to aws-cdk repository, analyzing CDK issues, implementing fixes, or preparing pull requests.
---

# CDK Contribution Workflow

Orchestrates specialized phases for AWS CDK contributions with human approval gates.

## MANDATORY: Present Workflow Overview FIRST

BEFORE doing ANY work, you **MUST** present the ASCII workflow diagram below, explain the process,
and wait for confirmation — because the user needs visibility into the multi-phase process and
an opportunity to opt out before any work begins.

Display this to the user:

```text
CDK Contribution Workflow for Issue #<NUMBER>

I'll guide you through a structured process with human approval gates:

+------------------------------------------+
|           MAIN ORCHESTRATOR              |
|         (Me - Coordinates work)          |
+--------------------+---------------------+
                     |
     +---------------+--------------+
     |       PHASE 1: ANALYSIS      |
     +---------------+--------------+
                     |
                     v
     +-------------------------------+
     |        ISSUE ANALYST          |
     |  Analyze issue, classify      |
     |  type, explore code           |
     +---------------+---------------+
                     |
     +---------------+--------------+
     |       PHASE 2: PLANNING      |
     +---------------+--------------+
                     |
                     v
     +-------------------------------+
     |      SOLUTION ARCHITECT       |
     |  Propose solution, tests,     |
     |  and impl approach            |
     +---------------+---------------+
                     |
                     v
     +-------------------------------+
     |      YOUR APPROVAL            |
     |  Issue at a glance (ASCII)    |
     |  Proposed solution (ASCII)    |
     |  Unit + integ test plan       |
     |                               |
     |  Continue? Yes | No |         |
     |             Something Else    |
     +---------------+---------------+
               [YES] |
     +---------------+--------------+
     |    PHASE 3: BUILD & IMPL     |
     +---------------+--------------+
                     |
                     v
     +-------------------------------+
     |   BUILD SETUP + IMPLEMENT     |
     |   Branch, env, write code     |
     +---------------+---------------+
                     |
     +---------------+--------------+
     |   PHASE 4: PARALLEL VALID    |
     +-------+-------+-------+------+
             |       |       |
             v       v       v
         +------+ +-----+ +------+
         | TEST | |  QA | | DOCS |
         +--+---+ +--+--+ +--+---+
            +-------+-------+
                    |
     +--------------+--------------+
     |     PHASE 5: SELF REVIEW    |
     +-------+---------------------+
             |             |
             v             v
     +------------+  +------------+
     |  SECURITY  |  | REGRESSION |
     |   REVIEW   |  |   REVIEW   |
     +------+-----+  +-----+------+
            +----------+---+
                       |
                       v
            +--------------------+
            |    SYNTHESIZE      |
            |   REVIEW REPORT    |
            +--------+-----------+
                     |
                     v
     +-------------------------------+
     |       REVIEW FINDINGS         |
     |   Submit PR or fix issues     |
     +-------------------------------+

Key Points:
- Phase 1 (Analysis) runs first, then Phase 2 (Planning) runs after — sequential, not parallel
- You review and approve the plan before any code is written
- I pause for your input at critical decision points

Should we start? Yes | No
```

STOP and wait for user response. You **MUST NOT** proceed until the user confirms — because the user needs to understand the full workflow scope before committing to it.

---

## Reference Files

**CRITICAL**: The reference files in the `references/` folder define the exact structure and content
of each deliverable per phase. You **MUST** read the relevant reference file BEFORE executing each phase:

| SOP | Phase | Purpose |
|-----|-------|---------|
| `issue-analyst-sop.md` | 1 – Analysis | Issue analysis and classification |
| `solution-architect-sop.md` | 2 – Planning | Solution design and planning |
| `build-engineer-sop.md` | 3 – Build & Impl | Build environment setup |
| `implementation-specialist-sop.md` | 3 – Build & Impl | Code implementation |
| `test-engineer-sop.md` | 4 – Validation | Unit and integration testing |
| `quality-assurance-sop.md` | 4 – Validation | Lint, build, and quality checks |
| `documentation-specialist-sop.md` | 4 – Validation | README and docs updates |
| `security-reviewer-sop.md` | 5 – Self Review | Security review |
| `regression-reviewer-sop.md` | 5 – Self Review | Regression review |
| `review-report-generator-sop.md` | 5 – Self Review | Final review report synthesis |

Supplementary references: `debug-ci.md` (use when debugging GitHub Actions CI failures)

Refer to the **Quick Reference — Commands** table in `AGENTS.md` at the aws-cdk repo root for exact commands.

## Deliverables

Each phase **MUST** write its output to markdown files under `.contributions/<ISSUE_NUMBER>/`:

| Phase   | Output file                                                    |
|---------|----------------------------------------------------------------|
| Phase 1 | `.contributions/<ISSUE_NUMBER>/01-analysis.md`            |
| Phase 2 | `.contributions/<ISSUE_NUMBER>/02-solution.md`            |
| Phase 3 | `.contributions/<ISSUE_NUMBER>/03-build.md`               |
| Phase 4 | `.contributions/<ISSUE_NUMBER>/test-results.md`           |
| Phase 4 | `.contributions/<ISSUE_NUMBER>/quality-validation.md`     |
| Phase 4 | `.contributions/<ISSUE_NUMBER>/pr.md`                     |
| Phase 4 | `.contributions/<ISSUE_NUMBER>/04-validation.md`          |
| Phase 5 | `.contributions/<ISSUE_NUMBER>/security-review.md`        |
| Phase 5 | `.contributions/<ISSUE_NUMBER>/regression-review.md`      |
| Phase 5 | `.contributions/<ISSUE_NUMBER>/05-review.md`              |

Each phase is responsible for writing its own deliverable file(s) before completing.
The orchestrator reads the file as the handoff input to the next phase.

### Deliverable ASCII Diagram Requirement

Every deliverable markdown file MUST include at least one ASCII diagram that visually summarises
the key findings or structure of that phase. Examples by phase:

- Phase 1: module/file dependency map or issue impact diagram
- Phase 2: solution architecture diagram showing files to change and their relationships
- Phase 3: branch/build setup flow
- Phase 4: test coverage map (unit vs integ, pass/fail)
- Phase 5: review findings summary (security + regression side by side)

Use plain ASCII box-and-arrow style. No emoji. Example:

```text
  +----------------+       +------------------+
  |  affected.ts   | ----> |  test/foo.test.ts |
  +----------------+       +------------------+
         |
         v
  +----------------+
  |  features.ts   |  (new feature flag)
  +----------------+
```

---

## Phase Execution Instructions

### When user says "Yes" to start:

IMPORTANT: Phase 1 and Phase 2 are SEQUENTIAL — Phase 2 depends on Phase 1's output.
You **MUST NOT** run them in parallel — because Phase 2 requires the analysis deliverable from Phase 1.

**Step A — Execute Phase 1 (Issue Analysis):**

> **MANDATORY PRE-READ: Before executing this phase, you MUST read the instructions in `references/issue-analyst-sop.md`**
> **Do NOT proceed until you have read the file and understand the required deliverables.**
>
> Analyze the GitHub issue at <ISSUE_URL>.
>
> To fetch issue data, use the following priority order:
>
> 1. PRIMARY - gh CLI (preferred):
>    - `gh issue view <NUMBER> --repo aws/aws-cdk --json number,title,body,labels,comments,state`
>    - `gh issue view <NUMBER> --repo aws/aws-cdk --comments` for full comment thread
>    - `gh pr list --repo aws/aws-cdk --search "<issue number>" --json number,title,url` for linked PRs
>
> 2. FALLBACK - GitHub MCP server (only if gh CLI is unavailable or fails):
>    - Use GitHub MCP tools with perPage: 3 to avoid token overflow
>
> Then explore the aws-cdk codebase to understand the affected module(s), existing patterns, test
> conventions, and any related code. Classify the issue type (bug, feature, docs, etc.).
> Produce a structured analysis: issue summary, affected files/modules, root cause or gap,
> relevant CDK patterns observed, and any constraints or risks.
>
> Write your full analysis to `.contributions/<ISSUE_NUMBER>/01-analysis.md` before returning.

**Step B — WAIT for Phase 1 to complete, then read its output:**

Read `.contributions/<ISSUE_NUMBER>/01-analysis.md` to confirm it was written successfully.

**Step C — Only AFTER Phase 1 is complete, execute Phase 2 (Solution Planning):**

Pass the content of `01-analysis.md` as context to Phase 2:

> **MANDATORY PRE-READ: Before executing this phase, you MUST read the instructions in `references/solution-architect-sop.md`**
> **Do NOT proceed until you have read the file and understand the required deliverables.**
>
> Using the analysis provided in the context, propose a concrete solution for the CDK
> contribution. Include: the implementation approach, which files need to change and how,
> required unit tests (what to test and why), required integ tests (which integ test file,
> what scenario to cover), and any breaking change considerations. Be specific about CDK
> patterns to follow.
>
> Write your full proposal to `.contributions/<ISSUE_NUMBER>/02-solution.md` before returning.

You **MUST NOT** ask the user for input between Phase 1 and Phase 2 — because the analysis-to-planning handoff is fully automated and interrupting it adds no value.

---

### After both phases complete, present this proposal to the user:

```
ISSUE AT A GLANCE
=================

Issue:   #<NUMBER> - <TITLE>
Type:    <bug | feature | docs | ...>
Module:  <e.g. aws-lambda, aws-s3, ...>
Impact:  <one line>

Affected files:
  - <file 1>
  - <file 2>
  - ...

Root cause / gap:
  <2-3 sentence summary>

Constraints / risks:
  - <item>
  - ...


PROPOSED SOLUTION
=================

Approach:
  <concise description of the fix or feature>

Files to change:
  +---------------------------+----------------------------------+
  | File                      | Change                           |
  +---------------------------+----------------------------------+
  | <path/to/file.ts>         | <what changes>                   |
  | <path/to/other.ts>        | <what changes>                   |
  +---------------------------+----------------------------------+

Unit tests:
  File: <test file path>
  +------------------------------------------+
  | Test case                                |
  +------------------------------------------+
  | <describe what is tested>                |
  | <describe what is tested>                |
  +------------------------------------------+

Integ tests:
  File: <integ test file path>
  Scenario: <what the integ test exercises>
  Snapshot update required: Yes | No

Breaking changes: Yes | No
  <if yes, explain>


Continue with this plan? Yes | No | Something Else
```

---

## Phases 3-5 (after plan approval)

### Phase 3: Build & Implementation

**STEP 0 — READ SOPs (non-negotiable, do this FIRST):**

You **MUST** read BOTH of these files before ANY other action in this phase:
1. `references/build-engineer-sop.md` - provides instructions for the build phase
2. `references/implementation-specialist-sop.md` - provides instructions for the implementation phase

After reading, confirm you understand the required deliverables and procedures.

**STEP 1**

Before doing anything, present this summary to the user:

```text
PHASE 3: BUILD & IMPLEMENTATION
================================

Here's what I'm about to do:

  1. Create local branch   fix/issue-<NUMBER>-<slug>
  2. Sync with upstream    fetch and rebase onto upstream/main
  3. Assess build need     read 02-solution.md to decide if build is required
  4. Implement             apply all changes from the approved plan

Kicking off now...
```

Then proceed immediately without waiting for a response.

Steps (in order, no skipping) — see `references/build-engineer-sop.md` for exact commands:

1. Create a local PR branch named `fix/issue-<NUMBER>-<short-description>` from current HEAD
2. Sync with upstream main (add upstream remote if missing, then fetch and rebase)
3. Assess whether a build is required:
   - Read `02-solution.md` — if changes touch generated code, L1 constructs, or cross-package
     imports that require compilation, build the affected package(s)
   - If changes are purely in `.ts` source + tests with no codegen dependency, skip the build
     and go straight to implementation
   - Document the decision in `03-build.md`
4. Implement all changes from the approved plan in `02-solution.md`.
5. Write deliverable to `.contributions/<ISSUE_NUMBER>/03-build.md` (with ASCII diagram)

### Phase 4: Parallel Validation

**STEP 0 — READ SOPs (non-negotiable, do this FIRST):**

You **MUST** read ALL THREE of these files before ANY other action in this phase:
1. `references/test-engineer-sop.md` - provides instructions for the testing task. Required deliverable: `test-results.md`.
2. `references/quality-assurance-sop.md` - provides instructions for the QA task. Required deliverable: `quality-validation.md`
3. `references/documentation-specialist-sop.md` - provides instructions for the documentation task. Required deliverable: `pr.md`

After reading, confirm you understand the required deliverables and procedures.

**STEP 1**

Before doing anything, present this summary to the user:

```text
PHASE 4: PARALLEL VALIDATION
==============================

Here's what I'm about to do (all three run in parallel):

  +---------------------+   +------------------+   +------+
  |        TEST         |   |        QA        |   | DOCS |
  +---------------------+   +------------------+   +------+
  | - Run unit tests    |   | - Lint with fix  |   | Check|
  |   for affected pkgs |   | - Build affected |   | README|
  | - Verify integ test |   |   packages       |   | needs|
  |   files exist and   |   |                  |   | update|
  |   are well-formed   |   |                  |   |      |
  | - Dry-run integ     |   |                  |   |      |
  |   snapshot check    |   |                  |   |      |
  +---------------------+   +------------------+   +------+
        |                  |                  |
        +------------------+------------------+
                           |
                    04-validation.md

Kicking off now...
```

Then proceed immediately without waiting for a response.

Run all three tasks in parallel. You **MUST NOT** wait for one to finish before starting the next — because they are independent and running them concurrently reduces wall-clock time.

- TEST task (see `references/test-engineer-sop.md` for exact commands):
  - Run unit tests for all affected packages
  - Integ tests:
    1. Check if new or updated integ test files exist (compare against `02-solution.md` plan)
    2. If YES — deploy and regenerate snapshots on those files
    3. If NO  — run integ on the relevant module to confirm existing snapshots are intact
- QA checks (see `references/quality-assurance-sop.md` for lint and build commands)
- Documentation updates

When all three tasks have succeeded, summarize the testing, QA and documentation details in `.contributions/<ISSUE_NUMBER>/04-validation.md`.

### Phase 5: Self Review

**STEP 0 — READ SOPs (non-negotiable, do this FIRST):**

You **MUST** read ALL THREE of these files before ANY other action in this phase:
1. `references/security-reviewer-sop.md` - provides instructions for the security review task. Required deliverable: `security-review.md`.
2. `references/regression-reviewer-sop.md` - provides instructions for the regression review task. Required deliverable: `regression-review.md`
3. `references/review-report-generator-sop.md` - provides instructions for creating the final review report. Required deliverable: `05-review.md`

After reading, confirm you understand the required deliverables and procedures.

**STEP 1**

Before doing anything, present this summary to the user:

```text
PHASE 5: SELF REVIEW
=====================

Here's what I'm about to do (both run in parallel):

  +------------------+     +------------------+
  |    SECURITY      |     |   REGRESSION     |
  |     REVIEW       |     |     REVIEW       |
  +------------------+     +------------------+
  | - IAM/policy     |     | - Snapshot diff  |
  |   impact check   |     | - API surface    |
  |   check          |     |   breaking change|
  | - Secrets/creds  |     | - Existing test  |
  |   exposure check |     |   coverage gaps  |
  | - CDK nag rules  |     | - CloudFormation |
  +------------------+     |   drift risk     |
           |               +------------------+
           +--------+--------+
                    |
             05-review.md
                    |
                    v
          SYNTHESIZE GO/NO-GO

Kicking off now...
```

Then proceed immediately without waiting for a response.

Run both tasks in parallel. You **MUST NOT** wait for one to finish before starting the next — because security and regression reviews are independent analyses.

Synthesize findings into a go/no-go report in `.contributions/<ISSUE_NUMBER>/05-review.md` and present to user before PR submission.

### Phase 6: PR Submission

After the user approves the go/no-go report, commit and create the PR.

**MANDATORY**: Every PR description body MUST end with this marker line:

```
[comment]: <> (by cdk-contribution-skill)
```

This is a hidden Markdown comment that identifies PRs created via this workflow.
It MUST be the last line of the PR body. Do NOT omit it.

---

## Prerequisites

- Local clone of `aws-cdk` repository
- Node.js v18+, Yarn
- `gh` CLI (preferred for issue analysis) — `gh auth login` must be completed
- GitHub MCP server (fallback if `gh` CLI is unavailable)
