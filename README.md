# gh-release-url

[![github release](https://img.shields.io/github/v/release/flex-development/gh-release-url-action.svg?include_prereleases\&sort=semver)](https://github.com/flex-development/gh-release-url-action/releases/latest)
[![test](https://github.com/flex-development/gh-release-url-action/actions/workflows/test.yml/badge.svg)](https://github.com/flex-development/gh-release-url-action/actions/workflows/test.yml)
[![module type: esm](https://img.shields.io/badge/module%20type-esm-brightgreen)](https://github.com/voxpelli/badges-cjs-esm)
[![license](https://img.shields.io/github/license/flex-development/gh-release-url-action.svg)](LICENSE.md)
[![conventional commits](https://img.shields.io/badge/-conventional%20commits-fe5196?logo=conventional-commits\&logoColor=ffffff)](https://conventionalcommits.org)
[![yarn](https://img.shields.io/badge/-yarn-2c8ebb?style=flat\&logo=yarn\&logoColor=ffffff)](https://yarnpkg.com)

Create a URL for a GitHub release

## Contents

- [What is this?](#what-is-this)
- [Use](#use)
- [Inputs](#inputs)
  - [`owner`](#owner)
  - [`repo`](#repo)
  - [`server`](#server)
  - [`tag-prefix`](#tag-prefix)
  - [`version`](#version)
- [Outputs](#outputs)
  - [`tag`](#tag)
  - [`url`](#url)
- [Related](#related)
- [Contribute](#contribute)

## What is this?

This is a simple, but useful, action for creating GitHub release URLs.

## Use

```yaml
---
name: release
on:
  pull_request:
    branches:
      - main
    types:
      - closed
env:
  REF: ${{ github.event.pull_request.merge_commit_sha }}
  REF_NAME: ${{ format('{0}@{1}', github.base_ref, github.event.pull_request.merge_commit_sha) }}
jobs:
  preflight:
    if: github.event.pull_request.merged && startsWith(github.head_ref, 'release/')
    permissions:
      contents: read
    runs-on: ubuntu-latest
    outputs:
      tag: ${{ steps.environment.outputs.tag }}
      url: ${{ steps.environment.outputs.url }}
      version: ${{ steps.version.outputs.manifest }}
    steps:
      - id: checkout
        name: Checkout ${{ env.REF_NAME }}
        uses: actions/checkout@v5.0.0
        with:
          ref: ${{ env.REF }}
      - id: version
        name: Get release version
        uses: flex-development/manver-action@1.0.1
      - id: tag-prefix
        name: Get release tag prefix
        uses: flex-development/jq-action@1.0.0
        with:
          data: grease.config.json
          filter: .tagprefix
      - id: environment
        name: Get release url
        uses: flex-development/jq-action@2.0.0
        with:
          tag-prefix: ${{ steps.tag-prefix.outputs.result }}
          version: ${{ steps.version.outputs.manifest }}
  publish:
    needs: preflight
    runs-on: ubuntu-latest
    environment:
      name: release
      url: ${{ needs.preflight.outputs.url }}
    env:
      GITHUB_TOKEN: ${{ secrets.GH_REPO_TOKEN }}
      NOTES_FILE: RELEASE_NOTES.md
    steps:
      - id: checkout
        name: Checkout ${{ env.REF_NAME }}
        uses: actions/checkout@v5.0.0
        with:
          fetch-depth: 0
          persist-credentials: true
          ref: ${{ env.REF }}
          token: ${{ env.GITHUB_TOKEN }}
      - id: gpg-import
        name: Import GPG key
        uses: crazy-max/ghaction-import-gpg@v6.3.0
        with:
          git_config_global: true
          git_push_gpgsign: false
          git_tag_gpgsign: true
          git_user_signingkey: true
          gpg_private_key: ${{ secrets.GPG_PRIVATE_KEY }}
          passphrase: ${{ secrets.GPG_PASSPHRASE }}
          trust_level: 5
      - id: node
        name: Setup Node.js
        uses: actions/setup-node@v5.0.0
        with:
          cache: yarn
          cache-dependency-path: yarn.lock
          node-version-file: .nvmrc
      - id: yarn
        name: Install dependencies
        run: yarn --no-immutable && echo "$GITHUB_WORKSPACE/node_modules/.bin" >>$GITHUB_PATH
      - id: pack
        name: Pack project
        run: yarn pack -o %s-%v.tgz
      - id: release-notes
        name: Generate release notes
        env:
          TZ: ${{ vars.TZ }}
        run: grease changelog -wo $NOTES_FILE && echo "$(cat $NOTES_FILE)" >>$GITHUB_STEP_SUMMARY
      - id: tag
        name: Create annotated tag
        run: 'grease tag -ps -m "release: {tag}" ${{ needs.preflight.outputs.version }}'
      - id: publish
        name: Publish release
        run: |
          gh release create ${{ needs.preflight.outputs.tag }} *.tgz --title=${{ needs.preflight.outputs.tag }} --notes-file=$NOTES_FILE --verify-tag
```

## Inputs

### `owner`

> **default**: `${{ github.repository_owner }}`

The repository owner (optional).

### `repo`

> **default**: `${{ github.event.repository.name }}`

The name of the repository (optional).

### `server`

> **default**: `${{ github.server_url }}`

The URL of the GitHub server (optional).

### `tag-prefix`

The prefix to append to the [release version](#version) when building the release tag component of the URL (optional).

### `version`

The release version or tag to create a URL for.

## Outputs

### `tag`

The release tag the URL was created with.

### `url`

The release URL.

## Related

- [`flex-development/ghr-url-action`][ghr-url-action] — create a url for a github registry
- [`flex-development/npm-url-action`][npm-url-action] — create a url for the npm registry

## Contribute

See [`CONTRIBUTING.md`](CONTRIBUTING.md).

This project has a [code of conduct](./CODE_OF_CONDUCT.md). By interacting with this repository, organization, or
community you agree to abide by its terms.

[ghr-url-action]: https://github.com/flex-development/ghr-url-action

[npm-url-action]: https://github.com/flex-development/npm-url-action
