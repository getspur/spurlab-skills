---
name: explore-data
description: >
  Use when a SpurLab datasource is already connected and the task is to
  understand it: find the grain, join keys, date columns, measures, null rates,
  or value ranges before analysis, charts, or promotion.
---

# Explore data

**Required:** follow `notebook-analyst`. The relation must already be in the catalog. `profile-source` owns the leaf schema; this skill owns the exploration order and the schema map.

## Order

1. `notebook_context_pack`, then `notebook_catalog` to the table leaf. The leaf carries the invoke syntax for cells.
2. One shape cell: `SELECT * FROM <relation> LIMIT 10`, or one table-function page. Read actual values before writing analytics.
3. One profiling cell per open question, not one sweep:
   - `SUMMARIZE <relation>` for ranges, cardinality, null rates.
   - `COUNT(DISTINCT …)` on candidate keys to test the grain.
   - `min` / `max` on date or timestamp columns to bound the window.
4. Report the schema map before any analytical SQL: grain, join keys, date columns, likely measures, and any column unsafe to aggregate.

## Rules

- Bound every exploration cell: `LIMIT`, `SUMMARIZE`, or one TF page. An unbounded read of a large relation stops for confirmation.
- Confirm grain before joins: compare row count against the distinct key count, or count rows before and after the join.
- Trust the leaf schema over guesses. Re-descend the catalog when a column is missing; do not invent a `ds://` path.
- Explore top-down: connection, then table, then columns. Analytical SQL waits until the step 4 map exists.
- Nested types: sample the column first (`LIMIT 5`), then `UNNEST` the list or select struct fields by path.

## Hand off

`live-query` owns the analytical SQL, `sql-patterns` its shapes, `ggsql-visualize` the chart, `promote-dataset` the name. On a failed or stale run, `notebook_lineage` from the returned ref.
