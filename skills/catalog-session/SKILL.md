---
name: catalog-session
description: >
  Use when the open SpurLab or Jute notebook should be inspected for datasources,
  connections, tables, or ds:// refs before a query.
license: MIT
compatibility: Requires a SpurLab or Jute notebook session with the notebook MCP server (notebook_* tools).
---

# Catalog session

**Required:** follow `notebook-analyst` for order. This skill only reads the catalog.

## Calls

1. `notebook_context_pack` with no arguments.
2. `notebook_catalog` with `ref` omitted for the first layer.
3. Pass the returned `ds://` ref to descend. File kinds are leaves at layer 2. Stop at the first table leaf.
4. `scope` is `all`, or `used` when the question is only relations already wired into this notebook.

`notebook_list_datasources` is the flat list. Use it when a one-layer descent is not needed. Carry refs from the response. Do not invent a `ds://` path.
