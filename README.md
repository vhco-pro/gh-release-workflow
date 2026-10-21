# gh-release-workflow

Reusable workflow that versions a repository with GitVersion and publishes the
GitHub Release for the exact tag. It is for repositories whose Git tag is the
release, such as GitHub Actions and reusable workflows.

It composes three actions and adds no logic of its own:

1. `actions/checkout` with full history and tags.
2. [`michielvha/gitversion-tag-action`](https://github.com/michielvha/gitversion-tag-action)
   calculates the version and pushes the exact `vX.Y.Z` tag when it is new.
3. [`vhco-pro/gh-release-action`](https://github.com/vhco-pro/gh-release-action)
   renders git-cliff notes and creates the GitHub Release.

## Adopt it

Copy [examples/action-repo](examples/action-repo) into the repository's
`.github/workflows/`, replace `FULL_COMMIT_SHA` with a released commit of this
repository, and put the repository's checks in `test.yml`.

The example runs the tests first and the release only after they pass. Once a
tag exists, consumers can use it, so a tag must never be created for a commit
that failed its checks. PRs and manual runs default to a dry run: the version is
calculated and the notes are rendered into the job summary, nothing is created.

## Contract

Inputs, outputs and permissions are defined in
[gh-release.yml](.github/workflows/gh-release.yml): `runs-on` and `dry-run` in;
`version`, `tag`, `tagged` and `release-url` out. The caller job grants
`contents: write`.

A run that computes an already tagged version publishes the release if an
earlier run tagged it but failed to publish. An existing release is never
changed.

## This repository

[release.yml](.github/workflows/release.yml) releases this repository with its
own workflow by local path: a dry run at the commit first, then the real run on
`main`. A change that breaks the workflow fails its own release before any
consumer can pin it.

To lint the example as a consumer would use it, copy the folder into a scratch
repository's `.github/workflows/` and run `actionlint`.
