# github-action-workflow
This repository contains the reusable Github Actions workflows used by IOTechSystems.

Refer to [Github Actions Reusing Workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows) for details.


## Workflows

### golangci-lint 

The workflow runs golangci-lint action and reports issues from linters.

| Inputs | Description |
|--------|-------------|
| `WORKING_DIRECTORY` | The working directory, default is project root. |
| `GO_LINT_CONFIG_PATH` | The GolangCI-Lint config path which is relative to `WORKING_DIRECTORY`, default is no config. |
| `REQUIRED_PACKAGES` | The required packages are installed by `apt`, seperate the packages with spaces. |
| `USE_PRIVATE_MODULE` | If true, set up the enviroment for the private go modules, and retrieve the modules versions from scripts/Jenkinsfile. |
| `GQL_DIRECTORY` | The directory which defines the GraphQL schema and gqlgen generated files, default is an empty string. |

### sonarqube-scan

The workflow runs sonarqube-scan action to detect Bugs, Vulnerabilities and Code Smells. It also sets up Go based on go.mod and runs `make test` to get Go unit test coverage reports.

| Inputs | Description |
|--------|-------------|
| `PROJECT_NAME` | **Required.** The repository name for SonarQube. |

## Example

An Example of using the reusable workflows in a caller workflow:

```
name: code-analysis
on:
  # Trigger the workflow on push or pull request,
  # but only for the v[0-9]+.[0-9]+-branch (e.g. v2.0-branch)
  push:
    branches:
      - v[0-9]+.[0-9]+-branch
  pull_request:
    # By default, a pull_request's activity type is opened, synchronize, or reopened
    branches:
      - v[0-9]+.[0-9]+-branch

jobs:
    call-go-lint:
      uses: IOTechSystems/github-action-workflow/.github/workflows/reusable-golangci-lint.yml@main
      with:
        # Optional: working directory, default is project root 
        WORKING_DIRECTORY: "."
        # Optional: golangci-go config path, default is no config
        GO_LINT_CONFIG_PATH: ""
        # Optional: required packages installed by apt
        REQUIRED_PACKAGES: ""
        
    call-sonar-scan:
      uses: IOTechSystems/github-action-workflow/.github/workflows/reusable-sonarqube-scan.yml@main
      with:
        # Required: repository name
        PROJECT_NAME: go-mod-core-contracts
      # Required: pass SONAR_TOKEN to the reusable workflow  
      secrets: inherit
```