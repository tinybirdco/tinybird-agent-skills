# Tinybird Agent Skills

A collection of skills for AI coding agents. Skills are packaged instructions that extend agent capabilities for working with Tinybird.

Skills follow the [Agent Skills](https://agentskills.io/) format.

Install with

```bash
npx skills add tinybirdco/tinybird-agent-skills
```

Update with

```bash
npx skills update
```

## Available Skills

### Core project skills

Use these for day-to-day Tinybird project work. They are safe defaults to keep enabled.

#### tinybird-best-practices

Tinybird project guidelines from Tinybird Engineering. Contains 18 rule files covering datasources, pipes, endpoints, SQL, deployments, and testing.

**Use when:**
- Creating or updating Tinybird resources (.datasource, .pipe, .connection)
- Working with queries, endpoints, or data exploration
- Managing Tinybird deployments, secrets, or tests
- Reviewing or refactoring Tinybird project files

**Categories covered:**
- Project structure and local development
- Datasource, pipe, and endpoint files
- SQL and query optimization
- Build and deploy workflows
- Testing and secrets management

### CLI workflow skills

Use these when operating Tinybird with the CLI (local dev, deployments, data ops) and the datafile (.pipe, .connection, .datasource) format

#### tinybird-cli-guidelines

Tinybird CLI commands, workflows, and operations. Use when running `tb` commands, managing local development, deploying, or working with data operations.

**Use when:**
- Running any `tb` command
- Local development with Tinybird Local
- Building and deploying projects
- Appending, replacing, or deleting data
- Managing tokens and secrets via CLI
- Generating mock data
- Running tests

### TypeScript SDK skills

Use these when working with the `@tinybirdco/sdk` package and type-safe projects.

#### tinybird-typescript-sdk-guidelines

Tinybird TypeScript SDK for defining datasources, pipes, and queries with full type inference. Use when working with @tinybirdco/sdk, TypeScript Tinybird projects, or type-safe data ingestion and queries.

**Use when:**
- Installing or configuring @tinybirdco/sdk
- Defining datasources or pipes in TypeScript
- Creating typed Tinybird clients
- Using type-safe ingestion or queries
- Running tinybird dev/build/deploy commands for TypeScript projects
- Migrating from legacy .datasource/.pipe files to TypeScript

## Usage

Skills are automatically available once installed. The agent will use them when relevant tasks are detected. You can use the agent cli to check, e.g., `amp skill list`, or directly ask the agent to tell you what skills are available.

**Recommended defaults:**
- Always enable `tinybird-best-practices` for general Tinybird project work.
- Add `tinybird-cli-guidelines` whenever you plan to run `tb` commands.
- Add `tinybird-typescript-sdk-guidelines` for TypeScript SDK projects.

**Examples:**
- "Create a datasource for user events"
- "Optimize this endpoint for low latency"
- "Set up tests for my endpoints"

## Skill Structure

Each skill contains:
- `SKILL.md` - Instructions for the agent
- `rules/` - Individual guidance files
