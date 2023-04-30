
<!-- action-docs-description -->
## Description

Action to deploy a preview GitHub Pages for each branch, pull request, commit
<!-- action-docs-description -->
<!-- action-docs-inputs -->
## Inputs

| parameter | description | required | default |
| --- | --- | --- | --- |
| token | The GitHub App token or personal access token to access GitHub Pages publishing repository and commenting to the pull requests and branches. Defaults to the repository scoped GitHub token (Which means that the repository you want to deploy GitHub Pages is the same one as you are running your workflow).  | `true` | ${{ github.token }} |
| source-dir | Path of the directory you want to deploy. | `true` |  |
| target-repository | The repository you want to deploy GitHub Pages to, in the format of 'rajyan/preview-pages'. Defaults to the current repository you are running the workflow.  | `false` | ${{ github.repository }} |
| target-branch | The branch you want to deploy GitHub Pages to. | `false` | gh-pages |
| target-dir | The directory you want to deploy in the GitHub Pages repository. Defaults the root directory.  | `false` | / |
| configured-dir | The directory configured in GitHub Pages repository as a source. (ex. /docs) | `false` | / |
| configured-domain | The custom domain configured in GitHub Pages repository. | `false` |  |
| git-config-name | Username of the commit when deploying to the GitHub Pages publishing repository. | `false` | github-actions[bot] |
| git-config-email | Email of the commit when deploying to the GitHub Pages publishing repository. | `false` | 41898282+github-actions[bot]@users.noreply.github.com |
| pr-per-commit | Whether to keep different pages per each commit SHA or overwrite per each pull request. If 'true' the deploy directory for pull request will be '{{ inputs.target-dir }}/pr-{{ github.event.number }}/{{ github.sha }}'. If 'false' the directory will be '{{ inputs.target-dir }}/pr-{{ github.event.number }}' and overwrites on each commit.  | `false` | true |
| pr-comment | Whether to comment the preview page url in a pull request and how. Valid choices are 'each' which posts a comment on each commit, 'append', 'sticky', 'recreate', 'hide_and_recreate' see https://github.com/marocchino/sticky-pull-request-comment for the options, or 'none' if you don't need the comment.  | `false` | each |
| branch-per-commit | Whether to keep different pages per each commit SHA or overwrite per each branch. If 'true' the deploy directory for pull request will be '{{ inputs.target-dir }}/{{ github.ref_name }}/{{ github.sha }}'. If 'false' the directory will be '{{ inputs.target-dir }}/{{ github.ref_name }}' and overwrites on each commit.  | `false` | true |
| branch-comment | Whether to comment the preview page url on the branches commit. Valid options are 'each' or 'none'.  | `false` | each |
<!-- action-docs-inputs -->
<!-- action-docs-outputs -->
## Outputs

| parameter | description |
| --- | --- |
| pages-url | The deployed Github Pages url |
| upload-dir | The deployed directory under {{inputs.target-dir}} |
<!-- action-docs-outputs -->
<!-- action-docs-runs -->
## Runs

This action is a `composite` action.
<!-- action-docs-runs -->
