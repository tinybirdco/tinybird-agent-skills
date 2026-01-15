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
