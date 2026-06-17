# Pre-filter Right-Side JOINs in Materialized Views

Materialized views run as **insert triggers**: on every block inserted
into the source datasource (the left-most table in `FROM`), Tinybird
re-executes the pipe SQL with that block as the `FROM` source. Any
table on the right side of a `JOIN` / `ASOF JOIN`, however, is scanned
**in full** unless explicitly restricted. As the right-side table grows,
each insert becomes more expensive and ingestion can stall or fail.

## When to Apply

Apply this pattern to any `TYPE materialized` pipe where:

- The right side of a JOIN is a datasource that grows unbounded over time
  (e.g. `email_sends`, `sms_sends`, `orders`, `sessions`).
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
   gap** between the right-side event and the left-side event — make
   it an obvious, configurable constant so it can be tuned later.

The left-side reference inside the subquery (`FROM events_landing`)
resolves to the inserting block, not the full table — that is exactly
what makes the pre-filter cheap.

## Example

Before — `email_sends` is scanned in full on every insert:

```
NODE mv_messaging_events_node
SQL >
    SELECT
        e.business_id,
        e.customer_id,
        e.event_name,
        e.event_time,
        es.sent_at AS send_time
    FROM events_landing e
    ASOF LEFT JOIN email_sends es
        ON e.business_id = es.business_id
        AND e.customer_id = es.customer_id
        AND toInt64OrZero(toString(e.event.drip_email_id)) = es.drip_email_id
        AND e.event_time >= es.sent_at
    WHERE e.event_name LIKE 'email-%'
       OR e.event_name = 'clicked-link'

TYPE materialized
DATASOURCE mv_messaging_events
```

After — `email_sends` is restricted by keys present in the batch and
by a 30-day time window relative to the batch's event times:

```
NODE mv_messaging_events_node
SQL >
    SELECT
        e.business_id,
        e.customer_id,
        e.event_name,
        e.event_time,
        es.sent_at AS send_time
    FROM events_landing e
    ASOF LEFT JOIN (
        SELECT business_id, customer_id, drip_email_id, sent_at
        FROM email_sends
        WHERE sent_at >= (
                SELECT min(event_time)
                FROM events_landing
                WHERE event_name LIKE 'email-%' OR event_name = 'clicked-link'
            ) - INTERVAL 30 DAY
          AND sent_at <= (
                SELECT max(event_time)
                FROM events_landing
                WHERE event_name LIKE 'email-%' OR event_name = 'clicked-link'
            )
          AND (business_id, customer_id, drip_email_id) IN (
                SELECT
                    business_id,
                    customer_id,
                    toInt64OrZero(toString(event.drip_email_id))
                FROM events_landing
                WHERE event_name LIKE 'email-%' OR event_name = 'clicked-link'
            )
    ) es
        ON e.business_id = es.business_id
        AND e.customer_id = es.customer_id
        AND toInt64OrZero(toString(e.event.drip_email_id)) = es.drip_email_id
        AND e.event_time >= es.sent_at
    WHERE e.event_name LIKE 'email-%'
       OR e.event_name = 'clicked-link'

TYPE materialized
DATASOURCE mv_messaging_events
```

Repeat the same wrapping for every right-side JOIN (e.g. `sms_sends`).

## Checklist

- [ ] Subquery on the right-side table selects only the columns used by
      the JOIN and the SELECT.
- [ ] Key pre-filter uses an `IN` tuple of the join columns from the
      left-side source, replicating the same `WHERE` that bounds the MV.
- [ ] For `ASOF`/time-bounded joins, include `right.time BETWEEN
      (min(left.time) - INTERVAL N <unit>) AND max(left.time)`.
- [ ] The `INTERVAL N <unit>` constant is a single, obvious literal —
      not buried in arithmetic — so the maximum gap is tunable.
- [ ] The `WHERE` filter inside the inner `FROM events_landing`
      subqueries matches the outer pipe's `WHERE` so the inserting
      block is read consistently.

## Gotchas

- **The lower time bound trades cost for correctness.** Any right-side
  row older than `min(left.time) - N <unit>` will be excluded — even if
  it would have been the correct `ASOF` match. Pick `N` large enough to
  cover the realistic gap between right-side events (e.g. a send) and
  the left-side events that reference them (e.g. an open). Document it.
- **Multiple right-side JOINs need independent pre-filters.** Each
  right-side table has its own keys and time semantics; do not share
  one subquery across them.
- **`event.field` access inside the inner subquery** must mirror the
  outer extraction exactly (same `toInt64OrZero(toString(...))` or
  equivalent), otherwise the `IN` tuple won't match.
- **The pre-filter does not change MV correctness for in-window data**
  but it does change it for out-of-window data — make this explicit in
  a pipe-level `DESCRIPTION`.
