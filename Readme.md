# GitHub Actions
```
Workflow
   |
   v
  Jobs
   |
   v
 Steps
```
## Workflows:
- Define which triggers start the workflow
- Are composed of one or more jobs
- Dir `.github/workflows`

## Jobs
- Define the environment where they will run
- Are composed of one or more steps
- Run in parallel by default if no dependency defined

## Steps
- Define the actual scipt or GitHub Action that will be executed
- Run requentially by default
