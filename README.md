# put-built-npm-package-contents-on-branch

An action to build an npm package and push its contents to a branch.

**Note:** This action assumes there is only one tarball in the working-directory
after the `pack` command. It will not work as expected with any others in the 
directory.

## Getting started 

To use this action you can create a new workflow and copy the following into it: 

```yaml
# .github/workflows/push-dist.yml

name: Push dist

on:
  push:
    branches:
      - main
      - master

jobs:
  push-dist:
    name: Push dist
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@v3
      - run: npm run build # Add whatever you need to build your package here
      - uses: kategengler/put-built-npm-package-contents-on-branch@v1.0.0
        with:
          branch: ${{ github.head_ref || github.ref_name }}-dist
          token: ${{ secrets.GITHUB_TOKEN }}
```

If you're working with a fork and you want to publish your work for testing you can update the workflow to build on all pushes to all branches: 

```yaml
on:
  push: {}
```

## Inputs

### `token` (required)

**Description:** A GitHub token with write access to the repository contents. 
Either a personal access token or `${{ secrets.GITHUB_TOKEN }}` if write access 
for that default token has been enabled in the repository settings.

### `working-directory` (default: `'./'`)

**Description:** The directory to run the pack command in. Should be the directory
that contains the package you want to publish to the branch.

### `pack-command` (default: `npm pack`)

**Description:** The command to generate the package tarball.

### `branch` (default: `dist`)

**Description:** The branch to push the package contents to.

### `tmp-dir` (default: `./tmp/dist-action`)

**Description:** Empty directory where the package tarball will be extracted. 
This directory will be created if it does not exist. It will be deleted after
the action is complete.

### `commit-message` (default: `"Publish dist for ${{ github.sha }}"`)

**Description:** The commit message to use when pushing the built package to the branch.

### `commit-author` (default: `github-actions[bot]`)

**Description:** The author to use when pushing the built package to the branch.

### `commit-email` (default: `github-actions[bot]@users.noreply.github.com`)

**Description:** The email to use when pushing the built package to the branch.

### `omit-scripts` (default: `prepack,postpack`)

**Description:** Comma separated list of scripts to omit from `package.json`
after packing, or "*" to omit everything.

**Why is this needed?** Package managers differ in opinion on what scripts to
run when installing dependencies from Git. Some package managers do nothing at
all, and some choose to run `pack` command and the `prepack`/`postpack` scripts
along with it.

A common reason for using this workflow is to push the already-packed content
to a dist branch in order to support package manages that does not run the
`pack` script when installing a Git dependency.

However, on package managers that runs the `pack` command, they may encounter
issues when installing from the dist branch. For example, the Ember's v2 addons
has `"prepack": "rollup --config"`, but since the rollup config file is omitted
from the packed tarball, the command will fail.

Therefore, by default, we omit the `prepack` and `postpack` scripts from the
resulting `package.json` before pushing, in order to normalize this difference
across package managers. Running these scripts should be entirely unnecessary
when installing from the dist branch.

You can opt-out of this behavior by setting `omit-scripts` to an empty string.
