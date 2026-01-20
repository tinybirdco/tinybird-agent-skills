# Tinybird Agent Skills

A collection of skills for AI coding agents. Skills are packaged instructions that extend agent capabilities for working with Tinybird.

Skills follow the [Agent Skills](https://agentskills.io/) format.

Install with

```bash
npx skills add tinybirdco/tinybird-agent-skills
```

## Available Skills

### tinybird-best-practices

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

## Usage

Skills are automatically available once installed. The agent will use them when relevant tasks are detected. You can use the agent cli to check, e.g., `amp skill list`, or directly ask the agent to tell you what skills are available.

**Examples:**
- "Create a datasource for user events"
- "Optimize this endpoint for low latency"
- "Set up tests for my endpoints"

## Skill Structure

Each skill contains:
- `SKILL.md` - Instructions for the agent
- `rules/` - Individual guidance files
