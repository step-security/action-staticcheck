[![StepSecurity Maintained Action](https://raw.githubusercontent.com/step-security/maintained-actions-assets/main/assets/maintained-action-banner.png)](https://docs.stepsecurity.io/actions/stepsecurity-maintained-actions)

# action-staticcheck

This action runs [staticcheck](https://staticcheck.io/) with reviewdog on pull requests to improve code review
experience.

[![github-pr-review demo](https://user-images.githubusercontent.com/3797062/81496355-1a58f580-92f2-11ea-809b-7e61f863c147.png)](https://github.com/step-security/action-staticcheck/pull/1#discussion_r422620536)
<sub>Link to the staticcheck check rule is available as well.
e.g. [SA9004](https://staticcheck.io/docs/checks#SA9004)</sub>

## Input

```yaml
inputs:
  github_token:
    description: 'GITHUB_TOKEN'
    default: '${{ github.token }}'
  workdir:
    description: 'Working directory relative to the root directory.'
    default: '.'
  ### Flags for reviewdog ###
  level:
    description: 'Report level for reviewdog [info,warning,error]'
    default: 'error'
  reporter:
    description: 'Reporter of reviewdog command [github-pr-check,github-check,github-pr-review].'
    default: 'github-pr-review'
  filter_mode:
    description: |
      Filtering mode for the reviewdog command [added,diff_context,file,nofilter].
      Default is added.
    default: 'added'
  fail_level:
    description: |
      If set to `none`, always use exit code 0 for reviewdog. Otherwise, exit code 1 for reviewdog if it finds at least 1 issue with severity greater than or equal to the given level.
      Possible values: [none,any,info,warning,error]
      Default is `none`.
    default: 'none'
  fail_on_error:
    description: |
      Deprecated, use `fail_level` instead.
      Exit code for reviewdog when errors are found [true,false]
      Default is `false`.
    default: 'false'
    deprecationMessage: 'Deprecated, use `fail_level` instead.'
  reviewdog_flags:
    description: 'Additional reviewdog flags'
    default: ''
  ### Flags for staticcheck ###
  target:
    description: 'Target of staticcheck'
    default: './...'
  staticcheck_flags:
    description: 'staticcheck flags'
    default: ''
  use_go_install:
    description: 'Use go install to build staticcheck from source instead of downloading prebuilt binary'
    default: 'false'
```

## Usage

```yaml
name: reviewdog
on: [ pull_request ]
jobs:
  staticcheck:
    name: runner / staticcheck
    runs-on: ubuntu-latest
    steps:
      # checkout code
      - uses: actions/checkout@v6

      - uses: actions/setup-go@v6
        with:
          go-version: "1.25"

      # run staticcheck
      - uses: step-security/action-staticcheck@v1
        with:
          github_token: ${{ secrets.github_token }}
          # Change reviewdog reporter if you need [github-pr-check,github-check,github-pr-review].
          reporter: github-pr-review
          # Report all results.
          filter_mode: nofilter
          # Exit with 1 when it find at least one finding.
          fail_on_error: true
```
