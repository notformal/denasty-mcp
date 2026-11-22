# denasty-mcp

Documentation and configuration snippets for **DeNasty Memory**, a hosted remote MCP server that gives your AI coding
tools one shared memory per project.

This repository contains **docs and config examples only**. The server is a hosted, centralized service operated by
Denha Nexus s.r.o. (Slovakia, EU); its source is not in this repository. There are no secrets here and you should never
commit your token.

- Server URL: `https://denasty.me/v1/mcp`
- Transport: MCP Streamable HTTP, JSON responses, stateless (`GET` returns 405, use `POST`)
- Auth: OAuth 2.1 sign-in window (URL alone is enough in clients that support it), or
  `Authorization: Bearer dn_pat_...` personal access token from https://denasty.me/connect
- Registry name: `me.denasty/memory` in the official MCP Registry 
- Support: info@denasty.me

## What it does

Each AI tool forgets what you told the last one. DeNasty Memory stores decisions, conventions, context notes and open
tasks per project, and any connected MCP client can read and write them.

| Tool | Title | Hints | Purpose |
| --- | --- | --- | --- |
| `recall` | Recall project memory | read-only | What is remembered about a project; optional `query`, `kind`, `limit` |
| `remember` | Remember for this project | writes, not destructive | Save one decision / convention / context / todo / note (`project`, `kind`, `title`, `body`, `tags`) |
| `list_projects` | List projects | read-only | Projects with counts and last update |
| `project_brief` | Project brief (Markdown) | read-only | Memory as a Markdown section for CLAUDE.md, AGENTS.md or Cursor rules |
| `my_preferences` | My preferences | read-only | Personal preferences and writing rules you chose to share with AI tools |
| `forget` | Forget a memory | destructive | Delete one memory by id. Cannot be undone |

`kind` is one of `decision`, `convention`, `context`, `todo`, `note`. The server sends usage instructions in the MCP
`initialize` result so agents know to call `recall` at the start of work and `remember` after decisions.

## Privacy and control (plain language)

- Centralized service. It is **not** decentralized, not zero-knowledge and **not end-to-end encrypted**.
- API keys, tokens, private keys and passwords are detected and removed before a memory is stored. This is pattern
  based and not perfect: do not paste secrets on purpose.
- You can see every memory and delete any of them at https://denasty.me/connect (and /memory). Tokens can be revoked
  there at any time; each token shows when and by which client it was last used.
- Memory is isolated per account.
- Privacy policy and terms: https://denasty.me/privacy and https://denasty.me/terms (currently drafts under legal review).

## Setup

1. Sign in at https://denasty.me and open **Connect**.
2. Create a token. It is shown once. Treat it like a password.
3. Add the server to your tool (replace `YOUR_TOKEN`; prefer environment variables or prompts over files you commit).

### Claude Code

```bash
claude mcp add --transport http --scope user denasty https://denasty.me/v1/mcp \
  --header "Authorization: Bearer YOUR_TOKEN"
```

### VS Code (`.vscode/mcp.json`)

VS Code prompts for the token once, so it does not sit in a file you might commit.

```json
{
  "inputs": [
    { "type": "promptString", "id": "denasty-token", "description": "DeNasty token", "password": true }
  ],
  "servers": {
    "denasty": {
      "type": "http",
      "url": "https://denasty.me/v1/mcp",
      "headers": { "Authorization": "Bearer ${input:denasty-token}" }
    }
  }
}
```

### Cursor (`~/.cursor/mcp.json` or `.cursor/mcp.json`)

```json
{
  "mcpServers": {
    "denasty": {
      "url": "https://denasty.me/v1/mcp",
      "headers": { "Authorization": "Bearer YOUR_TOKEN" }
    }
  }
}
```

Use the global file for a token; do not commit a project-level file that contains it.

### Codex CLI (`~/.codex/config.toml`)

```toml
[mcp_servers.denasty]
url = "https://denasty.me/v1/mcp"
http_headers = { Authorization = "Bearer YOUR_TOKEN" }
```

### Gemini CLI

```bash
gemini mcp add --scope user --transport http --header "Authorization: Bearer YOUR_TOKEN" denasty https://denasty.me/v1/mcp
```

### Zed (`settings.json`)

```json
{ "context_servers": { "denasty": { "url": "https://denasty.me/v1/mcp", "headers": { "Authorization": "Bearer YOUR_TOKEN" } } } }
```

### Cline

```json
{
  "mcpServers": {
    "denasty": {
      "type": "streamableHttp",
      "url": "https://denasty.me/v1/mcp",
      "headers": { "Authorization": "Bearer YOUR_TOKEN" }
    }
  }
}
```

### Windsurf and other apps that only start local programs (stdio)

Use the `mcp-remote` bridge:

```json
{
  "mcpServers": {
    "denasty": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://denasty.me/v1/mcp", "--transport", "http-only", "--header", "Authorization:${AUTH_HEADER}"],
      "env": { "AUTH_HEADER": "Bearer YOUR_TOKEN" }
    }
  }
}
```

### xAI Grok API (Python)

```python
from xai_sdk.tools import mcp

tools = [mcp(server_url="https://denasty.me/v1/mcp", server_label="denasty",
             extra_headers={"Authorization": "Bearer YOUR_TOKEN"})]
```

> Snippets are the ones generated by the DeNasty Connect page. Only some clients have been exercised end to end by the
> maintainers; if one fails, open an issue.

## Raw protocol check

```bash
curl -s https://denasty.me/v1/mcp \
  -H "Authorization: Bearer YOUR_TOKEN" -H "Content-Type: application/json" \
  -H "Accept: application/json, text/event-stream" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}'
```

Without a valid token the server answers `401`. Limit: 120 requests per minute.

## Example prompts

- "Check DeNasty for what we decided about authentication in `my-app`."
- "Remember in `my-app`: we use UUIDv7 for ids because of index locality."
- "Give me a CLAUDE.md section for `my-app` from DeNasty."
- "List the projects DeNasty has memory for."

## Not yet available

A public listing in the Anthropic and OpenAI directories (custom connector by URL works today),
Chrome Web Store availability.

## License

Documentation and snippets in this repository: MIT. DeNasty the service is governed by its own terms.
