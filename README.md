# Tinybird Agent Skills

A collection of skills for AI coding agents. Skills are packaged instructions that extend agent capabilities for working with Tinybird.

Skills follow the [Agent Skills](https://agentskills.io/) format.

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

## Installation

Choose your coding agent and follow the instructions below.

### Quick Install (All Agents)

Use the [skills CLI](https://skills.sh) to install with a single command—works with Claude Code, Cursor, Windsurf, and other agents:

```bash
npx skills add tinybirdco/tinybird-agent-skills
```

### Amp

Install the skill using the Amp CLI:

```bash
amp skill add github:tinybirdco/tinybird-agent-skills/skills/tinybird-best-practices
```

### Claude Code

Claude Code supports the [Agent Skills standard](https://agentskills.io). Install the skill to your project's `.claude/skills/` directory:

```bash
mkdir -p .claude/skills
curl -L https://github.com/tinybirdco/tinybird-agent-skills/archive/main.tar.gz | tar -xz --strip-components=2 -C .claude/skills tinybird-agent-skills-main/skills/tinybird-best-practices
```

Or install globally to `~/.claude/skills/`:

```bash
mkdir -p ~/.claude/skills
curl -L https://github.com/tinybirdco/tinybird-agent-skills/archive/main.tar.gz | tar -xz --strip-components=2 -C ~/.claude/skills tinybird-agent-skills-main/skills/tinybird-best-practices
```

The skill will be automatically discovered and applied when your request matches its description.

### Cursor

Cursor supports the [Agent Skills standard](https://agentskills.io). Install the skill to your project's `.cursor/skills/` directory:

```bash
mkdir -p .cursor/skills
curl -L https://github.com/tinybirdco/tinybird-agent-skills/archive/main.tar.gz | tar -xz --strip-components=2 -C .cursor/skills tinybird-agent-skills-main/skills/tinybird-best-practices
```

Or install via Cursor Settings:

1. Open **Cursor Settings** → **Rules**
2. In **Project Rules**, click **Add Rule**
3. Select **Remote Rule (Github)**
4. Enter: `https://github.com/tinybirdco/tinybird-agent-skills`

The skill will be available in the Agent Decides section and can be invoked with `/tinybird`.

### OpenCode

OpenCode supports the [Agent Skills standard](https://agentskills.io). Install the skill to your project's `.opencode/skills/` directory:

```bash
mkdir -p .opencode/skills
curl -L https://github.com/tinybirdco/tinybird-agent-skills/archive/main.tar.gz | tar -xz --strip-components=2 -C .opencode/skills tinybird-agent-skills-main/skills/tinybird-best-practices
```

Or install globally to `~/.config/opencode/skills/`:

```bash
mkdir -p ~/.config/opencode/skills
curl -L https://github.com/tinybirdco/tinybird-agent-skills/archive/main.tar.gz | tar -xz --strip-components=2 -C ~/.config/opencode/skills tinybird-agent-skills-main/skills/tinybird-best-practices
```

The skill will be available via the native `skill` tool and can be loaded on-demand by the agent.

### Codex (OpenAI)

Codex supports the [Agent Skills standard](https://agentskills.io). Install the skill to your project's `.codex/skills/` directory:

```bash
mkdir -p .codex/skills
curl -L https://github.com/tinybirdco/tinybird-agent-skills/archive/main.tar.gz | tar -xz --strip-components=2 -C .codex/skills tinybird-agent-skills-main/skills/tinybird-best-practices
```

Or use `$skill-installer` in Codex to install from GitHub:

```
$skill-installer install the tinybird-best-practices skill from tinybirdco/tinybird-agent-skills
```

The skill will be available as `$tinybird` in your Codex session.

### Universal (works with most agents)

Most coding agents support `AGENTS.md`. Download directly to your project:

```bash
curl -o AGENTS.md https://raw.githubusercontent.com/tinybirdco/tinybird-agent-skills/main/skills/tinybird-best-practices/SKILL.md
```

## Usage

Skills are automatically available once installed. The agent will use them when relevant tasks are detected.

**Examples:**
- "Create a datasource for user events"
- "Optimize this endpoint for low latency"
- "Set up tests for my pipes"

## Skill Structure

Each skill contains:
- `SKILL.md` - Instructions for the agent
- `rules/` - Individual guidance files

## License

MIT
