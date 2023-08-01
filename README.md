# Actions

This repository centralize all Github actions workflows used by @snapshot-labs, for easier reuse, maintenance and reduce code duplication.

## Usage

### Lint

This workflow trigger the lint (`yarn lint`) and typecheck (`yarn typecheck`) tasks.

Install it in your project by creating the file `.github/workflows/lint.yml`, with the following content 

```yaml 
name: Lint
on: [push]
jobs:
  lint:
    uses: snapshot-labs/actions/.github/workflows/lint.yml@main
```

If you want to run it on different node versions 

```yaml 

```yaml 
name: Lint
on: [push]
jobs:
  lint:
  	strategy:
      matrix:
        target: ['16', '18', '20'] # Set your desired node versions (Default is 16)
    uses: snapshot-labs/actions/.github/workflows/lint.yml@main
    with:
      target: ${{ matrix.target }}
```

### Test

This workflow trigger the test suite (`yarn test`), and upload the test coverage to codecov.

**Ensure your test command is creating the test coverage report.**

```yaml 
name: Test
on: [push]
jobs:
  test:
    uses: snapshot-labs/actions/.github/workflows/test.yml@main
```

You can optionally test on multiple node versions, and setup a storage

```yaml 
name: Test
on: [push]
jobs:
  test:
    strategy:
      matrix:
        target: ['16', '18', '20'] # Set your desired node versions (Default is 16)
    uses: snapshot-labs/actions/.github/workflows/test.yml@main
    with:
      target: ${{ matrix.target }}
      # Setup MySQL database
      mysql_database_name: mydb_test
      mysql_schema_path: ./schema.sql # Default to 'src/helpers/schema.sql'
```

### Create a sentry release

This workflow trigger a task to upload the sourcemaps to Sentry, then create a Sentry release.

**Ensure your build task (`yarn build`) is configured to output sourcemaps.**


Install it in your project by creating the file `.github/workflows/lint.yml`, with the following content 

```yaml
name: Create a Sentry release
on:
  push:
    branches:
      - 'main' # or master, depending on your repo
jobs:
  create-sentry-release:
    uses: snapshot-labs/actions/.github/workflows/create-sentry-release.yml@main
    with:
      project: YOUR-SENTRY-PROJECT-NAME
      # You can optionally add the following line to set the node version (Default is 16)
      # target: '16'
```

## Notes

- [Github docs about reused worflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows)
- Environement variables can not be passed to the called workflow
