# Skip Commit

Does the last commit message request contain a skip request?

## Usage

An example workflow showing how the following commit can be used to skip the `test` job.

```bash
chore: update documentation [skip test-job]
```

```yaml
name: Example workflow

on: pull_request

jobs:
  check-skip:
    runs-on: ubuntu-20.04
    outputs:
      should-skip: ${{ steps.skip-commit.outputs.should-skip == 'true' }}
    steps:
      # Checkout codebase triggered by commit
      - name: Checkout Codebase
        uses: actions/checkout@v2.3.4
        with:
          # Fetch depth is 2 so that the last pull
          # request commit can be read.
          fetch-depth: 2

      # Read the last commit message and check if
      # it contains the filter.
      - id: skip-commit
        name: Does the last commit message request contain a skip request?
        uses: domjtalbot/skip-commit@v1.0.1
        with:
          # The string filter to find in the
          # last commit.
          filter: '"\[skip test-job\]"'
  
  # An example test job that only runs when the last
  # commit message doesn't contain '[skip example-job]'.
  test:
    if: ${{ needs.check-skip.outputs.should-skip != 'true' }}
    needs: [check-skip]
    runs-on: ubuntu-20.04
    steps:
      - name: Tests
        runs: echo Running tests...
```