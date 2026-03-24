# CDK Contribution Skill

CDK Contribution Skill is an Agent Skill that combines multiple specialized subagents, AWS tooling, and Agent SOPs to streamline the entire contribution process. It helps experienced developers move faster from issue analysis to PR submission while maintaining human oversight at critical decision points.

## Developer Ecosystem Expansion

- **Lower Contribution Barriers**: Standardized LLM prompt templates maintained by the CDK team make it easier for new developers to contribute to AWS CDK
- **Enhanced Development Efficiency**: Kiro IDE integration accelerates PR authoring and self-review processes
- **Knowledge Standardization**: Codifies CDK team best practices into reusable, version-controlled prompt templates

### Core Components

- **Skill Definition** (`skill/SKILL.md`) - Orchestrator instructions with phased workflow, approval gates, and deliverable requirements
- **CDK Team Agent SOPs** (`skill/references/`) - Standard operating procedures for each specialized phase of the contribution workflow
- **Subagent Orchestration** - Parallel execution of specialized agents (Issue Analyst, Solution Architect, Implementation Specialist, Test Engineer, QA, Documentation, Security Review, etc.)

### What It Does

The skill implements an orchestrator pattern that:

- Runs subagents in parallel where possible
- Includes a human approval gate before implementation
- Produces all PR artifacts (code changes, tests, documentation, changelog)
- Writes structured deliverables to `.kiro/contributions/<ISSUE_NUMBER>/`
- Automates the journey from "here's a GitHub issue" to "here's a ready-to-submit PR"

## What CDK Contribution Skill Is (and Isn't)

### ✅ What It Is

A contributor acceleration tool that helps experienced developers move faster from issue → PR ready for review, with:

- Human-in-the-loop at critical decision points
- Approval gateway before implementation
- Contributor remains accountable for the final PR

### ❌ What It Is NOT

- Not a "zero-coding" AI PR generator
- Not a tool for users with no CDK/coding experience
- Not producing unreviewed "AI slop" PRs

## Who Should Use This Skill

### Good Fit

- Developers with coding experience looking to contribute to AWS CDK
- Beginning contributors who want guidance on CDK patterns and conventions
- Repeat/seasoned contributors who want to accelerate routine PR tasks
- Anyone comfortable reviewing and taking ownership of AI-assisted code

### Not a Good Fit

- Users with no coding experience
- Anyone expecting fully automated, zero-review PRs
- Users unwilling to review and approve AI-generated changes
- Those looking to bulk-generate low-quality contributions

## How It Works

```
┌─────────────────────────────────────┐
│         MAIN ORCHESTRATOR           │
│       (Me - Coordinates work)       │
└─────────────────┬───────────────────┘
                  │
    ┌─────────────┴─────────────┐
    │     PHASE 1: ANALYSIS     │
    └─────────────┬─────────────┘
                  ▼
    ┌───────────────────────────┐
    │     01 ISSUE ANALYST      │
    │   Analyze issue & code    │
    └─────────────┬─────────────┘
                  │
    ┌─────────────┴─────────────┐
    │     PHASE 2: PLANNING     │
    └─────────────┬─────────────┘
                  ▼
    ┌───────────────────────────┐
    │   03 SOLUTION ARCHITECT   │
    │   Create impl plan        │
    └─────────────┬─────────────┘
                  ▼
    ┌───────────────────────────┐
    │   🧑 YOUR APPROVAL 🧑     │
    │   Review plan before      │
    │   any code is written     │
    └─────────────┬─────────────┘
            [YES] │
    ┌─────────────┴─────────────┐
    │  PHASE 3: BUILD & IMPL    │
    └─────────────┬─────────────┘
                  ▼
    ┌───────────────────────────┐
    │  02 BUILD + 04 IMPLEMENT  │
    │  Setup env & write code   │
    └─────────────┬─────────────┘
                  │
    ┌─────────────┴─────────────┐
    │ PHASE 4: PARALLEL VALID   │
    └─────────────┬─────────────┘
        ┌─────────┼─────────┐
        ▼         ▼         ▼
    ┌───────┐ ┌───────┐ ┌───────┐
    │ TEST  │ │  QA   │ │ DOCS  │
    └───┬───┘ └───┬───┘ └───┬───┘
        └─────────┼─────────┘
                  │
    ┌─────────────┴─────────────┐
    │   PHASE 5: SELF REVIEW    │
    └─────────────┬─────────────┘
        ┌─────────┴─────────┐
        ▼                   ▼
    ┌───────────┐     ┌───────────┐
    │ SECURITY  │     │REGRESSION │
    │  REVIEW   │     │  REVIEW   │
    └─────┬─────┘     └─────┬─────┘
          └─────────┬─────────┘
                    ▼
          ┌───────────────────┐
          │   SYNTHESIZE      │
          │   REVIEW REPORT   │
          └─────────┬─────────┘
                    ▼
    ┌───────────────────────────┐
    │   🧑 REVIEW FINDINGS 🧑   │
    │   Submit PR or fix issues │
    └───────────────────────────┘
```

## Workflow Phases

### Phase 1: Analysis
The Issue Analyst examines the GitHub issue and relevant codebase to understand requirements and context.

### Phase 2: Planning
The Solution Architect creates a detailed implementation plan based on the analysis.

**🧑 Human Approval Gate**: You review and approve the plan before any code is written.

### Phase 3: Build & Implementation
The Build Engineer sets up the environment, and the Implementation Specialist writes the code according to the approved plan.

### Phase 4: Parallel Validation
Multiple specialists work in parallel:
- Test Engineer creates and runs tests
- QA Specialist validates quality standards
- Documentation Specialist updates docs

### Phase 5: Self Review
Security and Regression reviewers examine the changes, then synthesize findings into a comprehensive review report.

**🧑 Final Review**: You review all findings and decide whether to submit the PR or address issues.

## Deliverables

Each phase writes its output to `.kiro/contributions/<ISSUE_NUMBER>/`:

| Phase | Output File |
|-------|-------------|
| Phase 1 | `01-analysis.md` |
| Phase 2 | `02-solution.md` |
| Phase 3 | `03-build.md` |
| Phase 4 | `04-validation.md` |
| Phase 5 | `05-review.md` |

Each deliverable includes ASCII diagrams summarizing key findings for that phase.

## Reference SOPs

Detailed standard operating procedures are in `skill/references/`:

| SOP | Purpose |
|-----|---------|
| `01-issue-analyst-sop.md` | Issue analysis and classification |
| `02-build-engineer-sop.md` | Build environment setup |
| `03-solution-architect-sop.md` | Solution design and planning |
| `04-implementation-specialist-sop.md` | Code implementation |
| `05-test-engineer-sop.md` | Unit and integration testing |
| `06-quality-assurance-sop.md` | Lint, build, and quality checks |
| `07-documentation-specialist-sop.md` | README and docs updates |
| `08-security-reviewer-sop.md` | Security review |
| `09-regression-reviewer-sop.md` | Regression review |
| `10-review-report-generator-sop.md` | Final review report synthesis |

Supplementary references: `debug-ci.md`, `integ-testing.md`, `linting.md`

## Getting Started

1. Install the CDK Contribution Skill in Kiro
2. Point the skill at a CDK GitHub issue
3. Review and approve the implementation plan
4. Review the final output and submit your PR

## Prerequisites

- Local clone of the `aws-cdk` repository
- Node.js v18+, Yarn
- `gh` CLI (preferred for issue analysis) with `gh auth login` completed
- GitHub MCP server (fallback if `gh` CLI is unavailable)


## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This project is licensed under the Apache-2.0 License.
