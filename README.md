# gh-actions

A place to aggregate my re-used GitHub Actions.


## `mshafer1/gh-actions/set-build-counter`


Provide template to increment a repo variable and return it for use during a workflow run.
See [`set-build-counter/`](set-build-counter/)

(uses a PAT to store the counters as repo variables)

## `mshafer1/gh-actions/set-build-counter-2`

Provide template to increment a repo variable and return it for use during a workflow run.
See [`set-build-counter-2/`](set-build-counter-2/)

(Uses workflow artifacts to preserve state. Does not require a PAT; however, will only retain for 90 days - or whatever GitHub has artifact retention set to)
