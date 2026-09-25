---
name: profile-source
description: >
  Use when a SpurLab notebook datasource is already connected and the question
  is its schema, columns, invoke syntax, or a bounded sample.
license: MIT
compatibility: Requires a SpurLab or Jute notebook session with the notebook MCP server (notebook_* tools).
---

# Profile source

**Required:** follow `notebook-analyst`. The source must already be in the catalog.

## Call

`notebook_catalog` with the `ds://` ref from `notebook_context_pack` or the previous layer. Repeat until the response is a table leaf. The leaf includes column schema and the invoke syntax for a cell.

Use that invoke syntax in the SQL cell. Keep the sample bounded (`LIMIT`, `SUMMARIZE`, or one page of a table function). A count that would walk every API page reports the page cap instead.

`notebook_lineage` with that `ds://` ref shows what already consumes it. Direction `upstream` or `downstream`, depth defaults to 3.
