---
name: fetchmcp
description: Fetch a public web page live and get its main content as clean Markdown, plain text or HTML, through the FetchMCP API. Use when you have a specific URL and need what the page says now, for example docs, articles, product or listing pages, including pages that need a real browser to render.
---

# FetchMCP: live web pages as Markdown

Use this when the user gives you a URL, or you found one, and you need the page's current content. It does not search
the web, log in or submit forms.

## With the MCP server

If the `fetchmcp` MCP server is connected (`https://fetchmcp.net/mcp`), call its tools:

- `get_page_contents`: one URL in, the page's main content out.
- `get_account_status`: remaining credits, plan and rate limit. Check it before a large batch.

## With the CLI

No MCP server? Use the `fetchmcp` command (Node.js 18 or later). It needs the key in `FETCHMCP_API_KEY`, or
saved once with `npx @fetchmcp-net/cli login`.

```bash
npx @fetchmcp-net/cli fetch https://example.com -o text                    # Markdown, else text, else HTML
npx @fetchmcp-net/cli fetch https://example.com/a https://example.com/b    # up to 10 URLs, JSON with results[] and statuses[]
npx @fetchmcp-net/cli fetch https://example.com --render                   # rendered in a real browser
npx @fetchmcp-net/cli fetch https://example.com --country-code de          # from another country
npx @fetchmcp-net/cli account                                              # plan, credits and rate limit
npx @fetchmcp-net/cli fetch --help                                         # every option
```

## Rules

- Each fetched URL costs credits; do not fetch the same URL twice in one task.
- Exit code 1 means the API answered with an error (JSON on stderr), 2 a usage error, 3 no network answer.
- No key yet: tell the user to get one at https://fetchmcp.net. Never print or log the key.
- Docs: https://fetchmcp.net/docs
