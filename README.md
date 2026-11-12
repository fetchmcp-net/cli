# FetchMCP CLI

Fetch MCP setup, client by client, checked and dated.

`fetchmcp` fetches live web pages as clean Markdown, text or HTML, straight from your terminal or your agent's shell.
It is the command line client for the [FetchMCP API](https://fetchmcp.net).

## Quick start

```bash
npx @fetchmcp-net/cli login                                   # paste your API key (not echoed)
npx @fetchmcp-net/cli fetch https://example.com -o text       # the page as Markdown
```

Get a key at [fetchmcp.net](https://fetchmcp.net). Node.js 18 or later.

## Install

```bash
npm install -g @fetchmcp-net/cli
fetchmcp --help
```

## Use

```bash
fetchmcp fetch https://example.com/a https://example.com/b      # up to 10 URLs; JSON with results[] and statuses[]
fetchmcp fetch https://example.com -o text                      # just the content (Markdown, else text, else HTML)
fetchmcp fetch https://example.com -o html --html               # the page HTML
fetchmcp fetch https://example.com --country-code de --render   # from Germany, rendered in a real browser
fetchmcp fetch --data @request.json                             # the whole request body from a file (- for stdin)
fetchmcp account                                                # plan, credits and rate limit
fetchmcp fetch --help                                           # every option, with the API's own descriptions
```

A fetch that is still running after about 100 seconds is collected automatically with the same
`Idempotency-Key`, so it is charged once.

## MCP server

The FetchMCP API is also a remote MCP server (Streamable HTTP) at `https://fetchmcp.net/mcp`, with the tools
`get_page_contents` and `get_account_status`. It takes the same API key as a bearer token.

```bash
claude mcp add --transport http fetchmcp https://fetchmcp.net/mcp \
  --header "Authorization: Bearer $FETCHMCP_API_KEY"
```

Any other client: URL `https://fetchmcp.net/mcp`, header `Authorization: Bearer <key>`. Config for Cursor, VS Code and
Claude Desktop: [MCP docs](https://fetchmcp.net/docs/mcp). The server's entry for the MCP Registry is [server.json](server.json)
(`net.fetchmcp/fetchmcp`).

## Agent plugin and skill

This repo is also a plugin for Claude Code, Grok Build and Gemini CLI, and holds an agent skill. Each one connects the
MCP server above and teaches the agent when to fetch a page with it or with `fetchmcp`.

```bash
npx skills add fetchmcp-net/cli                                    # the skill, for any agent that reads SKILL.md
claude plugin marketplace add fetchmcp-net/cli && claude plugin install fetchmcp@fetchmcp
gemini extensions install https://github.com/fetchmcp-net/cli
```

Network and credentials: the plugin calls `https://fetchmcp.net/mcp` only. The CLI calls `https://fetchmcp.net/api` and
`https://fetchmcp.net/openapi.json`, and `https://registry.npmjs.org` (`npx` and a once-a-day update check, off with
`FETCHMCP_NO_UPDATE_NOTIFIER=1`). Both need one credential, your FetchMCP API key,
read from `FETCHMCP_API_KEY` or saved by `fetchmcp login`. No hooks, no scripts run on install.

## The commands follow the live API

`fetchmcp` builds its commands and options from the API's OpenAPI document at
[https://fetchmcp.net/openapi.json](https://fetchmcp.net/openapi.json), cached for an hour. When the API gains a field or an endpoint, the
option or command is there on your next run, without updating the CLI. Offline, it uses the last cached
copy, then the copy bundled with the installed version (`fetchmcp spec` shows which one; `--offline` skips the
download). An hourly job here publishes a new version whenever the API spec changed.

## Configuration

| Setting | Option | Environment | Default |
|---|---|---|---|
| API key | `--api-key` | `FETCHMCP_API_KEY` | the key saved by `fetchmcp login` (`~/.config/fetchmcp/config.json`, mode 0600) |
| API base URL | `--base-url` | `FETCHMCP_API_BASE_URL` | `https://fetchmcp.net/api` |
| API spec URL | `--spec-url` | `FETCHMCP_SPEC_URL` | `https://fetchmcp.net/openapi.json` |
| Output | `-o json\|text\|<field>` | | `json` |
| Update notice | | `FETCHMCP_NO_UPDATE_NOTIFIER=1` turns it off | once a day, terminals only |

Requests and your key only ever go to the base URL, never to a server named in the spec.

## Exit codes

| Code | Meaning |
|---|---|
| 0 | OK |
| 1 | The API answered with an error (JSON on stderr, with its `requestId`), every URL failed with `-o text`, or the fetch was still running at `--max-wait` |
| 2 | Usage: unknown command or option, a value the API does not accept |
| 3 | Network: no answer. The error carries the `Idempotency-Key` to collect the result without a new charge |

## Links

- [fetchmcp.net](https://fetchmcp.net)
- [CLI docs](https://fetchmcp.net/docs/cli)
- [API docs](https://fetchmcp.net/docs)
- [API spec](https://fetchmcp.net/openapi.json)

## License

MIT, © fetchmcp.net
