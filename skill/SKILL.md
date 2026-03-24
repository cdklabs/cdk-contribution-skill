---
name: cdk-contribution-skill
description: Guide AWS CDK contributions from GitHub issue analysis to PR-ready code. Use when contributing to aws-cdk repository, analyzing CDK issues, implementing fixes, or preparing pull requests.
---

# CDK Contribution Workflow

Orchestrates specialized phases for AWS CDK contributions with human approval gates.

## MANDATORY: Present Workflow Overview FIRST

BEFORE doing ANY work, you MUST present the ASCII workflow diagram below, explain the process,
and wait for confirmation. This is NON-NEGOTIABLE.

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
     |  [subagent] Analyze issue,    |
     |  classify type, explore code  |
     +---------------+---------------+
                     |
     +---------------+--------------+
     |       PHASE 2: PLANNING      |
     +---------------+--------------+
                     |
                     v
     +-------------------------------+
     |      SOLUTION ARCHITECT       |
     |  [subagent] Propose solution, |
     |  tests, and impl approach     |
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
- Phases 1 and 2 run as subagents automatically after you say yes
- You review and approve the plan before any code is written
- I pause for your input at critical decision points

Should we start? Yes | No
```

STOP and wait for user response. Do NOT proceed until the user confirms.

---

## Deliverables

Each phase MUST write its output to a markdown file under `.kiro/contributions/<ISSUE_NUMBER>/`:

| Phase   | Output file                                           |
|---------|-------------------------------------------------------|
| Phase 1 | `.kiro/contributions/<ISSUE_NUMBER>/01-analysis.md`   |
| Phase 2 | `.kiro/contributions/<ISSUE_NUMBER>/02-solution.md`   |
| Phase 3 | `.kiro/contributions/<ISSUE_NUMBER>/03-build.md`      |
| Phase 4 | `.kiro/contributions/<ISSUE_NUMBER>/04-validation.md` |
| Phase 5 | `.kiro/contributions/<ISSUE_NUMBER>/05-review.md`     |

The subagent for each phase is responsible for writing its own deliverable file before returning.
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

**Immediately** invoke Phase 1 as a subagent with this task:

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
>    - Use MCP github tools with perPage: 3 to avoid token overflow
>
> Then explore the aws-cdk codebase to understand the affected module(s), existing patterns, test
> conventions, and any related code. Classify the issue type (bug, feature, docs, etc.).
> Produce a structured analysis: issue summary, affected files/modules, root cause or gap,
> relevant CDK patterns observed, and any constraints or risks.
>
> Write your full analysis to `.kiro/contributions/<ISSUE_NUMBER>/01-analysis.md` before returning.

Then, **without pausing**, invoke Phase 2 as a subagent with the Phase 1 output as context:

> Using the analysis in `.kiro/contributions/<ISSUE_NUMBER>/01-analysis.md`, propose a concrete
> solution for the CDK contribution. Include: the implementation approach, which files need to
> change and how, required unit tests (what to test and why), required integ tests (which integ
> test file, what scenario to cover), and any breaking change considerations. Be specific about
> CDK patterns to follow.
>
> Write your full proposal to `.kiro/contributions/<ISSUE_NUMBER>/02-solution.md` before returning.

---

### After both subagents complete, present this proposal to the user:

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

Before doing anything, present this summary to the user:

```text
PHASE 3: BUILD & IMPLEMENTATION
================================

Here's what I'm about to do:

  1. Create local branch   fix/issue-<NUMBER>-<slug>
  2. Sync with upstream    git fetch upstream && git rebase upstream/main
  3. Assess build need     read 02-solution.md to decide if yarn build is required
  4. Implement             apply all changes from the approved plan

Kicking off now...
```

Then proceed immediately without waiting for a response.

Steps (in order, no skipping):

1. Create a local PR branch named `fix/issue-<NUMBER>-<short-slug>` from current HEAD
2. Sync with upstream main:
   - `git fetch upstream`
   - `git rebase upstream/main`
   - If no upstream remote exists: `git remote add upstream https://github.com/aws/aws-cdk.git` then fetch
3. Assess whether a build is required:
   - Read `02-solution.md` — if changes touch generated code, L1 constructs, or cross-package
     imports that require compilation, build the affected package(s) with `yarn build`
   - If changes are purely in `.ts` source + tests with no codegen dependency, skip the build
     and go straight to implementation
   - Document the decision in `03-build.md`
4. Implement all changes from the approved plan in `02-solution.md`
5. Write deliverable to `.kiro/contributions/<ISSUE_NUMBER>/03-build.md` (with ASCII diagram)

### Phase 4: Parallel Validation

Before doing anything, present this summary to the user:

```text
PHASE 4: PARALLEL VALIDATION
==============================

Here's what I'm about to do (all three run in parallel):

  +---------------------+   +------------------+   +------+
  |        TEST         |   |        QA        |   | DOCS |
  +---------------------+   +------------------+   +------+
  | - Run unit tests    |   | yarn lint        |   | Check|
  |   for affected pkgs |   | yarn build (tsc) |   | README|
  | - Verify integ test |   | for affected     |   | needs|
  |   files exist and   |   | packages         |   | update|
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

Run all three as parallel subagents simultaneously. Do NOT wait for one to finish before starting the next.

- TEST subagent:
  - Run unit tests for all affected packages
  - Integ tests:
    1. Check if new or updated integ test files exist (compare against `02-solution.md` plan)
    2. If YES — run `yarn integ-runner --update-on-failed` on those files to deploy and regenerate snapshots
    3. If NO  — run `yarn integ-runner --update-on-failed` on the relevant module to confirm existing snapshots are intact and unchanged
- QA checks (lint, build)
- Documentation updates

### Phase 5: Self Review

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

Run both as parallel subagents simultaneously. Do NOT wait for one to finish before starting the next.

Then synthesize findings into a go/no-go report and present to user before PR submission.

---

## Prerequisites

- Local clone of `aws-cdk` repository
- Node.js v18+, Yarn
- `gh` CLI (preferred for issue analysis) — `gh auth login` must be completed
- GitHub MCP server (fallback if `gh` CLI is unavailable)

## Reference Files

Detailed SOPs available in `references/`:
- Issue analysis, solution architecture, build, implementation
- Testing, QA, documentation, security review, regression review
- Supplementary skills: debug-ci, integ-testing, linting
