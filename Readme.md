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
## Basics
### Workflows:
- Define which triggers start the workflow
- Are composed of one or more jobs
- Dir `.github/workflows`

### Jobs
- Define the environment where they will run
- Are composed of one or more steps
- Run in parallel by default if no dependency defined

### Steps
- Define the actual scipt or GitHub Action that will be executed
- Run requentially by default

## GitHub Runners
Common charecteristics of a runner
  - A runner is assigned for a job i.e. it's shared by all the steps in a job but different jobs can get different runners.
  - Runner OS could be windows, ubuntu & mac

### Types of runners
There are basically two types of runners 
- **GitHub-hosted** 
  - Managed Service
  - Different job get clean runner

- **Self-hosted**
  - Which are managed by us.
  - Can be added at repository, organization or enterprise level.
  - Jobs do not necessarily have clean instances

## Workflow Trigger
We can make use of different methods to trigger workflows
- **Repository events** - push, issues, pull_requests, pull_request_review, fork etc
- **Manual trigger** - via UI, via API call, from another workflow
- **Schedule** - Cron job
### Ref
- [trigger-a-workflow](https://docs.github.com/en/actions/how-tos/write-workflows/choose-when-workflows-run/trigger-a-workflow)
- [about-events-that-trigger-workflows](https://docs.github.com/en/actions/reference/workflows-and-actions/events-that-trigger-workflows#about-events-that-trigger-workflows)

## Event Filters
We can add condition of the Events for triggering our workflow
e.g.
- push event
  - branches/branches_ignore - trigger the pipeline only for specific branches
  - tag/tag_ignore - If tags are pushed
  - paths/paths_ignore - If files are changed in specific path  
  etc

## Workflow Contexts 
Contexts are a way to access information about workflow runs, variables, runner environments, jobs, and steps. Each context is an object. 

Context can be accessed using the expression syntax. 
```
${{ <context> }}
```
- **github**
  - Commit SHA
  - Event name
  - Ref of branch or tag triggering the workflow 
- **env**
  - Contains variables that have been defined in a workflow, job or step. Change based on which part of workflow is executing.
- **inputs**
  - Contains input properties passed via the keyword with to an action, reusable workflow or manually triggered workflow.
- **vars**
  - Contains custom configuration variables set at the organisation, repository and environment levels.
- secrets, strategy, matrix, needs etc

### Ref
[contexts](https://docs.github.com/en/actions/reference/workflows-and-actions/contexts)

## Expressions
Expression is used dynamic values in workflow. Should use `${{ <expression> }}` syntax and can be combination of
- Literal values
- Context values
- Functions 

## Variables & Secrets 
Variables and Secrets can be defined at
- Organisation 
- Repository 
- Environments 

## Functions 
Functions can be 
- General purpose functions 
  - contains()
  - startsWith()
  - endsWith()
  - fromJSON()
  - toJSON()
- Status check functions 
  - success()
  - failure()
  - always()
  - cancelled()

## Controlling the Execution flow
- Steps execute only if previous step was successful, this in standard execution.
- We can use `if:` to change this behaviour using status check functions.
- Using `needs:` keyword at jobs to create dependency between jobs.
- Using `continue-on-error:` keyword on job we can mark the job pass even if any containing steps fails.

## Inputs 
Inputs can be used to pass specific information to workflow(dispatch/call) or action which would be used during execution.
```
inputs:
  action:
    type: choice 
    description: What needs to be done
    options:
    - build
    - build & test
    - build, test & deployment
    - deployment 
  reason:
    required: true
  run-performance-test:
    type: Boolean
    description: Run performance test after deployment 
  environment:
    type: environment
```

## Outputs
Output data from jobs for later usage, these are stored in different files for each step
```
jobs:
  welcome:
    runs-on: ubuntu-latest
    outputs:
      name: ${{ steps.step1.outputs.NAME}}
    steps:
      - id: step1
        run: echo "NAME=Lauro" >> "$GITHUB_OUTPUT"
  goodbye:
    runs-on: ubuntu-latest
    needs: welcome
    steps:
    - run: echo "Bye, ${{ needs.welcome.outputs.name}}"
```

## Caching
Speed up workflow by caching the dependency which can be used to skip downloading same dependency for each build. Caches are stored for 7D.
```
steps:
  - uses: actions/cache@v3
    id: cache
    with:
      path: node_modules
      key: ${{ had files('**/package-lock.json') }}
  - name: Install dependencies
    if: steps.cache.outputs.cacahe-hit != 'true:
    run: npm ci
  - name: Lint & test
    run: |
      npm run lint
      npm run test
```

## Artifacts
Share data between jobs in a workflow and store data once the workflow has completed. By default it's stored for 90D.
- actions/upload-artifact@v4
- actions/download-artifact@v5

## Matrices 
Runs several variations of the same job
```
```