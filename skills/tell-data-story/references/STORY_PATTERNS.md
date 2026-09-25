# Story Patterns

Narrative templates and pattern library for SpurLab data stories. Ported from the visual-storytelling-design skill, adapted to notebook cells and ggsql charts.

## Contents

| Section | Covers |
|---|---|
| Story templates | Arcs over notebook cells |
| Pattern details | Per-pattern SQL shape and annotation |
| Pattern combinations | Mixing patterns without overload |
| Quality checklist | Final review gate |

## Story templates

### Step-by-step arc

Best for cause-effect, building to a conclusion.

```text
Cell 1  markdown  Hook + context (surprising finding or human impact)
Cell 2  chart     Overview: the full trend or population
Cells 3-5 chart   One finding per chart, each adding one thing
Cell 6  chart     Climax: the key insight, heavily annotated
Cell 7  markdown  Takeaway, limitations, source
```

### Annotated chart story

Best for one rich dataset.

1. One chart cell, clean.
2. Re-run the same SQL with one more layer per cell: insight `text`, baseline `rule`, shaded `rect` period, highlight layer.
3. Close with a markdown cell reading the chart in one sentence.

The reader scrolls through increasingly annotated versions of the same query; the story is the annotation sequence.

### Exploration story

Best when the audience should discover.

1. Markdown: the question and how to read the first chart.
2. A FACET or multi-layer chart showing all segments.
3. One chart per notable segment, filtered in SQL.
4. Markdown: what the segments share, what differs.

### Deck story

For presenting: same arc, but every markdown cell is one sentence and every chart carries the slide's full message in its `LABEL title`.

## Pattern details

### Timeline

- `line` over a date grain from a daily CTE; `text` layer on at most 5–7 events.
- Smooth the noise only in a separate series (`smooth` layer), never replacing raw.

### Before / after

- Identical scale and chart type across the two conditions; grouped `bar` with `fill`, or `FACET`.
- Annotate the delta: a `text` layer with the difference or percent change at the point of change.

### Rankings

- Sort in SQL (`ORDER BY` + window rank, see `sql-patterns`), horizontal bars via `PROJECT y, x TO cartesian`.
- Highlight the subject of interest with a category column mapped to `fill`; add a benchmark `rule`.

### Deviation

- Compute the delta in SQL, split into positive/negative (or above/below) category column, `bar` with `fill` by that column.
- Label the zero line and what deviation means in `LABEL`.

### Part-to-whole

- Stacked `bar` with direct labels; treemap-class charts are not in the v1 profiles — composition by `bar` or `FACET`.
- More than 6 slices: group the tail into an "other" bucket in SQL.

### Indexed comparison

- Two units, one story: index both series to 100 at a baseline date in SQL, draw two `line` layers, label the divergence.

## Pattern combinations

```text
Timeline + FACET        how a trend differs across regions
Rankings + before/after who improved most
Composition + timeline  how mix shifted over time (stacked bars per period)
Deviation + rankings    who deviates most from baseline
```

Maximum two patterns per story section.

## Quality checklist

```text
- [ ] Reader can state the main insight in one sentence
- [ ] Opening cell creates interest (finding or impact, not topic)
- [ ] Every chart's title states its point
- [ ] Each chart changes exactly one thing from the previous
- [ ] Baselines, denominators, and windows are honest
- [ ] Limitations and source in the closing markdown
- [ ] No chart exists only to display data
```

8–10 checks pass: ready. 5–7: revise. Under 5: restructure the arc.
