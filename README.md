# mcp-istat-it

ISTAT (Istituto Nazionale di Statistica) MCP — Italy's national statistics

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1476+ live data sources.

## Tools

| Tool | Description |
|------|-------------|
| `list_dataflows` | Browse or keyword-search ISTAT datasets (dataflows). Each result has an `id` (the dataflowId you pass to get_data / dataflow_structure) and an English name. ISTAT publishes ~4,800 datasets, so always pass `query` to filter unless you really want the whole list. Example: list_dataflows({ query: "unemployment" }) or list_dataflows({ query: "GDP" }). |
| `dataflow_structure` | Get the structure (Data Structure Definition) of one ISTAT dataset: its ordered dimensions and, for each, the valid codes. Use this to learn how to build the dot-separated SDMX key for get_data. The key positions correspond to the dimensions in order; an empty position is a wildcard. Example: dataflow_structure({ dataflow_id: "101_1015" }). |
| `get_data` | Pull observations from an ISTAT dataset. `key` is a dot-separated SDMX dimension filter, one position per dimension in the order given by dataflow_structure; leave a position empty to wildcard it. Example: get_data({ dataflow_id: "101_1015", key: "A.IT...", start_period: "2021", end_period: "2023" }) selects annual (A), REF_AREA=IT (Italy), and wildcards the rest. Omit `key` (or pass "") to fetch all series — caution, this can be large. Returns decoded series with their dimension labels and per-period values. |

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "istat-it": {
      "url": "https://gateway.pipeworx.io/istat-it/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/istat-it/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 1476+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Istat It data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
