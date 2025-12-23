# build-counter


The `mshafer1/gh-actions/build-counter` action sets an ever incrementing repo variable based 
on seed information.

By default, this action starts counting at 0

## Usage

> [!NOTE]
> These examples use `@v0`, but pinning to a commit hash or full release tag is recommended for 
> build reproducibility and security.

```yaml

env:
  REPO_MAJOR_MIN: 1.0
  GH_TOKEN: ${{ github.token }}

permissions:
  contents: read

steps:
 
```

## Inputs

### `seed`

```yaml
environment:
  REPO_MAJOR_MIN: 1.0

steps:
- uses: mshafer1/gh-actions/build-counter@v0
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

steps:
- uses: mshafer1/gh-actions/build-counter@v0
  id: set-build-counter
  with:
    start-counter-at: 100
- name: Use Output
  run: echo "Build Counter Result: ${{ steps.set-build-counter.outputs.count }}"
```

## Outputs

### `count`

You can reference `count` for the updated counter value.
