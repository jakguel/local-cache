# Local Cache action

This is a fork of (https://github.com/corca-ai/local-cache) with updates an improvements.

This action allows caching dependencies and build outputs to improve workflow execution time on self hosted machine.
Artifacts are cached in the /home/ubuntu/.cache by default.

## What's New

Paths can be relative to the working directory or absolute.
You can now use this to cache any directory, not just the ones in the workspace.

See the [v1 README.md](https://github.com/corca-ai/local-cache/blob/v1/README.md) for older updates.

## Usage

### Pre-requisites

Create a workflow .yml file in your repository's .github/workflows directory.

### Inputs

| Parameter   | Required/Optional | Description                                                                                                            | Default             |
| ----------- | ----------------- | ---------------------------------------------------------------------------------------------------------------------- | ------------------- |
| `path`      | **Required**      | A directory to save and restore cache.                                                                                 | -                   |
| `key`       | **Required**      | An explicit key for a cache.                                                                                           | -                   |
| `base`      | **Optional**      | A base directory to save and restore cache.                                                                            | /home/ubuntu/.cache |
| `clean-key` | **Optional**      | If set, caches that have not been accessed over 7 days are cleaned up automatically at post action stage by clean-key. | -                   |

### Outputs

| Output      | Description      |
| ----------- | ---------------- |
| `cache-hit` | Cache hit or not |

## Example cache workflow

**Restoring and saving cache using a single action**

```yaml
name: Caching Primes

on: push

jobs:
  build:
    runs-on: [self-hosted]

    steps:
      - uses: actions/checkout@v3

      - name: Cache dependencies
        id: cache
        uses: corca-ai/local-cache@v2
        with:
          path: path/to/dependencies
          key: ${{ hashFiles('**/lockfiles') }}

      - name: Install dependencies
        if: steps.cache.outputs.cache-hit != 'true'
        run: /install.sh

      - name: Use dependencies
        run: /run.sh
```

## Cache Limits

Cache limits are defined by the disk space of self-hosted runners. Caches that have not been accessed over 7 days can be cleaned up at post action stage by `clean-key`, but if you still run out of disk space, you need to manage runners by increasing disk capacity or removing cache on your own.
