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
        target: ['16', '18'] # Set your desired node versions (Default is 16)
    uses: snapshot-labs/actions/.github/workflows/lint.yml@main
    with:
      target: ${{ matrix.target }}
```

### Create a sentry release

This workflow trigger a task to upload the sourcemaps to Sentry, then create a Sentry release.

**Ensure your build task (`yarn build`) is configured to output sourcemaps.**


Install it in your project by creating the file `.github/workflows/lint.yml`, with the following content 

```yaml 
name: Create a Sentry release
on: [push]
jobs:
  lint:
    uses: snapshot-labs/actions/.github/workflows/create-sentry-release.yml@main
    with:
      project: YOUR-SENTRY-PROJECT-NAME
      # You can optionally add the following line to set the node version (Default is 16)
      # target: '16'
```
