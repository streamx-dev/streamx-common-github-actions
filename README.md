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
