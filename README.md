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
  - [`tag`](#tag)
  - [`tag-prefix`](#tag-prefix)
- [Outputs](#outputs)
  - [`tag`](#tag-1)
  - [`url`](#url)
- [Related](#related)
- [Contribute](#contribute)

## What is this?

This is a simple action for creating GitHub release URLs.

## Use

**TODO**: use

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

### `tag`

The release tag or version to create a URL for.

### `tag-prefix`

The prefix to append to the [release version](#tag) (optional).

## Outputs

### `tag`

The release tag (including [`inputs.tag-prefix`](#tag-prefix) if specified).

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
