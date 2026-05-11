# Branch Development

## Overview

Tinybird Cloud branches provide isolated environments for development and testing. Each branch gets its own copy of resources and can optionally include production data. Branches are the recommended workflow for teams collaborating on the same workspace.

## When to Use Branches

- Developing features that need real production data shapes for testing
- Collaborating with a team where multiple people work on the same workspace
- Testing schema changes or new endpoints before deploying to production
- CI/CD workflows that validate changes on pull requests

For solo development or quick iteration, Tinybird Local (`dev_mode=local`) may be faster. See `rules/local-development.md`.

## Branch Workflow

1. Create a git branch for your feature
2. Create a Tinybird branch (this happens automatically with `tb build` when `dev_mode=branch`, or manually)
3. Develop and test against the branch
4. Push changes and create a PR
5. Merge to deploy to production

## Creating Branches

Automatic (recommended when `dev_mode=branch`):

```
tb build
```

This creates a Cloud branch derived from the current git branch name automatically.

Manual:

```
tb branch create my_feature
```

Branch names must use underscores, not hyphens (e.g., `my_feature`, not `my-feature`).

### The `--last-partition` Flag

Use `--last-partition` to copy the latest partition of production data into the branch:

```
tb branch create my_feature --last-partition
```

This is useful when you need real data to test queries, validate endpoint behavior, or debug issues that depend on production data shapes. Without it, the branch starts empty.

## Working with Branch Tokens

After creating a branch, you may need its token to connect client applications (dashboards, APIs, scripts) to the branch environment instead of production.

List tokens for a branch:

```
tb --branch my_feature token ls
```

### Using Branch Tokens in Client Apps

A common pattern is to set an environment variable that your application checks, falling back to the production token when no branch token is set:

```env
# .env.local
TINYBIRD_API_URL=https://api.tinybird.co
TINYBIRD_API_TOKEN=<production-read-token>
TINYBIRD_BRANCH_TOKEN=<branch-token>
```

In your application, prioritize the branch token when present:

```
token = TINYBIRD_BRANCH_TOKEN || TINYBIRD_API_TOKEN
```

This way, setting or unsetting the branch token switches between branch and production data without code changes.

## Branch Commands Reference

- `tb branch ls`: List all branches
- `tb branch create <name>`: Create a new branch (empty)
- `tb branch create <name> --last-partition`: Create a branch with latest production data
- `tb branch rm <name>`: Remove a branch
- `tb branch clear`: Clear branch state

## Targeting a Branch Explicitly

Most commands can target a specific branch with the `--branch` flag:

```
tb --branch my_feature endpoint data my_endpoint
tb --branch my_feature sql "SELECT count() FROM my_datasource"
tb --branch my_feature token ls
```

When `dev_mode=branch`, `tb build` targets the branch automatically without needing `--branch`.
