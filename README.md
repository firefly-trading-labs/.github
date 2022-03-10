# Firefly Trading Labs' .github repository

This repository is to be used for any shared functionality that is required for operating within GitHub.

## Pull Request template

The default pull request template is setup in `.github/pull_request_template.md`.

## Recommended GitHub Action triggers

> We recommend adding the `workflow_dispatch` trigger to every action to allow it to be manually run

### For unit tests

For running unit tests, set up a trigger to run the tests when either the test project changes and any of it's dependencies

```yaml
on:
  pull_request:
    branches:
      - main
    paths:
      - path/to/Some.Project/**
      - path/to/Some.Project.Tests/**
      - path/to/Some.Dependency/**
      - '!**.md' # so that changes to documentation don't trigger tests
  workflow_dispatch:
```

### For publishing packages or services

For publishing packages or services, set up a trigger to be when a release is published

```yaml
on:
  release:
    types: 
      - published
  workflow_dispatch:
    inputs:
      version:
        description: 'Version'
        required: true
```

## Shared GitHub Action workflows

> Always use forward slashes for paths! These actions run on linux

### Running dotnet tests

To use the shared workflow for running dotnet tests, use the following job

```yaml
jobs:
  test:
    uses: firefly-trading-labs/.github/.github/workflows/tests.yaml@main
    with: 
      project-name: Some.Project.Tests
      test-project: path/to/Some.Project.Tests/Some.Project.Tests.csproj
    secrets:
      MYGETTOKEN: ${{secrets.MYGETTOKEN}}
```

### Publishing dotnet packages to MyGet

To use the shared workflow for publishing dotnet packages to MyGet, use the following job

```yaml
jobs:
  publish:
    uses: firefly-trading-labs/.github/.github/workflows/publish-package.yaml@main
    with: 
      project: path/to/Some.Project
      version: ${{github.event_name == 'workflow_dispatch' && github.event.inputs.version || github.event.release.tag_name }}
    secrets:
      MYGETTOKEN: ${{secrets.MYGETTOKEN}}
```