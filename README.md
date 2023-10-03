# Actions

This repository centralize all Github actions workflows used by @snapshot-labs, for easier reuse, maintenance and reduce code duplication.

## Usage

### Lint

This workflow trigger the lint and typecheck tasks.

#### Pre-requisites

- Your lint task is ran with `yarn lint`
- Your typescript check task is ran with `yarn typecheck`

#### Usage

Install it in your project by creating the file `.github/workflows/lint.yml`, with the following content 

```yaml 
name: Lint

on: [push]

jobs:
  lint:
    uses: snapshot-labs/actions/.github/workflows/lint.yml@main
    secrets: inherit
```

### Test

This workflow trigger the test suite, and upload the test coverage to codecov.

#### Pre-requisites

- Your test suite is ran with `yarn test`
- A coverage report should be produced at the end of the test suite

#### Usage

Install it in your project by creating the file `.github/workflows/test.yml`, with the following content 

```yaml 
name: Test

on: [push]

jobs:
  test:
    uses: snapshot-labs/actions/.github/workflows/test.yml@main
    secrets: inherit
```

You can optionally setup a storage

```yaml 
name: Test
on: [push]
jobs:
  test:
    uses: snapshot-labs/actions/.github/workflows/test.yml@main
    secrets: inherit
    with:
      # Setup MySQL database
      mysql_database_name: mydb_test
      mysql_schema_path: ./schema.sql # Default to 'src/helpers/schema.sql'
      # Setup redis
      redis: true
```

### Create a sentry release

This workflow trigger a task to upload the sourcemaps to Sentry, then create a Sentry release.

#### Pre-requisites

- Your build task is ran with `yarn build`
- Your build task (`yarn build`) is configured to output sourcemaps

Your tsconfig should contains the following properties:

```json
{
  "compilerOptions": {
    "sourceMap": true,
    "inlineSources": true,

    // Set `sourceRoot` to  "/" to strip the build path prefix from
    // generated source code references. This will improve issue grouping in Sentry.
    "sourceRoot": "/"
  }
}
```

See more on Sentry documentation: https://docs.sentry.io/platforms/node/sourcemaps/uploading/typescript/

#### Usage

Install it in your project by creating the file `.github/workflows/create-sentry-release.yml`, with the following content 

```yaml
name: Create a Sentry release
on:
  push:
    branches:
      - 'main' # or master, depending on your repo
jobs:
  create-sentry-release:
    uses: snapshot-labs/actions/.github/workflows/create-sentry-release.yml@main
    secrets: inherit
    with:
      project: YOUR-SENTRY-PROJECT-NAME
      # You can optionally add the following line to set the node version (Default is 16)
      # target: '16'
```

### Bump the package.json version and create a tag

This workflow trigger a task to bump the package.json version, and creates a tag with the newly created version.
Commits message will be read to determine the version bump type.

#### Usage

Install it in your project by creating the file `.github/workflows/bump-version.yml`, with the following content 

```yaml 
name: Bump version

on:
  push:
    branches:
      - 'main' # or master, depending on your repo

jobs:
  bump-version:
    uses: snapshot-labs/actions/.github/workflows/bump-version.yml@main
    secrets: inherit
```

### Create a Github release

This workflow trigger a task to create a github release

#### Usage

Install it in your project by creating the file `.github/workflows/create-github-release.yml`, with the following content 

```yaml 
name: Create Github release

on:
  push:
    tags:
      - 'v*.*.*'

jobs:
  create-github-release:
    uses: snapshot-labs/actions/.github/workflows/create-github-release.yml@main
    secrets: inherit
```


### Publish a NPM package

This workflow trigger a task to publish a package to NPM.

#### Pre-requisites

- The publish task requires the `NODE_AUTH_TOKEN` env variable. It should be set in the Github secrets as `NPM_TOKEN`.

#### Usage

Install it in your project by creating the file `.github/workflows/publish-npm.yml`, with the following content 

```yaml 
name: Publish NPM package

on:
  release:
    types: [created]

jobs:
  publish-npm:
    uses: snapshot-labs/actions/.github/workflows/publish-npm.yml@main
    secrets: inherit
```

### Release

This workflow triggers an aggregation of jobs/workflows, to publish a pull request to NPM. 

This task will trigger the following jobs in order:

- bump-version: to bump the version in package.json, and create a new tag
- create-github-release: create a new github release associated to the new tag
- publish-npm: publish the new github release to NPM

#### Pre-requisites

- View requirements for each workflow

#### Usage

Install it in your project by creating the file `.github/workflows/release.yml`, with the following content 

```yaml 
name: Release

on:
  push:
    branches:
      - 'master'

jobs:
  release:
    uses: snapshot-labs/actions/.github/workflows/release.yml@main
    secrets: inherit
```

## Notes

- [Github docs about reused worflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows)
- Environement variables can not be passed to the called workflow
- All workflows have a lower `timeout-minutes` than the default one (`360` minutes)

All worflows can be run with a matrix, e.g.

```yaml 
name: Lint

on: [push]

jobs:
  lint:
    strategy:
      matrix:
        target: ['16', '18', '20'] # Set your desired node versions (Default is 16)

    uses: snapshot-labs/actions/.github/workflows/lint.yml@main
    secrets: inherit
    with:
      target: ${{ matrix.target }}
```

## Convention

- Workflows are following the `VERB-DESCRIPTION` convention (e.g. `lint`, `build`, `publish-npm`) 
