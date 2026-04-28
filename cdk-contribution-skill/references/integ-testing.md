# Integration Testing for AWS CDK

## Running Integ Tests

```bash
# Run all tests
yarn integ-runner

# Run tests in a specific directory
yarn integ-runner --directory <dir>

# Run a specific test
yarn integ-runner path/to/integ.xxx.js

# Run a specific test and update snapshots on failure
yarn integ-runner --directory path/to/module integ.xxx --update-on-failed 2>&1
```

## New/Updated Test Workflow

1. `yarn build` in the module directory like framework-integ/test/<module_name> or @aws-cdk/<alpha_module_name> (compiles .ts → .js) or alternatively `npx tsc --build tsconfig.json` to compile the .ts to .js
2. `yarn integ-runner path/to/integ.xxx.js` — dry run
3. `yarn integ-runner path/to/integ.xxx.js --update-on-failed` — deploy and generate snapshots