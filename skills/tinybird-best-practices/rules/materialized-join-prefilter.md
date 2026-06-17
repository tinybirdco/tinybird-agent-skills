# Pre-filter Right-Side JOINs in Materialized Views

Materialized views run as **insert triggers**: on every block inserted
into the source datasource (the left-most table in `FROM`), Tinybird
re-executes the pipe SQL with that block as the `FROM` source. Any
table on the right side of a `JOIN` / `ASOF JOIN`, however, is scanned
**in full** unless explicitly restricted. As the right-side table grows,
each insert becomes more expensive and ingestion can stall or fail.

## When to Apply

Apply this pattern to any `TYPE materialized` pipe where:

- The right side of a JOIN is a datasource that grows unbounded over time.
- Symptoms: slow inserts, ingestion lag, memory spikes on the source
  datasource, `MEMORY_LIMIT_EXCEEDED` errors on the MV.
- The JOIN already has selective conditions (equality on keys, time
  bounds) — those conditions are what we promote into a pre-filter.

## The Pattern

Replace the right-side datasource with a subquery that restricts it
to rows that **could possibly match** the current insert batch.
Two filters compose:

1. **Key pre-filter** — keep only right-side rows whose join keys
   appear in the inserting batch from the left-side source.
2. **Time pre-filter** — for `ASOF` joins with `left.time >= right.time`,
   bound `right.time` to `[min(left.time) - INTERVAL N <unit>, max(left.time)]`
   of the inserting batch. The lower bound is the **maximum acceptable
   gap** between the right-side row and the left-side row — make
   it an obvious, configurable constant so it can be tuned later.

The left-side reference inside the subquery (the same datasource that
appears in the outer `FROM`) resolves to the inserting block, not the
full table — that is exactly what makes the pre-filter cheap.

## Example

Before — `right_table` is scanned in full on every insert:

```
NODE mv_node
SQL >
    SELECT
        l.key_a,
        l.key_b,
        l.event_name,
        l.event_time,
        r.right_time AS resolved_time
    FROM left_source l
    ASOF LEFT JOIN right_table r
        ON l.key_a = r.key_a
        AND l.key_b = r.key_b
        AND l.join_id = r.join_id
        AND l.event_time >= r.right_time
    WHERE l.event_name IN ('event_x', 'event_y')

TYPE materialized
DATASOURCE mv_target
```

After — `right_table` is restricted by keys present in the batch and
by a 30-day time window relative to the batch's event times:

```
NODE mv_node
SQL >
    SELECT
        l.key_a,
        l.key_b,
        l.event_name,
        l.event_time,
        r.right_time AS resolved_time
    FROM left_source l
    ASOF LEFT JOIN (
        SELECT key_a, key_b, join_id, right_time
        FROM right_table
        WHERE right_time >= (
                SELECT min(event_time)
                FROM left_source
                WHERE event_name IN ('event_x', 'event_y')
            ) - INTERVAL 30 DAY
          AND right_time <= (
                SELECT max(event_time)
                FROM left_source
                WHERE event_name IN ('event_x', 'event_y')
            )
          AND (key_a, key_b, join_id) IN (
                SELECT key_a, key_b, join_id
                FROM left_source
                WHERE event_name IN ('event_x', 'event_y')
            )
    ) r
        ON l.key_a = r.key_a
        AND l.key_b = r.key_b
        AND l.join_id = r.join_id
        AND l.event_time >= r.right_time
    WHERE l.event_name IN ('event_x', 'event_y')

TYPE materialized
DATASOURCE mv_target
```

Repeat the same wrapping for every right-side JOIN — one independent
subquery per right-side table.

## Checklist

- [ ] Subquery on the right-side table selects only the columns used by
      the JOIN and the SELECT.
- [ ] Key pre-filter uses an `IN` tuple of the join columns from the
      left-side source, replicating the same `WHERE` that bounds the MV.
- [ ] For `ASOF`/time-bounded joins, include `right.time BETWEEN
      (min(left.time) - INTERVAL N <unit>) AND max(left.time)`.
- [ ] The `INTERVAL N <unit>` constant is a single, obvious literal —
      not buried in arithmetic — so the maximum gap is tunable.
- [ ] The `WHERE` filter inside the inner subqueries over the left-side
      source matches the outer pipe's `WHERE` so the inserting block is
      read consistently.

## Gotchas

- **The lower time bound trades cost for correctness.** Any right-side
  row older than `min(left.time) - N <unit>` will be excluded — even if
  it would have been the correct `ASOF` match. Pick `N` large enough to
  cover the realistic gap between right-side rows and the left-side rows
  that reference them. Document it.
- **Multiple right-side JOINs need independent pre-filters.** Each
  right-side table has its own keys and time semantics; do not share
  one subquery across them.
- **Key extraction inside the inner subquery** must mirror the outer
  extraction exactly (same casts, same `JSONExtract` / `toInt64OrZero`
  wrappers, etc.), otherwise the `IN` tuple won't match.
- **The pre-filter does not change MV correctness for in-window data**
  but it does change it for out-of-window data — make this explicit in
  a pipe-level `DESCRIPTION`.
