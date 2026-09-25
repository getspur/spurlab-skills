---
name: notebook-analyst
description: >
  Use when analyzing, querying, profiling, or connecting data in a SpurLab or
  Jute notebook: a file, Postgres, MySQL, SQL Server, BigQuery, Snowflake,
  Iceberg, Delta, or a REST API table.
---

# Notebook analyst

The open notebook catalog is the session. One connection uses exactly one attach tool. Query and naming stay on the SQL cell.

Load the sibling named below for arguments. This skill owns only the order.

## Order

1. `notebook_context_pack`
2. One attach tool, and only when the source is not already in the catalog
3. `notebook_catalog` until the table leaf
4. `notebook_insert_cell` with `kind=code` and `code_type=sql`
5. `notebook_run_cell`
6. `notebook_dataset_promote` when the relation needs a name, or `notebook_lineage` when the run is failed or stale

## Which attach tool

| Source | Skill | Tool |
|---|---|---|
| CSV, Parquet, JSON, SQLite, DuckDB file | `file-scan` | `notebook_attach_datasource` |
| Postgres, MySQL, SQL Server, BigQuery, Snowflake | `warehouse-connection` | `notebook_attach_database` |
| Iceberg or Delta | `lakehouse-connection` | `notebook_test_lakehouse` then `notebook_attach_lakehouse` |
| REST provider manifest | `api-connection` | `notebook_navigate_api_providers`, `notebook_rest_catalog_check`, `notebook_add_api_connection` |
| Polymarket or RSS | `api-connection` | `notebook_add_api_datasource` |

A manifest connection and `notebook_add_api_datasource` are different connections. Do not call both for the same name.

## Already connected

Skip step 2. Continue at `notebook_catalog`. Use `catalog-session` when the question is only "what can I query?"

## Query and name

`explore-data` owns the schema map before analytics. `live-query` owns the SQL cell. `sql-patterns` owns the pattern library. `profile-source` owns the leaf schema. `promote-dataset` owns the name. `ggsql-visualize` owns the chart on that cell: `notebook_ggsql_check` before `notebook_run_cell`.
