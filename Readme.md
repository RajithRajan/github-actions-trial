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
jobs:
  backwards-compatibility:
    name: ${{ matrix.os }}-${{ matrix.node }}
    strategy:
      matrix:
        node: [14, 16, 18]
        os:
          - ubuntu-latest 
          - macos-latest
          - windows-latest
    runs-on: ${{ matrix.os }}
    steps: 
      - uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node }}
```

## Custom Actions
Requires an action.yaml file and should have its own repository to be reused by other repos.
- Composite Actions 
  - Simplest types of custom, it is grouping of other GitHub Actions.
- JavaScript Actions 
  - Allows writing any type of custom logic. @actions packages provide lot of functionality. Requires JavaScript knowledge and Node environment.
- Docker Actions
  - Allows writing any type of custom logic in any programming language. No @actions package support.
  - While defining the action we can provide either an image or Docker file location. In case of docker file it would be built on each run, so it better to provide image of the build could take some time for build.

## Reusable Workflow 
It's always advisable to create reusable work flow to reduce duplication and to set some standards. Any workflow can be made Reusable by adding `workflow_call` to the top-level `on` key of the workflow definition. These workflow are at job and not at step level. env variables are not passed to called workflow.
**Reusable workflow**
```
on:
  workflow_call:
    inputs:
      aws-region:
        type: string
      cluster:
        type: string
    secrets:
      auth-token:
        type: string
    outputs:
      server-url:
        value: <value>
```
**Caller workflow**
```
jobs:
  backend-infra:
    uses: reusable-deploy@v1
      secrets:
        auth-token: ${{ secrets.GH_PAT }}
      with:
        aws-region: ${{ vars.AWS_REGION }}
```

## Managing Concurrency 
There might be cases where you don't want to run build job for a commit if there were subsequent commits on same branch,this can be done by defining concurrency. Concurrency can be defined at workflow level or at job level.
```
concurrency:
  group: ${{ gitHub.workflow }}-${{ gitHub.ref }}
```