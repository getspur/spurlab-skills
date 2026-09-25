# spurlab-skills

[![skills.sh](https://skills.sh/b/getspur/spurlab-skills)](https://skills.sh/getspur/spurlab-skills)

Open-source analyst skills for a SpurLab / Jute notebook. The session is the notebook catalog. SQL cells are the dataset definition. One connection uses one attach tool.

Follows the [Agent Skills spec](https://agentskills.io); installable for 80+ agents through the open [skills CLI](https://skills.sh) — Claude Code, Codex, Cursor, OpenCode, Gemini CLI, Copilot, Amp, and more.

## Install

Any agent, through the skills CLI:

```sh
npx skills add getspur/spurlab-skills
```

Target one agent or a subset:

```sh
npx skills add getspur/spurlab-skills -a claude-code -a codex -a cursor
npx skills add getspur/spurlab-skills --skill notebook-analyst --skill ggsql-visualize
npx skills add getspur/spurlab-skills --list
```

Claude Code plugin marketplace:

```text
/plugin marketplace add getspur/spurlab-skills
/plugin install spurlab-skills@spurlab-skills
```

From a clone, point the agent at `skills/`.

Every skill needs a SpurLab or Jute notebook session with the notebook MCP server (`notebook_*` tools) connected.

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
| `explore-data` | You need to understand a connected source: grain, join keys, dates, measures, null rates. |
| `live-query` | You have a SQL question or a natural-language question over a connected relation. |
| `sql-patterns` | You are writing analytical DuckDB SQL: latest-row, top-N, dedup, running totals, YoY, pivot, sampling. |
| `ggsql-visualize` | A SQL result should also render as a chart in the same cell (dual Table\|Visualize via ggsql). |
| `tell-data-story` | An analysis should read as a narrative: cell order, insight titles, annotation layers, honest framing, story patterns. |
| `promote-dataset` | A SQL cell should become a named live dataset. |

Load `notebook-analyst` first. It chooses one sibling for the connection and keeps the later steps on notebook tools.

## Session rules

- Start from `notebook_context_pack`.
- Descend `notebook_catalog` one `ds://` layer at a time.
- Put the question in a `code_type=sql` cell, then `notebook_run_cell`.
- Name the result with `notebook_dataset_promote` on that same cell. `copies_bytes` stays false.
- Chart a result by seeding the cell's `ggsql` metadata from a gallery or recipe template, after `notebook_ggsql_check`.
- On a failed or stale run, walk `notebook_lineage` from the returned ref.

## License

MIT
