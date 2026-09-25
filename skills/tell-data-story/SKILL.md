---
name: tell-data-story
description: >
  Use when a SpurLab analysis should become a narrative: sequencing markdown
  and chart cells into a data story, titling charts with the insight,
  annotating highlights and baselines, honest framing, or choosing a story
  pattern (before/after, timeline, rankings, deviation, part-to-whole,
  indexed comparison).
license: MIT
compatibility: Requires a SpurLab or Jute notebook session with the notebook MCP server (notebook_* tools).
---

# Tell a data story

**Required:** follow `notebook-analyst`. The numbers come from `live-query` and `sql-patterns`; the charts from `ggsql-visualize`; the reader-driven Explore surface from `perspective-explore`. This skill owns narrative order, chart titling, annotation, and honest framing.

## Arc

A notebook story is Context → Question → Evidence → Insight, one cell at a time. The notebook scroll is the progression.

1. Markdown cell: context and the question. 2–3 sentences: why this matters now. Open with the surprising finding or the human impact, not the topic.
2. Evidence: one `code_type=sql` + `ggsql` cell per finding. One message per chart; if its point needs two sentences, split the cell.
3. Markdown cell: the takeaway, the limitations, the data source. State the main insight in one sentence the reader could repeat.

Title every chart with the insight, not the contents: `LABEL title => 'Cash rides report near-zero tips'`, not `Tip rate by payment type`.

## Annotate with ggsql

| Technique | ggsql |
|---|---|
| Insight title, axis meaning | `LABEL title => '…', x => '…', y => '…'` |
| Direct labels on key points | `text` layer |
| Baseline, benchmark, target | `rule` layer, or a constant series computed in SQL |
| Highlight the in-scope subset | extra `point` / `line` / `smooth` layer over the full series (`composition`) |
| Shaded period or envelope | `rect` / `area` / `ribbon` layer |
| Small multiples | `FACET` (`composition`) |
| Distinguish series | map the category column to `fill` |
| Value labels on bars | `text` layer over `bar` |

Annotate the insight, the outliers, the inflection points, and the events. Do not annotate what is obvious, and do not annotate everything.

## Frame in the SQL

- Put the comparison in the query: prior period, historical average, or peer group as a column next to the current value.
- Make the denominator explicit — compute `rate AS tips / fares` in SQL and show both counts where the rate could mislead.
- Show the full relevant window. Focus by highlighting a period, never by trimming the data to flatter the trend.
- Absolute and relative together: `value` and `pct_change` columns, both charted or labeled.
- Note limitations in the closing markdown: source, sample, missing data, selection criteria.

## Chart vs Explore

Each evidence cell has two surfaces with one job each: the **Chart** tab is the authored, static insight; the **Explore** tab (Perspective over the Arrow snapshot) is where the reader re-pivots the full honest window. Keep every dimension in the snapshot for Explore, and shape it per `perspective-explore`. The closing markdown may suggest one pivot to try — one, not a tutorial.

## Pattern matrix

| Message | Pattern | ggsql shape |
|---|---|---|
| Change over time | Timeline | `line`; label at most 5–7 key events |
| Before vs after | Same scale, two conditions | grouped `bar` with `fill`, or `FACET` — identical scales, same chart type |
| Biggest / smallest | Rankings | `bar` sorted in SQL, horizontal via `PROJECT y, x TO cartesian`, value labels via `text` |
| Deviation from a norm | Diverging | split by sign into a category column in SQL, `bar` with `fill` by sign |
| Composition | Part-to-whole | stacked `bar`; polar only with ≤6 slices and a `dual_output_ready` check |
| Spread of a variable | Distribution | `histogram`, `boxplot`, `violin` (`distribution_stats`) |
| Two metrics, two units | Indexed lines | index both series to 100 at the baseline date in SQL, two `line` layers |
| Volume vs value | Segment bars | `bar` + `FACET` (volume segments beside value segments) |
| Density by a pair of bins | Heatmap | `tile` (`grid_marks`) |

Geographic charts are capability-gated in SpurLab v1 (`spatial_crs` unavailable) — do not promise a map.

Combine at most two patterns per story section. More creates overload.

## Progression rules

- Between consecutive charts, change one thing: highlight, or annotate, or add a series — not all three.
- Each chart earns its cell: a chart with no stated point is a data dump, not evidence.
- Review before calling the story done: insight visible in ~5 seconds per chart, story self-contained, window honest, limitations noted. A chart that fails this is revised or cut.
