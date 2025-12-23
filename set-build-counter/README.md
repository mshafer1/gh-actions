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

permissions:
  contents: read

steps:
  - name: Check out repo
    uses: actions/checkout@1af3b93b6815bc44a9784bd300feb67ff0d1eeb3 # v6.0.0
  - uses: ./set-build-counter
    id: set-build-counter
    with:
      GH_TOKEN: ${{ secrets.GH_PAT }} # default GITHUB_TOKEN has no permission to set repo variables, must be a PAT that has variable write access
  - name: Use Output
    run: |
      echo "Build Counter Result: ${{ steps.set-build-counter.outputs.count }}"
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
