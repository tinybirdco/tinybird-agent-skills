# Project Files

## Project Root

- By default, create a `tinybird/` folder at the project root and nest Tinybird folders under it.
- Ensure the `.tinyb` credentials file is at the same level where the CLI commands are run.
- If credentials issues occur, run `tb info` to see where the CLI is loading the `.tinyb` file from and the current working workspace.

## File Locations

Default locations (use these unless the project uses a different structure):

- Endpoints: `/endpoints`
- Materialized pipes: `/materializations`
- Sink pipes: `/sinks`
- Copy pipes: `/copies`
- Connections: `/connections`
- Datasources: `/datasources`

## File-Specific Rules

See these rule files for detailed requirements:

- `rules/datasource-files.md`
- `rules/pipe-files.md`
- `rules/endpoint-files.md`
- `rules/materialized-files.md`
- `rules/sink-files.md`
- `rules/copy-files.md`
- `rules/connection-files.md`
