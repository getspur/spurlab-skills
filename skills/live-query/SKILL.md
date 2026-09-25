---
name: live-query
description: >
  Use when running SQL, or a natural-language question that should become SQL,
  against a datasource already connected in a SpurLab notebook.
---

# Live query

**Required:** follow `notebook-analyst`. The relation comes from `profile-source` or a catalog leaf.

## Cell

`notebook_insert_cell`

| Field | Value |
|---|---|
| `mutation_id` | New UUID |
| `kind` | `code` |
| `code_type` | `sql` |
| `source` | The SQL text |
| `notebook_path` or `notebook_id` | The writer-owned notebook |

Put remote filters in table-function arguments (`source(arg := …)`). Residual `WHERE` stays in DuckDB. Keep `tf_invocations * pages_per_tf` at or under 20.

Friendly SQL is preferred: `FROM` first, `GROUP BY ALL`, `EXCLUDE` / `REPLACE`, `SUMMARIZE`, `PIVOT`. An unbounded read of a large relation stops for confirmation. Show at most 100 rows in the conversation.

## Run

`notebook_run_cell` with the returned `cell_id`. Re-read the cell on a version conflict. Do not open a second client beside an existing table function.

A chart on this cell: `notebook_ggsql_check` first, then pass `ggsql` on the insert. A published tool: pass `gateway.name` on the insert. `notebook_gateway_check` can preflight that declaration before the insert.
