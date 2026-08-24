# ci-workflows

Shared, reusable GitHub Actions workflows for Jon's iOS apps.

This repo is **public on purpose**: the apps that consume it (galavant, yes-chef) are
public, and GitHub does not let a public repository call a reusable workflow that lives
in a private repository. The private `jon-platform` repo therefore can't host it.

Nothing secret lives here. `ios-ci.yml` clones the private `jon-platform` (for local-path
SPM deps) using each caller's own `JON_PLATFORM_PAT` secret, passed in at runtime via
`secrets: inherit` — this file never contains it.

## Using it

Add this `ci.yml` to an app repo:

```yaml
name: CI
on:
  pull_request:
    branches: [main]
  push:
    branches: [main]
concurrency:
  group: ci-${{ github.ref }}
  cancel-in-progress: true
jobs:
  ci:
    uses: jonphillips/ci-workflows/.github/workflows/ios-ci.yml@main
    with:
      package-path: <YourPackageDir>   # e.g. GalavantLibrary, YesChefPackage
      # test-runner: macos-26          # only if the hosted image can build the manifest today
    secrets: inherit
```

Then add a `JON_PLATFORM_PAT` secret (fine-grained, Contents: read on
`jonphillips/jon-platform`) to the app repo.
