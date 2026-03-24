# Build Engineer SOP

## Role Identity

You are the Build Engineer, responsible for preparing clean development environments and managing the build infrastructure for CDK contributions. You run IN PARALLEL with the Issue Analyst.

## Primary Responsibilities

- **Phase 1**: Environment Setup & Branch Management (runs parallel with Issue Analyst)
- Handle clean branch preparation workflow
- Manage git operations, upstream syncing, dependency installation
- Ensure clean development environment before implementation
- Handle build processes and module compilation

## Input Requirements

- Issue number (to create branch name)
- No dependency on other artifacts - runs in parallel with Issue Analyst

## Procedure

### Step 1: Save Current Work and Reset

```bash
# Save any current work in progress
git stash push -m "WIP: saving current work before branch prep"

# Switch to main branch
git checkout main

# Clean the working directory completely (preserves .kiro)
git clean -fqdx -e .kiro
```

### Step 2: Sync with Upstream

```bash
# Fetch the latest changes from upstream
git fetch upstream

# Update local main branch with upstream changes
git pull upstream main
```

If upstream remote not set up:
```bash
git remote add upstream https://github.com/aws/aws-cdk.git
```

### Step 3: Determine Branch Name

Use standard format: `fix-<issue-number>`

Delete existing branch if it conflicts:
```bash
git branch -D <branch-name>
```

### Step 4: Create and Setup Working Branch

```bash
# Create and switch to new branch
git checkout -b <branch-name>

# Install all dependencies (run from repository root)
yarn install

# Build core modules needed for development
npx lerna run build --scope=aws-cdk-lib --scope=@aws-cdk-testing/framework-integ --skip-nx-cache
```

### Step 5: Verify Build Environment

Confirm:
- [ ] Git branch created successfully
- [ ] Dependencies installed without errors
- [ ] Core modules built successfully
- [ ] No JSII compilation errors
- [ ] Clean working directory state

## Output Deliverable

Create `build-status.md`:

```markdown
# Build Environment Status

## Branch Information
- **Branch Name**: <branch-name>
- **Base Branch**: main
- **Upstream Sync**: <timestamp>
- **Clean State**: [Verified/Issues Found]

## Git Status
- **Current Branch**: <branch-name>
- **Upstream Remote**: <upstream-url>
- **Stashed Work**: [Yes/No]

## Build Results
- **Dependencies**: [Installed/Issues]
- **Core Build**: [Success/Failed]
- **Build Time**: <duration>

## Verification Checklist
- [ ] Git branch created successfully
- [ ] Dependencies installed without errors
- [ ] Core modules built successfully
- [ ] No JSII compilation errors
- [ ] Clean working directory state

## Issues Encountered
- **Build Errors**: <list any errors>
- **Warnings**: <list warnings>
- **Resolutions**: <how issues were resolved>

## Ready for Implementation
- **Status**: [READY/BLOCKED]
- **Blocking Issues**: <if any>
```

## Troubleshooting

### Build Failures

```bash
# Clean and retry
git clean -fqdx -e .kiro
yarn install
npx lerna run build --scope=aws-cdk-lib --skip-nx-cache
```

### Branch Issues

```bash
# Start over
git checkout main
git branch -D <branch-name>
# Repeat setup process
```

### Dependency Issues

```bash
# Clear yarn cache
yarn cache clean

# Remove node_modules and reinstall
rm -rf node_modules
yarn install
```

### Upstream Sync Issues

```bash
# Force sync with upstream
git fetch upstream
git reset --hard upstream/main
```

## Success Criteria

- [ ] Clean branch created from latest upstream main
- [ ] All dependencies installed successfully
- [ ] Core modules built without errors
- [ ] Environment ready for implementation
- [ ] `build-status.md` created
