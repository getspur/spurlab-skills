---
name: ggsql-visualize
description: >
  Use when a SpurLab SQL result should also render as a chart in the same cell:
  dual Table|Visualize output through ggsql, from gallery or recipe template to
  a validated run.
---

# ggsql visualize

**Required:** follow `notebook-analyst`. The SQL comes from `live-query` or `explore-data`. SQL stays pure SQL; the visualise clause lives in `metadata.spur.ggsql`.

## Workflow

1. `notebook_ggsql_catalog_search` — find a recipe or gallery example for the question shape (histogram, rate series, top-N bars, heatmap).
2. `notebook_ggsql_catalog_get` — copy its `visualise` template and `metadata_template`. `notebook_ggsql_spec` answers profile and layer coverage.
3. `notebook_ggsql_check` with `{enabled: true, visualise, profile?, output?, width?, height?}`. Fix every diagnostic before any kernel spend. Fail closed: no pass, no run.
4. `notebook_insert_cell` with `kind=code`, `code_type=sql`, the SQL in `source`, and the `ggsql` seed `{enabled: true, visualise, output: "vega_spec", width, height}`. One mutation writes SQL and chart metadata together.
5. `notebook_run_cell`. Claim dual Table|Visualize output only when the recipe or gallery entry had `dual_output_ready: true`.

## Syntax

- `visualise` maps result columns to aesthetics and one DRAW layer: `species AS x, count AS y DRAW bar`.
- Layer names come from the profile: `point`, `line`, `path`, `bar`, `text`, `rule`, `segment`, `range`, `histogram`, `density`, `boxplot`, `violin`, `smooth`, `tile`, `area`, `ribbon`. There is no `scatter` layer — a scatterplot is `DRAW point`.
- Auto-count bars: omit `y` and `DRAW bar` counts occurrences.
- `LABEL title => '…', x => '…', y => '…'` titles the chart and axes.
- Multi-layer charts and FACET small multiples use profile `composition`.

## Profiles

| Profile | Covers | Dual output |
|---|---|---|
| `basic_marks` | point, line, bar | ready |
| `distribution_stats` | histogram, density, boxplot, violin | ready |
| `grid_marks` | heatmap tiles | ready |
| `composition` | multi-layer, FACET | ready |
| `polar_coord` | pie, polar | runtime risk — verify `dual_output_ready` before promising |
| `advanced_narrative`, `spatial_crs` | Minard, spatial | capability-gated |

## Size

Default 800×480. Clamps 320–1200 wide, 240–800 high. Charts are never container-sized; pick explicit `width` / `height`.

## Mistakes

- Skipping `notebook_ggsql_check` and spending a kernel run on invalid metadata.
- Inventing layers or profiles not listed in `notebook_ggsql_spec`.
- Putting the `VISUALISE` clause into the SQL `source` — in Spur it belongs in the `ggsql` seed.
- Promising dual output for `polar_coord` without checking.
