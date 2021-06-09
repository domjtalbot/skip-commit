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
  skip-commit:
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

      # Does the last commit message contain a skip request?
      - id: skip-commit
        name: Skip Commit
        uses: domjtalbot/skip-commit@v1.0.1
        with:
          # The string filter to find in the
          # last commit.
          filter: '"\[skip test-job\]"'
  
  # A test job that only runs when the last commit
  # message doesn't contain '[skip example-job]'.
  test:
    if: ${{ needs.skip-commit.outputs.should-skip != 'true' }}
    needs: [skip-commit]
    runs-on: ubuntu-20.04
    steps:
      - name: Tests
        runs: echo Running tests...
```