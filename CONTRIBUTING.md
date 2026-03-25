# Contributing to ci-workflows

## What this repo contains

This repository provides shared CI infrastructure for all repos in the organisation:

- **Reusable GitHub Actions workflows** - called by other repos via `workflow_call`
- **Dockerfile** for the `ros-ci:humble` container image used by ROS CI pipelines
- **Release workflow** for building variant Docker images on tagged releases

## How to modify workflows

1. Create a feature branch from `main`
2. Edit the relevant workflow YAML in `.github/workflows/`
3. Validate your YAML - use [actionlint](https://github.com/rhysd/actionlint) locally if possible
4. Open a PR with a clear description of what changed and why

### Key constraints

- All reusable workflows use `on: workflow_call` - they cannot be triggered directly (except via `workflow_dispatch` where configured)
- Input defaults should be sensible for the most common use case
- Avoid breaking changes to inputs - add new optional inputs instead
- Use concurrency groups to prevent duplicate runs

## How to test changes

- **Docker image changes**: Push to a branch, then manually trigger the `build-ci-image` workflow via `workflow_dispatch`
- **Reusable workflow changes**: Test in a caller repo by pointing the `uses:` reference to your branch (e.g., `calebjakemossey/ci-workflows/.github/workflows/ros-ci.yaml@your-branch`)
- **Release workflow changes**: Create a pre-release tag to verify the build matrix

## Docker image details

The `ros-ci:humble` image is based on `ros:humble` and includes:

- `python3-pip`
- `python3-vcstool` - for importing multi-repo workspaces
- `python3-colcon-common-extensions` - the standard ROS2 build tool
- `python3-rosdep` - dependency resolution (initialised and updated)
- `pytest` - Python test runner

The image is rebuilt automatically when the `Dockerfile` changes on `main` and pushed to `ghcr.io/calebjakemossey/ros-ci:humble`.

## PR requirements

- All PRs require at least one approving review from a code owner
- Fill in the PR template completely
- Ensure workflow YAML is valid
- Update the README if you change any workflow inputs or outputs
