# Azure Pipeline Schema


## Pipeline

```yaml
name: string  # build numbering format
resources:
  pipelines: [ pipelineResource ]
  containers: [ containerResource ]
  repositories: [ repositoryResource ]
variables: # several syntaxes, see specific section
trigger: trigger
pr: pr
stages: [ stage | templateReference ]
```

*Single stage with direct `job` keyword* 
```yaml
jobs: [ job | templateReference ]
```

*Single stage and single job with direct `steps` keyword*
```yaml
steps: [ script | bash | pwsh | powershell | checkout | task | templateReference ]
```

**Beispiel**

```yaml
name: $(Date:yyyyMMdd)$(Rev:.r)
variables:
  var1: value1
jobs:
- job: One
  steps:
  - script: echo First step!
```

## Stage

```yaml
stages:
- stage: string  # name of the stage (A-Z, a-z, 0-9, and underscore)
  displayName: string  # friendly name to display in the UI
  dependsOn: string | [ string ]
  condition: string
  variables: # several syntaxes, see specific section
  jobs: [ job | templateReference]
```


**Beispiel**

```yaml
stages:
- stage: Build
  jobs:
  - job: BuildJob
    steps:
    - script: echo Building!
- stage: Test
  jobs:
  - job: TestOnWindows
    steps:
    - script: echo Testing on Windows!
  - job: TestOnLinux
    steps:
    - script: echo Testing on Linux!
- stage: Deploy
  jobs:
  - job: Deploy
    steps:
    - script: echo Deploying the code!
```

## Job

```yaml
jobs:
- job: string  # name of the job (A-Z, a-z, 0-9, and underscore)
  displayName: string  # friendly name to display in the UI
  dependsOn: string | [ string ]
  condition: string
  strategy:
    parallel: # parallel strategy; see the following "Parallel" topic
    matrix: # matrix strategy; see the following "Matrix" topic
    maxParallel: number # maximum number of matrix jobs to run simultaneously
  continueOnError: boolean  # 'true' if future jobs should run even if this job fails; defaults to 'false'
  pool: pool # see the following "Pool" schema
  workspace:
    clean: outputs | resources | all # what to clean up before the job runs
  container: containerReference # container to run this job inside of
  timeoutInMinutes: number # how long to run the job before automatically cancelling
  cancelTimeoutInMinutes: number # how much time to give 'run always even if cancelled tasks' before killing them
  variables: # several syntaxes, see specific section
  steps: [ script | bash | pwsh | powershell | checkout | task | templateReference ]
  services: { string: string | container } # container resources to run as a service container
  uses: # Any resources (repos or pools) required by this job that are not already referenced
    repositories: [ string ] # Repository references to Azure Git repositories
    pools: [ string ] # Pool names, typically when using a matrix strategy for the job
```

**Beispiel**

```yaml
jobs:
- job: MyJob
  displayName: My First Job
  continueOnError: true
  workspace:
    clean: outputs
  steps:
  - script: echo My first job
```

## Steps

```yaml
steps: [ script | bash | pwsh | powershell | checkout | task | templateReference ]
```

**Beispiel**

```yaml
steps:
- script: echo This runs in the default shell on any machine
- bash: |
    echo This multiline script always runs in Bash.
    echo Even on Windows machines!
- pwsh: |
    Write-Host "This multiline script always runs in PowerShell Core."
    Write-Host "Even on non-Windows machines!"
```