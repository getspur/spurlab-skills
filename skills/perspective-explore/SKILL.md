---
name: perspective-explore
description: >
  Use when shaping a SpurLab SQL cell result for the Explore tab (Perspective
  viewer over the Arrow snapshot): pivot-friendly column shapes, date-part
  columns, bounded snapshots, or when connecting a data story's charts to a
  reader-driven exploration surface.
license: MIT
compatibility: Requires a SpurLab or Jute notebook session with the notebook MCP server (notebook_* tools).
---

# Perspective explore

**Required:** follow `notebook-analyst`. The SQL comes from `live-query`; the authored chart from `ggsql-visualize`; the story order from `tell-data-story`. This skill owns shaping a cell's result so the **Explore** tab pivots well.

## What Explore is

Every successful SQL cell output carries an Arrow snapshot. The dual output's Explore tab mounts a full `<perspective-viewer>` on it: Datagrid by default, settings panel open. The reader drives it — `group_by`, `split_by`, aggregates, filters, sort, expressions, and chart plugins are theirs to choose, and their presentation choices survive cell re-runs. The agent never authors the viewer config; the agent shapes the snapshot and, in the story, says what to try.

## Shape the snapshot

| Rule | Why |
|---|---|
| Unique column names — alias every output | Duplicate names make Explore fail closed on that cell |
| One row per observation grain, long form | `group_by` / `split_by` need a grain to pivot; pre-pivoted wide output can only be re-filtered |
| Dimensions as short strings, measures as numeric | Strings pivot and split; only numeric aggregates (text columns lose aggregation) |
| Date parts as own columns: `year`, `month`, `ym` | Timestamps, dates, and nested types arrive as text — group on derived parts instead, ISO-sorted strings still sort correctly |
| Integers within ±2^53, or cast to `DOUBLE` | Larger integers degrade to text |
| Keep expressions out of the snapshot | Compute rate, delta, index in SQL (`sql-patterns`) — every reader gets the same derived columns |
| Bound the snapshot | The snapshot lives in the cell output. Aggregate or `USING SAMPLE` when the row count is large; a story needs evidence, not the warehouse |

## Story split: Chart vs Explore

In a `tell-data-story` arc, each evidence cell uses both surfaces for one job each:

- **Chart tab (ggsql)** — the authored insight: one message, insight `LABEL title`, annotation layers. The reader should get the point without touching anything.
- **Explore tab (Perspective)** — the honesty surface: full relevant window, every dimension left in for re-pivoting. Where the reader verifies the story rather than being told it.

The closing markdown cell of a story may direct one exploration: "In Explore, group by region and split by year — the cash-tip gap is uniform, not local." One suggested pivot, not a tutorial.

## Pattern → suggested first pivot

| Story pattern | Reader's first move in Explore |
|---|---|
| Rankings | Y Bar plugin, `group_by` the category, aggregate `sum`, sort descending |
| Timeline | X Line plugin, x the ISO date-part string, y the measure |
| Part-to-whole | Treemap or Sunburst plugin, `group_by` the hierarchy levels |
| Deviation | Y Bar, `split_by` the sign/category column |
| Distribution | histogram is authored in ggsql Chart; in Explore, Datagrid + sort exposes the tails |
| Two metrics | Scatter plugin, x one measure, y the other, `split_by` a category |
| Density | Heatmap plugin, `group_by` one binned axis, `split_by` the other |

Plugin names come from the reader's plugin picker (Datagrid, Y Bar, X Line, Scatter, Heatmap, Treemap, Sunburst, and market-data plugins where loaded) — describe, don't promise an exhaustive registry.

## Limits

- Explore never writes back: pivots, filters, and expressions are presentation on the snapshot. A derived relation readers keep asking for belongs in its own cell and `promote-dataset`.
- On snapshot schema change (columns renamed, types changed), the reader's saved presentation resets to Datagrid — keep column names stable across story revisions.
- A failed snapshot does not fail the cell; Table (and Chart) remain. Fix duplicates and exotic types in the SQL and re-run.
