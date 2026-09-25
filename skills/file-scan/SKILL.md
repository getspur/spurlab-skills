---
name: file-scan
description: >
  Use when attaching a local CSV, Parquet, JSON, SQLite, or DuckDB file to the
  active SpurLab notebook catalog.
---

# File scan

**Required:** follow `notebook-analyst`. Call this only when the file is absent from the catalog.

## Call

`notebook_attach_datasource`

| Field | Value |
|---|---|
| `name` | Catalog name, non-empty |
| `path` | Local file path, non-empty |
| `group` | Optional catalog group |

The tool schema probe accepts CSV, Parquet, JSON, SQLite, and DuckDB. After it returns, continue at `notebook_catalog`.

Postgres, MySQL, SQL Server, BigQuery, Snowflake, Iceberg, Delta, and REST providers use their own skills.
