---
name: promote-dataset
description: >
  Use when a SpurLab SQL cell should become a named live dataset or a catalog
  ds:// derived relation, without copying rows into a warehouse.
license: MIT
compatibility: Requires a SpurLab or Jute notebook session with the notebook MCP server (notebook_* tools).
---

# Promote dataset

**Required:** follow `notebook-analyst`. The SQL cell from `live-query` is the definition.

## Call

`notebook_dataset_promote`

| Field | Value |
|---|---|
| `cell_id` | The SQL cell |
| `sql` | That cell's current source |
| `name` | Optional. Defaults from the cell id |
| `action` | `catalog` (default) or `establish` |
| `copies_bytes` | `false` |
| `source_refs` | Optional `ds://` refs the SQL reads |
| `port` | Optional produced port name |

`action=ducklake` is later-only. Do not substitute a `CREATE TABLE AS`, Iceberg landing, or SQLite copy. Re-running the same cell refreshes the named dataset. The catalog result is `ds://…/v2/derived/<name>`.
