# set-build-counter-2

The `mshafer1/gh-actions/set-build-counter-2` action looks for a previous `build-counters` artifact from the same workflow, starts from `start-counter-at` if none exists, increments the requested counter value, and uploads the resulting `counters.json` as a `build-counters` artifact for the next run.

By default, this action starts counting at 0.

> [!IMPORTANT]
> This action finds the previous run via the GitHub Actions API and then uses `actions/download-artifact` to restore the artifact contents. Use a workflow `permissions` block that allows `actions: read` for the token used by the runner, and consider a `concurrency` group if multiple jobs may trigger the action at the same time.

## Usage

> [!NOTE]
> These examples use `@v0`, but pinning to a commit hash or full release tag is recommended for build reproducibility and security.

```yaml
name: build

on:
  workflow_dispatch:

permissions:
  actions: read
  contents: read # Note, not needed on public repos

concurrency:
  group: build-counter
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Check out repo
        uses: actions/checkout@v7.0.1
      - uses: mshafer1/gh-actions/set-build-counter-2@v0
        id: set-build-counter
      - name: Use Output
        run: |
          echo "Build Counter Result: ${{ steps.set-build-counter.outputs.count }}"
```

## Inputs

### `seed`

This identifies the counter bucket to update. For example, a branch or release label such as `release-1.0`.

```yaml
steps:
  - uses: mshafer1/gh-actions/set-build-counter-2@v0
    id: set-build-counter
    with:
      seed: "${{ github.ref_name }}"
```

### `start-counter-at`

If no prior `build-counters` artifact is found for the same workflow, the action starts from this value instead.

```yaml
steps:
  - uses: mshafer1/gh-actions/set-build-counter-2@v0
    id: set-build-counter
    with:
      start-counter-at: 100
      seed: "release-1.0"
```

## Artifact behavior

The action does the following each run:

1. Looks for the latest artifact named `build-counters` produced by an earlier run in the same workflow.
2. If it is found, it restores `counters.json` and uses it as the basis for the next increment.
3. If it is not found, it starts from `start-counter-at` (default `0`).
4. Writes the updated counters to `counters.json` and uploads it again as an artifact named `build-counters`.

This avoids the branch restriction of GitHub Actions cache keys while still allowing the counter to carry forward across workflow runs.

## Concurrency

When multiple jobs in a workflow can call this action simultaneously, keep them behind a workflow or job `concurrency` group so that they do not race against each other while downloading and uploading the shared artifact.

```yaml
concurrency:
  group: build-counter
  cancel-in-progress: false
```

## Outputs

### `count`

You can reference `count` for the updated counter value.
