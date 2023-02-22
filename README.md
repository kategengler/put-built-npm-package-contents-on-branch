# put-built-npm-package-contents-on-branch

An action to build an npm package and push its contents to a branch.

**Note:** This action assumes there is only one tarball in the working-directory
after the `pack` command. It will not work as expected with any others in the 
directory.

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
