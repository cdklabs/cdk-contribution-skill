# Debugging CodeBuild CI Errors

## Quick Commands

### View last 100 lines of failed job
```bash
gh run view <RUN_ID> --repo aws/aws-cdk --log --job <JOB_ID> | tail -100
```

### Search for error markers (CDK uses `!!!!!!!!!!` to mark failures)
```bash
gh run view <RUN_ID> --repo aws/aws-cdk --log --job <JOB_ID> | grep -A 10 -B 10 "!!!!!!!!!!"
```

### Search for generic errors
```bash
gh run view <RUN_ID> --repo aws/aws-cdk --log --job <JOB_ID> | grep -i "error"
```

## Example

For run ID `20999722498` and job ID `60366124784`:

```bash
gh run view 20999722498 --repo aws/aws-cdk --log --job 60366124784 | grep -A 10 -B 10 "!!!!!!!!!!" | head -50
```

## Getting Run/Job IDs

From the GitHub Actions URL:
```
https://github.com/aws/aws-cdk/actions/runs/20999722498/job/60366124784
                                                    ^^^^^^^^^       ^^^^^^^^^^^
                                                    RUN_ID          JOB_ID
```

Or list recent runs:
```bash
gh run list --repo aws/aws-cdk --branch <BRANCH_NAME> --limit 5
```
