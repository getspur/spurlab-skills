---
name: sql-patterns
description: >
  Use when writing analytical DuckDB SQL in a SpurLab cell: aggregation shapes,
  latest-row, top-N, dedup, running totals, period-over-period, pivot, sampling
  — or when porting SQL from another engine.
license: MIT
compatibility: Requires a SpurLab or Jute notebook session with the notebook MCP server (notebook_* tools).
---

# SQL patterns

**Required:** follow `notebook-analyst`. The cell mechanics live in `live-query`. Write DuckDB SQL, not PostgreSQL. The full library is `references/SQL_PATTERNS.md`.

## Defaults

- Friendly SQL first: `FROM`-first, `GROUP BY ALL`, `EXCLUDE` / `REPLACE`, `QUALIFY`, `FILTER (WHERE …)`.
- CTEs over nested subqueries; filter early inside the CTE, not at the end.
- Filters on a table-function source go in the TF arguments (`source(arg := …)`); residual `WHERE` stays in DuckDB. Keep `tf_invocations * pages_per_tf` at or under 20.
- `SELECT *` only in exploration cells; list columns in analytical SQL.
- A shape that repeats deserves `promote-dataset`, not a `CREATE TABLE AS`.

## Quick reference

| Question | Pattern |
|---|---|
| Latest row per key | `arg_max(col, ts)` or `ROW_NUMBER() OVER (PARTITION BY … ORDER BY ts DESC)` + `QUALIFY … = 1` |
| Top N per group | `RANK() OVER (PARTITION BY … ORDER BY metric DESC)` + `QUALIFY … <= N` |
| Dedup | `ROW_NUMBER() OVER (PARTITION BY id ORDER BY ingested_at DESC)` + `QUALIFY … = 1` |
| Running total | daily CTE, then `SUM(…) OVER (ORDER BY day)` |
| Period over period | month CTE, self-join on month, ratio delta |
| Conditional aggregate | `SUM(x) FILTER (WHERE status = 'completed')` |
| Wide from long | `PIVOT t ON quarter USING SUM(revenue) GROUP BY region` |
| Long from wide | `UNPIVOT` or `UNION BY NAME` for drifted schemas |
| Nearest event | `ASOF JOIN events ON a.id = b.id AND a.ts >= b.ts` |
| Big-relation preview | `USING SAMPLE 1 PERCENT (bernoulli)` |

Read `references/SQL_PATTERNS.md` for the full SQL of each pattern, join-grain checks, and common mistakes.
