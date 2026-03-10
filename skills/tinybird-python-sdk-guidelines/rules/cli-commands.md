# SDK CLI Commands

The SDK installs `tinybird` as a runtime dependency. Some commands are handled by the SDK; others delegate to the Tinybird CLI.

## tinybird init

Initialize a new Tinybird project:

```bash
tinybird init
tinybird init --force          # Overwrite existing files
tinybird init --skip-login     # Skip browser authentication
```

Creates `lib/datasources.py`, `lib/pipes.py`, `lib/client.py`, and `tinybird.config.json`.

## tinybird migrate

Migrate legacy datafiles to Python definitions:

```bash
tinybird migrate "tinybird/**/*.datasource" "tinybird/**/*.pipe" "tinybird/**/*.connection"
tinybird migrate tinybird/legacy --out ./tinybird.migration.py
tinybird migrate tinybird --dry-run
```

Converts `.datasource`, `.pipe`, and `.connection` files into a Python definitions file.

## tinybird dev

Watch schema files and auto-sync to Tinybird:

```bash
tinybird dev                   # Default: sync with cloud branches
tinybird dev --local           # Sync with local container
tinybird dev --branch          # Explicitly use cloud branches
```

**Important**: Dev mode only works with feature branches, not main. This prevents accidental production changes.

## tinybird build

Build and push resources to a Tinybird branch:

```bash
tinybird build                 # Build and push to branch
tinybird build --dry-run       # Preview without pushing
tinybird build --local         # Build to local container
tinybird build --branch        # Explicitly use cloud branches
```

**Important**: Build targets branches only, not main.

## tinybird deploy

Deploy resources to the main workspace (production):

```bash
tinybird deploy                # Deploy to main/production
tinybird deploy --check        # Validate without deploying
tinybird deploy --allow-destructive-operations  # Allow breaking changes
```

This is the only way to deploy to main.

## tinybird pull

Pull resources from remote workspace:

```bash
tinybird pull                  # Pull to default location
tinybird pull --output-dir ./tinybird-datafiles
tinybird pull --force          # Overwrite existing files
```

## tinybird login

Authenticate via browser:

```bash
tinybird login
```

Useful for existing projects or token refresh.

## tinybird branch

Manage branches:

```bash
tinybird branch list           # List all branches
tinybird branch status         # Show current branch status
tinybird branch delete <name>  # Delete a branch
```

## tinybird info

Display workspace, local, and project configuration:

```bash
tinybird info                  # Show configuration
tinybird info --json           # Output as JSON
```

## Development Workflow

1. `tinybird init` - Initialize project
2. Define datasources and pipes in Python
3. `tinybird dev` - Watch and sync changes to a branch
4. Test endpoints
5. `tinybird deploy` - Deploy to production

## Migration Workflow

1. `tinybird migrate "path/to/*.datasource" "path/to/*.pipe"` - Convert legacy files
2. Review generated Python file
3. Move definitions to `lib/datasources.py` and `lib/pipes.py`
4. Update `tinybird.config.json` to include Python files
5. `tinybird dev` - Verify sync works

## Important Notes

- The `dev` command enforces feature branch restrictions to prevent production incidents
- Use `--dry-run` to preview changes before pushing
- The CLI automatically loads `.env.local` and `.env` files
