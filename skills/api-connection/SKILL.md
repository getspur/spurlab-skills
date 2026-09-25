---
name: api-connection
description: >
  Use when adding a REST API table connection, checking a connection manifest,
  or attaching the Polymarket or RSS shortcut in a SpurLab notebook.
---

# API connection

**Required:** follow `notebook-analyst`. A named connection takes one of the two paths below.

## Provider manifest

1. `notebook_navigate_api_providers` with `query`, or `root` of `category:<name>`, `<provider_key>`, or `<provider_key>/<table>`.
2. `notebook_rest_catalog_check` with the full `manifest_toml`. Env var names only. No credential values.
3. `notebook_add_api_connection` with `name` and `manifest_toml`. The same name again requires `confirm_replace=true`.
4. `notebook_api_connection_status` with that `name` to read the table-function index.
5. `notebook_oauth_connect` with that `name` when the saved connection needs browser OAuth.

`include_manifest` defaults off. Set it only on a single connection hop.

## Polymarket or RSS

`notebook_add_api_datasource` with `name` and `source` of `polymarket` or `rss`. This path does not take a manifest. Do not also call `notebook_add_api_connection` for that same name.

Remote filters later belong in the table-function arguments. `WHERE` is residual DuckDB. An action is not a profile target.
