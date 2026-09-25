---
name: lakehouse-connection
description: >
  Use when attaching or probing an Iceberg or Delta table or catalog, including
  a REST, Glue, Nessie, or Unity catalog, in a SpurLab notebook.
---

# Lakehouse connection

**Required:** follow `notebook-analyst`. Probe first. Attach only after the probe succeeds.

## Probe

`notebook_test_lakehouse`

| Field | Value |
|---|---|
| `name` | Probe name |
| `format` | `iceberg` or `delta` |
| `location` | Local table root or object-store URI (`s3://`, `gs://`) |
| `mode` | `table_scan` (default) or `catalog` |
| `catalog_provider` | `none`, `rest`, `glue`, `nessie`, or `unity`. Any value other than `none` forces catalog mode. |
| `credential_ref` | Optional object-store or catalog profile |

## Attach

`notebook_attach_lakehouse` with the same `name`, `format`, `location`, `mode`, and `catalog_provider`. The attach is read-only. A table root uses `table_scan`. A warehouse root uses `catalog`.

Continue at `notebook_catalog`. Partition and snapshot filters belong in the later SQL scan, not in a landed Parquet copy.
