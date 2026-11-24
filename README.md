# sync-oleander-notebooks

GitHub Action that keeps [oleander](https://oleander.dev) notebooks in sync with the `*.notebook.yaml` files in your repository. On pull requests it plans each changed notebook and writes the diff to the job summary. On other events it saves the changes and publishes them.

## Usage

Add an oleander API key as the repository secret `OLEANDER_API_KEY`, then:

```yaml
name: oleander notebooks
on:
  pull_request:
    paths: ["notebooks/**"]
  push:
    branches: [main]
    paths: ["notebooks/**"]

jobs:
  notebooks:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: OleanderHQ/sync-oleander-notebooks@v1
        with:
          api-key: ${{ secrets.OLEANDER_API_KEY }}
          path: notebooks
```

Start the repository with `oleander notebooks pull --all` (oleander-cli 0.28.0 or later).

## Inputs

| Name | Default | Description |
| --- | --- | --- |
| `api-key` | required | oleander API key. Notebooks are saved and published as this key's user. |
| `path` | `.` | Files or directories to search for `*.notebook.yaml`, one per line. |
| `mode` | `auto` | `plan` saves nothing, `sync` saves. `auto` plans on `pull_request` events and syncs otherwise. |
| `approve` | `true` | Apply changes that add outputs, a schedule, destinations or a budget. Merging the pull request is the approval. |
| `publish` | `true` | Publish the draft a save leaves on a live notebook. |
| `delete` | `false` | Delete the notebooks of `*.notebook.yaml` files the change removes, with their run history and result tables. Plans list them as "would delete". Without it, removed files are reported and the notebooks stay in oleander. |
| `base-ref` | PR base, or the commit before the push | Git ref to find removed files against. |
| `cli-version` | latest | Exact `oleander-cli` version to install (0.27.0 or later; 0.28.0 or later for `delete`). |

The step fails if any file is invalid, needs approval, changed in oleander while syncing, or fails to delete. Runs on Ubuntu runners, like [setup-oleander-cli](https://github.com/OleanderHQ/setup-oleander-cli).
