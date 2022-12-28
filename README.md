# Preview JavaScript Build Artifacts Directly in Pull Request with Stoat

This repo demos how to use Stoat for a typical JavaScript / TypeScript project. More details can be found in this article: [Preview JavaScript Build Artifacts Directly in Pull Request with Stoat](https://dev.to/tuliren/preview-javascript-typescript-build-artifacts-directly-in-pull-request-with-stoat-3nd1).

This repo is forked from [`penx/storybook-code-coverage`](https://github.com/penx/storybook-code-coverage). See this article for details: [Combining Storybook, Cypress and Jest Code Coverage](https://dev.to/penx/combining-storybook-cypress-and-jest-code-coverage-4pa5).

## Integration with Stoat in Three Steps

1. Add a GitHub workflow

    ```yaml
    name: Continuous Integration
    
    on:
      # Trigger the ci pipeline for every comment on the default branch or a pull request.
      # The default branch for the sample repo is `master`.
      push:
        branches:
          - master
      pull_request:
        branches:
          - master
    
    jobs:
      ci:
        runs-on: ubuntu-latest
        steps:
          - name: Checkout code
            uses: actions/checkout@v2
    
          - name: Set up node and cache
            uses: actions/setup-node@v3
            with:
              node-version: '16.8.0'
              cache: 'yarn'
    
          - name: Install dependencies
            run: yarn install --frozen-lockfile --prefer-offline
    
          # This project has a `coverage` script that runs all the
          #  tests and merge all their coverage reports.
          - name: Run tests and generate report
            run: yarn coverage
    
          - name: Generate storybook
            run: yarn build-storybook
    
          - name: Run stoat action
            uses: stoat-dev/stoat-action@v0
            if: always()
    ```

2. Install the Stoat app

The Stoat app can be installed [here](https://github.com/apps/stoat-app).

3. Add a Stoat config

    ```yaml
    ---
    version: 1
    enabled: true
    plugins:
      job_runtime:
        enabled: true
      static_hosting:
        merged-test-coverage:
          path: coverage/merged/lcov-report
        storybook:
          path: storybook-static
        cypress-video:
          path: cypress/videos/spec.js.mp4
    ```

That's it. See an example in [this pull request](https://github.com/stoat-dev/example-javascript/pull/1).
