# update-release-status Github Action

This action updates the status of a release on GitHub Actions to Prodvana. This is meant to be used in conjunction with record-release-action, with `pending: true`.


# Requirements
- [pvnctl](https://github.com/prodvana/pvnctl) present in the CI environment
  - We recommend using [init-pvnctl-action](https://github.com/prodvana/init-pvnctl-action) to handle installation and authentication initialization

# Usage

## Inputs

| Input             | Default        | Description                                                                                                                                              |
| ----------------- | -------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| release_id        | (required)     | The release ID returned from record-release action                                                                                                       |
| failed            | false          | Mark release as failing                                                                                                                                  |
| auth_context      | default        | pvnctl auth context to use. If you're using this action with init-pvnctl, leave as the default                                                           |


## Basic Usage

```yaml
steps:
  # pvnctl must be installed in your Action environment for configs-apply
  - uses: prodvana/init-pvnctl-action@v0.1.0
    with:
      org: my-org
      api_token: ${{ secrets.YOUR_PRODVANA_API_TOKEN }}
  - uses: prodvana/record-release-action@v0.1.1
    id: record-release
    with:
      app: product-name
      service: web
      release_channel: prod
      pending: true
  # do actual release, then add these at the end
  - uses: prodvana/update-release-status-action@v0.1.0
    with:
      release_id: ${{ steps.record-release.outputs.release_id }}
  - uses: prodvana/update-release-status-action@v0.1.0
    if: failure()
    with:
      release_id: ${{ steps.record-release.outputs.release_id }}
      failed: true
```