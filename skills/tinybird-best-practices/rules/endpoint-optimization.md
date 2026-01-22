# Endpoint Optimization

Use this checklist when optimizing endpoints.

## Step 1: Identify Issues

- Check endpoint metrics (latency, read_bytes, write_bytes).
- Look for high latency or excessive scanning.

## Step 2: 5-Question Diagnostic

1) Aggregations at query time?
- Fix: Move to materialized views when possible, to snapshots (copy pipes) or lambda architecture if MVs do not fit.

2) Filters match sorting keys?
- Fix: Include frequent filters in ENGINE_SORTING_KEY; order by selectivity.
- Avoid timestamp as first key in multi-tenant cases.

3) Data types oversized?
- Fix: Use smaller types, LowCardinality for low-unique strings, defaults instead of Nullable.

4) Complex ops too early?
- Fix: Filter first, then joins/aggregations.

5) Heavy JOINs?
- Fix: Replace with subqueries or filtered joins in materialized views.

## Step 3: Implementation Actions

- Schema changes: update datasource, sorting keys, and dependent pipes/endpoints.
- Query optimizations: materialize repeated aggregations; rewrite queries.
- JOIN optimizations: evaluate denormalization or filtered joins.

## Monitoring and Validation

- Track tinybird.pipe_stats_rt and tinybird.pipe_stats.
- Success metrics: lower latency, lower read_bytes, improved read_bytes/write_bytes.

## Query Explain

- For more details, call the endpoint with explain=true parameter to understand the query plan. E.g: https://$TB_HOST/v0/pipes/endpoint_name?explain=true

## Templates

Materialized view:
```
NODE materialized_view_name
SQL >
  SELECT toDate(timestamp) as date, customer_id, countState(*) as event_count
  FROM source_table
  GROUP BY date, customer_id

TYPE materialized
DATASOURCE mv_datasource_name
ENGINE "AggregatingMergeTree"
ENGINE_PARTITION_KEY "toYYYYMM(date)"
ENGINE_SORTING_KEY "customer_id, date"
```

Optimized query:
```
NODE endpoint_query
SQL >
  %
  SELECT date, sum(amount) as daily_total
  FROM events
  WHERE customer_id = {{ String(customer_id) }}
    AND date >= {{ Date(start_date) }}
    AND date <= {{ Date(end_date) }}
  GROUP BY date
  ORDER BY date DESC
```
