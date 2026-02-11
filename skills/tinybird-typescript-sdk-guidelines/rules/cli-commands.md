# SDK CLI Commands

The SDK includes CLI commands for development and deployment workflows.

## tinybird init

Initialize a new TypeScript Tinybird project:

```bash
npx tinybird init
npx tinybird init --force          # Overwrite existing files
npx tinybird init --skip-login     # Skip browser authentication
```

Detects existing `.datasource` and `.pipe` files for incremental migration.

## tinybird dev

Watch schema files and auto-sync to Tinybird:

```bash
tinybird dev                       # Default: sync with cloud branches
tinybird dev --local               # Sync with local container
tinybird dev --branch              # Explicitly use cloud branches
```

**Important**: Dev mode only works with feature branches, not main. This prevents accidental production changes.

## tinybird build

Build and push resources to a Tinybird branch:

```bash
tinybird build                     # Build and push to branch
tinybird build --dry-run           # Preview without pushing
tinybird build --local             # Build to local container
```

**Important**: Build targets branches only, not main.

## tinybird deploy

Deploy resources to the main workspace (production):

```bash
tinybird deploy                    # Deploy to main/production
tinybird deploy --dry-run          # Preview without deploying
```

This is the only way to deploy to main.

## tinybird login

Authenticate via browser:

```bash
tinybird login
```

Useful for existing projects or token refresh.

## tinybird branch

Manage branches:

```bash
tinybird branch list               # List all branches
tinybird branch status             # Show current branch status
tinybird branch delete <name>      # Delete a branch
```

## tinybird info

Display workspace, local, and project configuration:

```bash
tinybird info                      # Show configuration
tinybird info --json               # Output as JSON
```

## Development Workflow

1. `npx tinybird init` - Initialize project
2. Define datasources and pipes in TypeScript
3. `tinybird dev` - Watch and sync changes to a branch
4. Test endpoints
5. `tinybird deploy` - Deploy to production

## Important Notes

- The `dev` command enforces feature branch restrictions to prevent production incidents
- Use `--dry-run` to preview changes before pushing
- The SDK CLI is separate from the `tb` CLI but complementary
