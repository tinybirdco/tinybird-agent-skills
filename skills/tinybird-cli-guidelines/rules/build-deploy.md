# Build & Deploy

Use this rule to keep Tinybird Local and Tinybird Cloud in sync with your project files. Build updates Tinybird Local for fast iteration; deploy updates Tinybird Cloud for production or shared environments.

## When to use build vs deploy

- Use `tb build` when you need Tinybird Local updated with your latest datafiles.
- Use `tb --cloud deploy` when you need to publish changes to Tinybird Cloud.
- If you are unsure whether a resource is synced in Cloud, run `tb --cloud deploy --check` to see the differences between the local project files and Tinybird Cloud.

## Build to Tinybird Local (tb build)

- Builds your local project into Tinybird Local using the current project files.
- Use after editing datafiles to validate syntax and dependencies before testing locally.
- This does not deploy to Tinybird Cloud.

## Deploy to Tinybird Cloud (tb --cloud deploy)

- Deploys the current project files to Tinybird Cloud.
- Run a deploy check first with `tb --cloud deploy --check` to validate without deploying.
- Use only when the user explicitly requests a cloud deployment.
- Ask for confirmation before deploying.

## Deploy check without deploying (tb --cloud deploy --check)

- Validates that the project can be deployed without creating a deployment.
- Use before a real deploy to catch schema or dependency errors early.
- Check if the local project differs from Tinybird Cloud.

## Destructive operations and flags

- Deleting datasources, pipes, or connections locally requires an explicit destructive deploy.
- Use `tb --cloud deploy --allow-destructive-operations` only when the user confirms deletion or data loss is acceptable.
- If you see warnings about deletions, stop and ask for confirmation before re-running with the flag.

Example:
```
tb --cloud deploy --allow-destructive-operations
```

## Validation intent (why)

- Building keeps Tinybird Local aligned with local files for faster iteration.
- Deploy checks reduce failed deployments by validating changes before publishing.

## What not to do

- Do not deploy destructive changes without `--allow-destructive-operations` and explicit user confirmation.
- Do not assume Tinybird Cloud is updated after a local build; build and deploy are separate operations.
