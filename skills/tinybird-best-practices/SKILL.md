---
name: tinybird
description: Tinybird file formats, SQL rules, optimization patterns, and best practices for datasources, pipes, endpoints, and materialized views.
---

# Tinybird Best Practices

Guidance for Tinybird file formats, SQL rules, optimization patterns, and data modeling. Use this skill when creating or editing Tinybird datafiles.

## When to Apply

- Creating or updating Tinybird resources (.datasource, .pipe, .connection)
- Writing or optimizing SQL queries
- Designing endpoint schemas and data models
- Working with materialized views or copy pipes
- Implementing deduplication patterns
- Reviewing or refactoring Tinybird project files

## Rule Files

- `rules/project-files.md`
- `rules/datasource-files.md`
- `rules/pipe-files.md`
- `rules/endpoint-files.md`
- `rules/materialized-files.md`
- `rules/sink-files.md`
- `rules/copy-files.md`
- `rules/connection-files.md`
- `rules/sql.md`
- `rules/endpoint-optimization.md`
- `rules/tests.md`
- `rules/deduplication-patterns.md`

## Quick Reference

- Project local files are the source of truth.
- SQL is SELECT-only with Tinybird templating rules and strict parameter handling.
- Use MergeTree by default; AggregatingMergeTree for materialized targets.
- Filter early, select only needed columns, push complex work later in the pipeline.
