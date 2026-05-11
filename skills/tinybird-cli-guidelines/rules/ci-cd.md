# CI/CD Integration

## Recommended Pattern

Use `tb deploy --check` in CI to validate pull requests, and `tb deploy` in CD to deploy on merge to the main branch. This keeps production deploys automated and prevents manual mistakes.

## CI: Pull Request Validation

Run on every PR that changes Tinybird project files:

```
tb deploy --check
```

This validates that the deployment would succeed (schema compatibility, dependency resolution, resource naming) without actually applying changes. It catches issues before they reach production.

## CD: Production Deployment

Run when changes are merged to the main branch:

```
tb deploy
```

This deploys the current project files to the Tinybird Cloud production environment.

For projects that prefer explicit confirmation, use a two-step process:

```
tb deployment create
tb deployment promote
```

## Example: GitHub Actions

```yaml
# .github/workflows/tinybird-ci.yml
name: Tinybird CI
on:
  pull_request:
    paths:
      - 'tinybird/**'

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: pip install tinybird-cli
      - run: tb deploy --check
        working-directory: tinybird
        env:
          TB_TOKEN: ${{ secrets.TB_ADMIN_TOKEN }}
```

```yaml
# .github/workflows/tinybird-cd.yml
name: Tinybird CD
on:
  push:
    branches: [main]
    paths:
      - 'tinybird/**'

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: pip install tinybird-cli
      - run: tb deploy --wait
        working-directory: tinybird
        env:
          TB_TOKEN: ${{ secrets.TB_ADMIN_TOKEN }}
```

## Example: GitLab CI

```yaml
tinybird-ci:
  stage: test
  rules:
    - if: '$CI_PIPELINE_SOURCE == "merge_request_event"'
      changes:
        - tinybird/**
  script:
    - pip install tinybird-cli
    - cd tinybird && tb deploy --check
  variables:
    TB_TOKEN: $TB_ADMIN_TOKEN

tinybird-cd:
  stage: deploy
  rules:
    - if: '$CI_COMMIT_BRANCH == "main"'
      changes:
        - tinybird/**
  script:
    - pip install tinybird-cli
    - cd tinybird && tb deploy --wait
  variables:
    TB_TOKEN: $TB_ADMIN_TOKEN
```

## Key Principles

- Production deploys should happen through CI/CD, not manually.
- Always run `tb deploy --check` on PRs to catch issues early.
- Use `--wait` in CD pipelines so the job reflects the actual deployment result.
- Store the admin token as a CI/CD secret, never in code.
- Scope CI triggers to Tinybird project file paths to avoid unnecessary runs.
