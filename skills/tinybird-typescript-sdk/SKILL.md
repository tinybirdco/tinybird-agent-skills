---
name: tinybird-typescript-sdk
description: Tinybird TypeScript SDK for defining datasources, pipes, and queries with full type inference. Use when working with @tinybirdco/sdk, TypeScript Tinybird projects, or type-safe data ingestion and queries.
---

# Tinybird TypeScript SDK Skills

Guidance for using the `@tinybirdco/sdk` package to define Tinybird resources in TypeScript with complete type inference.

## When to Apply

- Installing or configuring @tinybirdco/sdk
- Defining datasources or pipes in TypeScript
- Creating typed Tinybird clients
- Using type-safe ingestion or queries
- Running tinybird dev/build/deploy commands for TypeScript projects
- Migrating from legacy .datasource/.pipe files to TypeScript

## Rule Files

- `rules/sdk-overview.md`
- `rules/sdk-configuration.md`
- `rules/sdk-datasources.md`
- `rules/sdk-endpoints.md`
- `rules/sdk-client.md`
- `rules/sdk-api.md`
- `rules/sdk-cli.md`

## Quick Reference

- Install: `npm install @tinybirdco/sdk`
- Initialize: `npx tinybird init`
- Dev mode: `tinybird dev` (watches and syncs to branches, not main)
- Deploy: `tinybird deploy` (deploys to main/production)
- Server-side only; never expose tokens in browsers
