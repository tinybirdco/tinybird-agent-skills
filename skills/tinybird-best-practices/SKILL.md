---
name: tinybird
description: Tinybird Code agent tools and prompts for working with Tinybird projects, datafiles, queries, deployments, and tests.
---

# Tinybird Agent Skills

Reusable guidance extracted from Tinybird Code (the Tinybird CLI coding agent). Use this skill when working in Tinybird projects, editing datafiles, running build/deploy flows, exploring data, or managing tests/secrets.

## When to Apply

- Creating or updating Tinybird resources (.datasource, .pipe, .connection)
- Working with queries, endpoints, or data exploration
- Managing Tinybird deployments, secrets, or tests
- Reviewing or refactoring Tinybird project files

## Rule Files

- `rules/project-files.md`
- `rules/build-deploy.md`
- `rules/datasource-files.md`
- `rules/pipe-files.md`
- `rules/endpoint-files.md`
- `rules/materialized-files.md`
- `rules/sink-files.md`
- `rules/copy-files.md`
- `rules/connection-files.md`
- `rules/sql.md`
- `rules/endpoint-optimization.md`
- `rules/append-data.md`
- `rules/mock-data.md`
- `rules/tests.md`
- `rules/secrets.md`
- `rules/tokens.md`
- `rules/cli-commands.md`
- `rules/data-operations.md`
- `rules/deduplication-patterns.md`
- `rules/local-development.md`

## Quick Reference

- Project local files are the source of truth; build for Local, deploy for Cloud.
- SQL is SELECT-only with Tinybird templating rules and strict parameter handling.
- Use `tb info` to check CLI context.
- CLI commands by default target Local; use `tb --cloud <command>` to target Cloud (production) and `tb --branch <branch-name> <command>` to target a specific branch in Cloud.
