# streamx-common-github-actions

This repository contains a GitHub actions used in StreamX repositories

## Create JIRA Release GitHub Action


This GitHub action is designed to automate the creation of JIRA releases. 
It operates by adding release version details to the `fix versions` field in a JIRA issues, 
driven by task numbers derived from commits since the previous git tag. 
The action should be set up to run subsequent to code checkouts in the earlier workflow steps.

### Inputs

This action requires the following inputs:

* `atlassianCloudUser`: Your Atlassian cloud user. This is required.
* `atlassianCloudApiKey`: Your Atlassian cloud ApiKey. This is required.
* `atlassianCloudDomain`: Your Atlassian cloud domain. This is required.
* `atlassianCloudJiraProject`: Your JIRA project. This is required.
* `releaseNamePrefix`: Your JIRA release name prefix. This is required.
* `tag`: This is an optional parameter. If provided, it will be used, otherwise, the latest git tag will be used.

### Usage

add following step to your GH workflow.

```yaml
      - name: Release Jira Version
        uses: streamx-dev/streamx-common-github-actions/.github/actions/jira-release@main
        with:
          atlassianCloudUser: ${{ secrets.ATLASSIAN_CLOUD_USER }}
          atlassianCloudApiKey: ${{ secrets.ATLASSIAN_CLOUD_APIKEY }}
          atlassianCloudDomain: ${{ secrets.ATLASSIAN_CLOUD_DOMAIN }}
          atlassianCloudJiraProject: ${{ vars.ATLASSIAN_CLOUD_JIRA_PROJECT }}
          releaseNamePrefix: ${{ github.event.repository.name }}
```

Secrets `ATLASSIAN_CLOUD_USER`, `ATLASSIAN_CLOUD_APIKEY`, and `ATLASSIAN_CLOUD_DOMAIN` are defined at the StreamX-dev organization level 
and should be accessible for all repositories without any additional configuration.

The `ATLASSIAN_CLOUD_JIRA_PROJECT` variable is also defined at the organizational level. 


## Create StreamX Connector GitHub Action

StreamX Connector GitHub is a Quarkus GitHub Action project that allows syncing GitHub
changes with StreamX.

Detail information please check [Quarkus GitHub Action](https://docs.quarkiverse.io/quarkus-github-action/dev/index.html) documentation.

### Actions
* WebResourceAction - This action checks your repository under $GITHUB_WORKSPACE, if there are any
  git modifications in files, that are configured for monitoring in variable `STREAMX_INGESTION_WEBRESOURCE_INCLUDES`.
  Additionaly by using `Run workflow` button with `Publish all` option checked we trigger full
  sync of the repository with StreamX.

### Usage WebResourceAction

#### Prerequisites

* job execution has if constraint that allows only pull request closed and manual workflow dispatch  
  executions, ie:
```yaml
    if: github.event.pull_request.merged == true || (github.event_name == 'workflow_dispatch' && inputs.publish_all_webresources == true)
```
* working directory has already checkout code base, with defined fetch-depth option set to 0 (option required for git diff detection)
```yaml
    - name: Checkout code
      uses: actions/checkout@v4
      with:
        fetch-depth: 0
```

#### Usage
<!-- start usage -->
```yaml
- name: Run sync with StreamX
  uses: streamx-dev/streamx-common-github-actions/.github/actions/connector-github@main
  with:
    # Name of the action with 'webresource_' prefix. Supported event names are [pull_request, workflow_dispatch]
    action: "webresource_${{github.event_name}}"
    
    # A secret parameter with auth token used for communication with StreamX ingestion API. 
    # Value required.
    streamx-ingestion-token: ${{ secret.STREAMX_INGESTION_TOKEN }}

     # A variable parameter with StreamX ingestion api endpoint URL. 
     # Example: https://ingestion.mystreamx.site
     # Value required.
    streamx-ingestion-url: ${{ vars.STREAMX_INGESTION_URL }}

    # A variable parameter list of paths that are included in StreamX webresources syncing processing.
    # Check mechanism is based on ant path matcher code base [details ref java.nio.file.PathMatcher]. 
    # Example: ["scripts/*.js", "styles/*.css"]
    # Value required.    
    streamx-ingestion-webresource-includes: ${{ vars.STREAMX_INGESTION_WEBRESOURCE_INCLUDES }}
```
<!-- end usage -->
#### Example workflow
```yaml
name: Publish/Unpublish web resources on StreamX

on:
  workflow_dispatch:
    inputs:
      publish_all_webresources:
        description: "Publish all pattern included webresources to StreamX"
        required: false
        type: boolean
        default: false
  pull_request:
    types:
      - closed
    branches:
      - main

jobs:
  sync-with-streamx:
    if: github.event.pull_request.merged == true || (github.event_name == 'workflow_dispatch' && inputs.publish_all_webresources == true)
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        with:
          fetch-depth: 0
      - name: Run sync with StreamX
        uses: streamx-dev/streamx-common-github-actions/.github/actions/connector-github@main
        with:
          action: "webresource_${{github.event_name}}"
          streamx-ingestion-token: ${{ secrets.STREAMX_INGESTION_TOKEN }}
          streamx-ingestion-url: ${{ vars.STREAMX_INGESTION_URL }}
          streamx-ingestion-webresource-includes: ${{ vars.STREAMX_INGESTION_WEBRESOURCE_INCLUDES }}
```

### Releasing

Please follow [Quarkus GitHub Action](https://docs.quarkiverse.io/quarkus-github-action/dev/push-to-production.html) documentation recommendation steps.