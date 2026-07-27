# Materialized Pipe Files

- Do not create by default unless requested.
- Create under `/materializations`.
- Use TYPE MATERIALIZED and set DATASOURCE to the target datasource.
- Use State modifiers in the pipe; use AggregateFunction in the target datasource.
- Use Merge modifiers when reading AggregateFunction columns.
- Put all dimensions in ENGINE_SORTING_KEY, ordered from least to most cardinality.

Example:

```
NODE daily_sales
SQL >
    SELECT toStartOfDay(starting_date) day, country, sumState(sales) as total_sales
    FROM teams
    GROUP BY day, country

TYPE MATERIALIZED
DATASOURCE sales_by_hour
```

Target datasource example:

```
SCHEMA >
    `total_sales` AggregateFunction(sum, Float64),
    `sales_count` AggregateFunction(count, UInt64),
    `dimension_1` String,
    `dimension_2` String,
    `date` DateTime

ENGINE "AggregatingMergeTree"
ENGINE_PARTITION_KEY "toYYYYMM(date)"
ENGINE_SORTING_KEY "date, dimension_1, dimension_2"
```

## JSON extraction: parse once, not once per field

Materialized views commonly read a raw JSON/string payload column and extract many fields out of it. Because the MV query runs on every inserted block for the lifetime of the pipe, the cost of how that JSON is parsed is paid continuously — this is the single biggest lever for MV ingestion cost when the source is JSON.

Calling `JSONExtractString`/`JSONExtractInt`/`JSONExtractBool`/`JSONExtractFloat`/`simpleJSONExtractString` (or their `visitParam*` equivalents) once per field re-parses the same raw JSON string from scratch for every field read. With a few dozen fields, that's a few dozen full parses of the same payload per row.

Fix: parse the JSON **once** into a typed `Tuple` with `JSONExtract(...)`, then read every field cheaply from the parsed structure with `getSubcolumn`.

Bad (re-parses the JSON string once per field):
```
NODE typed_events
SQL >
    SELECT
        at AS timestamp,
        visitParamExtractString(payload, 'field_a') AS field_a,
        visitParamExtractInt(payload, 'field_b') AS field_b,
        visitParamExtractBool(payload, 'field_c') AS field_c,
        simpleJSONExtractString(payload, 'field_d') AS field_d
    FROM raw_events

TYPE MATERIALIZED
DATASOURCE typed_events_ds
```

Good (parses once, then projects subcolumns):
```
NODE typed_events
SQL >
    WITH
        JSONExtract(payload, 'Tuple(
            field_a String,
            field_b String,
            field_c Bool,
            field_d String
        )') AS payload_json
    SELECT
        at AS timestamp,
        getSubcolumn(payload_json, 'field_a') AS field_a,
        toInt64OrZero(getSubcolumn(payload_json, 'field_b')) AS field_b,
        getSubcolumn(payload_json, 'field_c') AS field_c,
        getSubcolumn(payload_json, 'field_d') AS field_d
    FROM raw_events

TYPE MATERIALIZED
DATASOURCE typed_events_ds
```

Notes:
- Declare numeric/optional fields as `String` in the `Tuple` schema rather than their native numeric type. A missing key then resolves to `''` instead of erroring, and `toXOrZero(...)` / `toXOrDefault(..., fallback)` reproduces the original "extract or fall back" logic — e.g. `toInt32OrDefault(getSubcolumn(payload_json, 'field_b'), -1::Int32)` for a `-1` fallback, or `toInt64OrZero(...)` when the original extractor already defaulted to `0`.
- Declare fields that are true booleans and always present as their native type (e.g. `Bool`) directly in the `Tuple` — no string round-trip needed.
- If several derived expressions build on the same raw field (e.g. normalizing a path, deriving a domain from a URL), extract that field once into a `WITH` alias and reuse it, instead of re-extracting it inline for each derived expression.
- Output column names/types should stay identical to the pre-optimization query — this is a query-shape optimization, not a schema change.
- The same pattern applies to endpoint/pipe queries doing heavy JSON extraction, but the payoff is largest in materialized views since the parse cost compounds over every ingested row rather than every query call.

## Usual gotchas
- Materialized Views work as insert triggers, which means a delete or truncate operation on your original Data Source doesn't affect the related Materialized Views.

- As transformation and ingestion in the Materialized View is done on each block of inserted data in the original Data Source, some operations such as GROUP BY, ORDER BY, DISTINCT and LIMIT might need a specific engine, such as AggregatingMergeTree or SummingMergeTree, which can handle data aggregations.

- The Data Source resulting from a Materialized View generated using JOIN is automatically updated only if and when a new operation is performed over the Data Source in the FROM.