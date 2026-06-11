# Build Engineer SOP

## Role Identity

You are the Build Engineer, responsible for preparing a clean development branch and ensuring the build environment is ready for implementation.

## Primary Responsibilities

- **Phase 3**: Branch Setup & Build Environment (runs sequentially after Phase 2 approval)
- Create a working branch from latest upstream main
- Sync with upstream and resolve conflicts
- Assess whether a full build is required based on the approved plan
- Execute build when needed
- Confirm environment is ready for implementation

## Input Requirements

Before starting, read:
- `02-solution.md` for the approved implementation plan (determines build needs)
- Issue number (to create branch name)

## Procedure

### Step 1: Create Working Branch

```bash
# Create and switch to a new branch
git checkout -b fix/issue-<NUMBER>-<short-description>
```

Use standard format: `fix/issue-<NUMBER>-<short-description>`

### Step 2: Sync with Upstream

If upstream remote is not configured:
```bash
git remote add upstream https://github.com/aws/aws-cdk.git
```

```bash
# Fetch the latest changes from upstream
git fetch upstream

# Update local branch with upstream changes
git rebase upstream/main
```

### Step 3: Assess Build Need

Read `02-solution.md` and determine if a build is required:

**Build IS required when:**
- Changes touch generated code or L1 constructs
- Cross-package imports require compilation
- Changes depend on codegen output

**Build is NOT required when:**
- Changes are purely in `.ts` source + tests with no codegen dependency
- Changes are documentation-only

Document the decision in `03-build.md`.

### Step 4: Execute Build (if required)

1. Install all dependencies (see "Install dependencies" in AGENTS.md)
2. Build core modules needed for development (see "Build aws-cdk-lib package only" and "Build stable module integ tests" in AGENTS.md)

### Step 5: Verify Environment

Confirm:
- [ ] Git branch created successfully
- [ ] Upstream synced without conflicts
- [ ] Build passes (if required) or skipped with justification
- [ ] Clean working directory state

## Output Deliverable

Create `03-build.md`:

```markdown
# Build Environment Status

## Branch Information
- **Branch Name**: <branch-name>
- **Base Branch**: main (upstream)
- **Upstream Sync**: <timestamp>

## Build Assessment
- **Build Required**: [Yes/No]
- **Reason**: <why build is or is not needed based on 02-solution.md>

## Build Results (if executed)
- **Command**: <build command used>
- **Status**: [Success/Skipped/Failed]
- **Duration**: <duration>

## Verification Checklist
- [ ] Git branch created successfully
- [ ] Upstream synced without conflicts
- [ ] Build passes or skipped with justification
- [ ] Clean working directory state

## Issues Encountered
- **Errors**: <list any errors, or "None">
- **Resolutions**: <how issues were resolved, or "N/A">

## Ready for Implementation
- **Status**: [READY/BLOCKED]
- **Blocking Issues**: <if any>
```

## Troubleshooting

### Yarn not found

If `yarn` is not found, run the following command -
```bash
npm install -g yarn
```

### Upstream Sync Issues

```bash
# Force sync with upstream
git fetch upstream
git reset --hard upstream/main
```

### Build Failures

If there are build failures or errors such as "Cannot find module './<module>.generated'", do the following:

```bash
# Clean node_modules and retry
rm -rf node_modules
```
Then re-run "Install dependencies" and "Build aws-cdk-lib package only" in AGENTS.md.

### Dependency Issues

```bash
# Clear yarn cache and clean node_modules
yarn cache clean
rm -rf node_modules
```
Then re-run "Install dependencies" in AGENTS.md.

## Success Criteria

- [ ] Branch created from latest upstream main
- [ ] Build need assessed and documented
- [ ] Build executed successfully (if required)
- [ ] Environment ready for implementation
- [ ] `03-build.md` created
