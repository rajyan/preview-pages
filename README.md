
<!-- action-docs-description -->
## Description

Action to deploy a preview GitHub Pages for each branch, pull request, commit.
<!-- action-docs-description -->
This Action enables you to, for example, create previews of SPA for each branch,
and generate separate coverage reports for each pull request and commit.

## Usage

```yaml
# pull_request and push event is supported.
on:
  pull_request:
  push:
    branches:
      - main

# contents: write is required for deploying the GitHub Pages if using the default GITHUB_TOKEN
# pull-requests: write is required if you want to comment the preview page url to the pull request
permissions:
  pull-requests: write
  contents: write

# recommended to set a concurrency group
concurrency:
  group: preview-pages-${{ github.ref }}
  cancel-in-progress: true

jobs:
  run:
    steps:
      - name: Checkout
        uses: actions/checkout@v3

      # run some build steps here and export the result to a directory

      - name: Preview Pages
        uses: rajyan/preview-pages@v1
        with:
          source-dir: test
```

with this default configuration, the action would generate a preview page for each commit,
and comments the preview page url 

on the pull request with pull request events

example
https://github.com/rajyan/preview-pages/pull/3#issuecomment-1530952983

and on the branch commit with push events

example
https://github.com/rajyan/preview-pages/commit/df68e1415132b40181e1141e1daba89e216fb927#commitcomment-111425894

## Examples

### GitHub pages with custom domain or directories

You can set a custom domain and directory if you have a custom GitHub Pages setup.
For example, in the below, the workflow would deploy pages under `docs/preview-pages`
and the pages will be hosted from `docs` with a custom domain,
which results as a page url of `https://test.rajyan.net/preview-pages/{sub-dir}`

```yaml
  - name: Preview Pages
    uses: rajyan/preview-pages@v1
    with:
      token: ${{ steps.generate-token.outputs.token }}
      target-dir: docs/preview-pages
      configured-domain: test.rajyan.net
      configured-dir: docs
```

### Cross repository deployment

Set the target repository with an appropriate token that has the permission to write to the target repository.
Creating a token with GitHub App is recommended,
because it can set permissions with finer granularity and are scoped to only repositories where the App is installed.

```yaml
  - name: Generate token
    id: generate-token
    uses: tibdex/github-app-token@b62528385c34dbc9f38e5f4225ac829252d1ea92 #1.8.0
    with:
      app_id: ${{ secrets.RAJYAN_BOT_APP_ID }}
      private_key: ${{ secrets.RAJYAN_BOT_PRIVATE_KEY }}

  - name: Preview Pages
    uses: rajyan/preview-pages@v1
    with:
      token: ${{ steps.generate-token.outputs.token }}
      source-dir: test
      target-repository: rajyan/coverage-report
```

### Cleaning up pull request previews

Set up a workflow on pull_request closed event to delete the pull request previews if you want.
https://github.com/rajyan/preview-pages/blob/d99bc0116bc9d67b9aeb1fefd4f2b24f725c1eee/.github/workflows/cleanup-pr-preview.yml#L25-L37

<!-- action-docs-inputs -->
## Inputs

| parameter | description | required | default |
| --- | --- | --- | --- |
| source-dir | Path of the directory you want to deploy. This is the only required input.  | `true` |  |
| token | The GitHub App token or personal access token to access GitHub Pages publishing repository and commenting to the pull requests and branches. Defaults to the repository scoped GitHub token (Which means that the repository you want to deploy GitHub Pages is the same one as you are running your workflow).  | `false` | ${{ github.token }} |
| target-repository | The repository you want to deploy GitHub Pages to, in the format of 'rajyan/preview-pages'. Defaults to the current repository you are running the workflow.  | `false` | ${{ github.repository }} |
| target-branch | The branch you want to deploy GitHub Pages to. | `false` | gh-pages |
| target-dir | The directory you want to deploy in the GitHub Pages repository. Defaults the root directory.  | `false` | . |
| configured-dir | The directory configured in GitHub Pages repository as a source. (ex. docs) | `false` | . |
| configured-domain | The custom domain configured in GitHub Pages repository. | `false` |  |
| git-config-name | Username of the commit when deploying to the GitHub Pages publishing repository. See https://github.com/JamesIves/github-pages-deploy-action#optional-choices  | `false` | github-actions[bot] |
| git-config-email | Email of the commit when deploying to the GitHub Pages publishing repository. See https://github.com/JamesIves/github-pages-deploy-action#optional-choices.  | `false` | 41898282+github-actions[bot]@users.noreply.github.com |
| clean | Whether to delete files in the destination that doesn't exist in source. See https://github.com/JamesIves/github-pages-deploy-action#optional-choices.  | `false` |  |
| clean-exclude | Files or paths to exclude from clean. See https://github.com/JamesIves/github-pages-deploy-action#optional-choices.  | `false` |  |
| force | Force-push on deployment. Default is false which is different from the original action. See https://github.com/JamesIves/github-pages-deploy-action#optional-choices.  | `false` | false |
| tag | Add a tag on commit. See https://github.com/JamesIves/github-pages-deploy-action#optional-choices.  | `false` |  |
| pr-per-commit | Whether to keep different pages per each commit SHA or overwrite per each pull request. If 'true' the deploy directory for pull request will be '{{ inputs.target-dir }}/pr-{{ github.event.number }}/{{ github.sha }}'. If 'false' the directory will be '{{ inputs.target-dir }}/pr-{{ github.event.number }}' and overwrites on each commit.  | `false` | true |
| pr-comment | Whether to comment the preview page url in a pull request and how. Valid choices are 'each' which posts a comment on each commit, 'append', 'sticky', 'recreate', 'hide_and_recreate' see https://github.com/marocchino/sticky-pull-request-comment for the options, or 'none' if you don't need the comment.  | `false` | each |
| branch-per-commit | Whether to keep different pages per each commit SHA or overwrite per each branch. If 'true' the deploy directory for pull request will be '{{ inputs.target-dir }}/{{ github.ref_name }}/{{ github.sha }}'. If 'false' the directory will be '{{ inputs.target-dir }}/{{ github.ref_name }}' and overwrites on each commit.  | `false` | true |
| branch-comment | Whether to comment the preview page url on the branches commit. Valid options are 'each' or 'none'.  | `false` | each |
<!-- action-docs-inputs -->
<!-- action-docs-outputs -->
## Outputs

| parameter | description |
| --- | --- |
| pages-url | The deployed GitHub Pages url |
| upload-dir | The deployed directory under {{inputs.target-dir}} |
<!-- action-docs-outputs -->
