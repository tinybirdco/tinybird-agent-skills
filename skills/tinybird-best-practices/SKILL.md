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

- `rules/tools.md`
- `rules/planning.md`
- `rules/project-files.md
- `rules/build.md``
- `rules/datasource-files.md`
- `rules/pipe-files.md`
- `rules/endpoint-files.md`
- `rules/materialized-files.md`
- `rules/sink-files.md`
- `rules/copy-files.md`
- `rules/connection-files.md`
- `rules/sql.md`
- `rules/endpoint-optimization.md`
- `rules/explore-and-diff.md`
- `rules/append-data.md`
- `rules/tests.md`
- `rules/secrets.md`
- `rules/tokens.md`
- `rules/endpoint-urls.md`
- `rules/resources-context.md`
- `rules/cli-commands.md`
- `rules/local-development.md`
- `rules/style.md`

## Quick Reference

- Plan before create/update/delete operations; skip plan for single-step work.
- Project local files are the source of truth; build for Local, deploy for Cloud.
- SQL is SELECT-only with Tinybird templating rules and strict parameter handling.
