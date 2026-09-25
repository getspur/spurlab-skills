# spurlab-skills

Open-source analyst skills for a SpurLab / Jute notebook. The session is the notebook catalog. SQL cells are the dataset definition. One connection uses one attach tool.

## Install

```text
/plugin marketplace add getspur/spurlab-skills
/plugin install spurlab-skills@spurlab-skills
```

From a clone, point the agent at `skills/`.

## Skills

| Skill | Use when |
|---|---|
| `notebook-analyst` | Any notebook data question. Owns the session trace and which sibling to load. |
| `catalog-session` | You need to see what the open notebook can already query. |
| `file-scan` | The source is a local CSV, Parquet, JSON, SQLite, or DuckDB file. |
| `warehouse-connection` | The source is Postgres, MySQL, SQL Server, BigQuery, or Snowflake. |
| `lakehouse-connection` | The source is an Iceberg or Delta table or catalog. |
| `api-connection` | The source is a REST provider, or the Polymarket / RSS shortcut. |
| `profile-source` | You need the schema and a bounded sample of a relation that is already connected. |
| `live-query` | You have a SQL question or a natural-language question over a connected relation. |
| `promote-dataset` | A SQL cell should become a named live dataset. |

Load `notebook-analyst` first. It chooses one sibling for the connection and keeps the later steps on notebook tools.

## Session rules

- Start from `notebook_context_pack`.
- Descend `notebook_catalog` one `ds://` layer at a time.
- Put the question in a `code_type=sql` cell, then `notebook_run_cell`.
- Name the result with `notebook_dataset_promote` on that same cell. `copies_bytes` stays false.
- On a failed or stale run, walk `notebook_lineage` from the returned ref.

## License

MIT
