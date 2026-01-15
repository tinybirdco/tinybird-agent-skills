# Tinybird Local Development

## Overview

- Tinybird Local runs as a Docker container managed by the Tinybird CLI.
- Local is the default execution target; use `--cloud` to operate on Cloud.
- Use Tinybird Local to develop and test projects before deploying to Cloud.

## Commands

- `tb local start`
  - Options: `--use-aws-creds`, `--volumes-path <path>`, `--skip-new-version`, `--user-token`, `--workspace-token`, `--daemon`.
- `tb local stop`
- `tb local restart`
  - Options: `--use-aws-creds`, `--volumes-path`, `--skip-new-version`, `--yes`.
- `tb local status`
- `tb local remove`
- `tb local version`
- `tb local generate-tokens`

Notes:
- If you remove the container without a persisted volume, local data is lost.
- Use `tb --cloud ...` for Cloud operations.

## Local-First Workflow

1) `tb local start`
2) Develop resources and run `tb build` as needed
3) Test endpoints/queries locally
4) Use `--cloud` for Cloud actions (deploy, etc.)

Use `--volumes-path` to persist data between restarts.

## Troubleshooting

- If status shows unhealthy, run `tb local restart` and re-check.
- If authentication is not ready, wait or restart the container.
- If memory warnings appear in status, increase Docker memory allocation.
- If Local is not running, start it with `tb local start`.
