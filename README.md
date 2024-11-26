# streamx-common-github-actions

This repository contains a GitHub actions used in StreamX repositories

## Create JIRA Release GitHub Action

This GitHub action provides an automated way to create JIRA releases by adding release version into `fix versions` filed in JIRA issue.
The action retrieves tasks numbers from commits since last release.
For each task adds version from github tag preceded by repository unique prefix. 
It is designed to run after your code has been checked out in previous workflow steps.

### Inputs

This action requires the following inputs:

* `atlassianCloudUser`: Your Atlassian cloud user. This is required.
* `atlassianCloudApiKey`: Your Atlassian cloud ApiKey. This is required.
* `atlassianCloudDomain`: Your Atlassian cloud domain. This is required.
* `atlassianCloudJiraProject`: Your JIRA project. This is required.
* `releaseNamePrefix`: Your JIRA release name prefix. This is required.
* `tag`: This is an optional parameter. If provided, it will be used, otherwise, the latest git tag will be used.

### Example Usage

add following step to your GH workflow.

```yaml
      - name: Release Jira Version
        uses: streamx-dev/streamx-common-github-actions/.github/actions/jira-release@main
        with:
          atlassianCloudUser: ${{ secrets.ATLASSIAN_CLOUD_USER }}
          atlassianCloudApiKey: ${{ secrets.ATLASSIAN_CLOUD_APIKEY }}
          atlassianCloudDomain: ${{ secrets.ATLASSIAN_CLOUD_DOMAIN }}
          atlassianCloudJiraProject: ${{ vars.ATLASSIAN_CLOUD_JIRA_PROJECT }}
          releaseNamePrefix: ${{ vars.JIRA_RELEASE_NAME_PREFIX }}
```

The secrets ATLASSIAN_CLOUD_USER ATLASSIAN_CLOUD_APIKEY ATLASSIAN_CLOUD_DOMAIN are defined on StreamX-dev organization level.
Should be available for all repos. 

Variable ATLASSIAN_CLOUD_JIRA_PROJECT is also defined on organization level. 

Variable JIRA_RELEASE_NAME_PREFIX should be defined as unique value for each repository.
