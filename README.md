# js-release-workflow

Github workflow to simplify creating a release for a Node.js action. This will automatically increment the version of a tag (ex: v1.1 -> v1.2, v2 -> v2.1) and set the major version to the latest (ex: v2, v3).

New releases are cut from the main (or master) branch. When creating a release branch PR we install all `node_modules` and execute a build. A draft release will also be created if any branch with the prefix `releases/` is pushed to.

This was created for the specific workflow of two digit release names (first number for major / breaking improvements and 2nd number for smaller changes). Major releases are stored on a `releases/v1` branch with the number corresponding to the major version. Modifying this workflow to support other workflows would be trivial.

Previously, to create a release for a Node.js project, developers had to:

- Create a new branch (if a new major version)
- Remove node_modules and remove them from .gitignore
- Remove lib from .gitignore
- Run `npm install --no-bin-links`
- Run the build
- Push to the new branch
- Check which tag should be used
- Create a tag
- Create the release
- Change the major version release to updated

These workflows automate the process.

## Installation

Add the following two workflow files to your `.github/workflows` folder for a [reusable workflow](https://docs.github.com/en/actions/using-workflows/reusing-workflows).

```yaml
# .github/workflows/release-pr.yml
name: Create release PR

on:
  workflow_dispatch:
    inputs:
      release:
        description: "Define release major version (ex: v1, v2, v3)"
        required: true

jobs:
  release-pr:
    uses: mostafahussein/js-release-workflow/.github/workflows/release-pr.yml@main
    with:
      release: ${{ github.event.inputs.release }}
```

and

```yaml
# .github/workflows/tag-and-draft.yml
name: Tag and create release draft

on:
  push:
    branches:
      - releases/*

jobs:
  tag-and-release:
    uses: mostafahussein/js-release-workflow/.github/workflows/tag-and-release.yml@main
```

## Usage

When you want to create a release follow these steps:

- Go to the `Actions` tab of your Github repository
- Select `Create release PR` on the left side
- Select `Run workflow` then enter your release version (ex: v1, v2, v3)
- Find the created pull request and approve it (it may take a minute for the workflow to create the new release)
- Go to `Releases` then edit the created releases (edit title, add release notes) and publish them

Note: Workflows won't be run on PRs triggered by other actions. Close the PR then reopen it if you want workflows to be run.

## Credits

- [Oliver King](https://github.com/OliverMKing)
