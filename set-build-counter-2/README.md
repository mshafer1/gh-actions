# set-build-counter


The `mshafer1/gh-actions/set-build-counter` action sets an ever variable stored in a pipeline cache based 
on seed information.

By default, this action starts counting at 0.

> [!IMPORTANT]
> This action uses a shared pipeline cache as a convenience mechanism only.
> It is not an authoritative or integrity-protected source of truth and is not suitable for security-sensitive or repo-wide build accounting across parallel jobs.
>
> Use it only behind a strict workflow-level `concurrency` group and only when you accept cache-based eventual consistency.
> This variant is best-effort: it can work without a PAT, but it does not provide the same guarantees as a repo-scoped state store with explicit permissions.

## Usage

> [!NOTE]
> These examples use `@v0`, but pinning to a commit hash or full release tag is recommended for 
> build reproducibility and security.

```yaml

env:
  REPO_MAJOR_MIN: 1.0

permissions: {}

concurrency:
  group: build-counter
  cancel-in-progress: false

steps:
  - name: Check out repo
    uses: actions/checkout@1af3b93b6815bc44a9784bd300feb67ff0d1eeb3 # v6.0.0
  - uses: mshafer1/gh-actions/set-build-counter@v0
    id: set-build-counter
  - name: Use Output
    run: |
      echo "Build Counter Result: ${{ steps.set-build-counter.outputs.count }}"
```

## Inputs

### `seed`

```yaml
environment:
  REPO_MAJOR_MIN: 1.0

concurrency:
  group: build-counter
  cancel-in-progress: false

steps:
- uses: mshafer1/gh-actions/set-build-counter@v0
  id: set-build-counter
  with:
    seed: "${{ env.REPO_MAJOR_MIN }}"
- name: Use Output
  run: echo "Build Counter Result: ${{ steps.set-build-counter.outputs.count }}"
```

### `start-counter-at`

If you have previous builds and want the build counter to start at a value other then 0 (or for any other reason), specify it here.

```yaml
environment:
  REPO_MAJOR_MIN: 1.0

concurrency:
  group: build-counter
  cancel-in-progress: false

steps:
- uses: mshafer1/gh-actions/set-build-counter@v0
  id: set-build-counter
  with:
    start-counter-at: 100
- name: Use Output
  run: echo "Build Counter Result: ${{ steps.set-build-counter.outputs.count }}"
```

## Concurrency

Use a workflow or job `concurrency` group for the pipeline that calls this action when multiple jobs may update the same cache key at the same time:

```yaml
concurrency:
  group: build-counter
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: mshafer1/gh-actions/set-build-counter@v0
        id: set-build-counter
        with:
          seed: ${{ github.ref_name }}

      - name: Use counter
        run: echo "Build Counter Result: ${{ steps.set-build-counter.outputs.count }}"
```

This complements the action's internal lock and is the recommended way to keep the cache update path serialized.

## Outputs

### `count`

You can reference `count` for the updated counter value.
