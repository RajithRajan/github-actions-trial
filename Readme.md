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
GitHub provides multiple sources of data under different contexts
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