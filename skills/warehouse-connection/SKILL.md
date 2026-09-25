---
name: warehouse-connection
description: >
  Use when attaching Postgres, MySQL, SQL Server, BigQuery, or Snowflake to the
  active SpurLab notebook catalog.
license: MIT
compatibility: Requires a SpurLab or Jute notebook session with the notebook MCP server (notebook_* tools).
---

# Warehouse connection

**Required:** follow `notebook-analyst`. One engine per connection.

## Call

`notebook_attach_database`

| Field | Value |
|---|---|
| `name` | Catalog name |
| `engine` | `postgres`, `mysql`, `sqlserver`, `bigquery`, or `snowflake` (aliases `pg`, `mariadb`, `mssql`, `bq`, `sf` are accepted) |
| `credential_ref` | Existing credential profile name |
| `credentials` | Key/value pairs only when no profile exists |
| `group` | Optional |

Pass a profile name when one exists. BigQuery public data uses `PROJECT_ID` as the billing project, `DATASET_ID` for the dataset, `SOURCE_PROJECT_ID=bigquery-public-data` when the data is public, and `AUTH_TYPE=adc`.

Do not copy the remote database into a local file. Profile through `notebook_catalog` after the attach returns.
