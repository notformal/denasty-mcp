# DeNasty plugin for Claude Code

Gives Claude Code the DeNasty project memory: it recalls decisions and conventions when you start
work and remembers new ones, and the same memory is available in Cursor, VS Code, Codex CLI and
other tools you connect to your DeNasty account.

## Install

```
/plugin marketplace add notformal/denasty-mcp
/plugin install denasty@denasty
```

Then run `/mcp`, choose `denasty` and sign in to DeNasty in the browser window (OAuth; there is no
token to copy). Free account, no card.

What you get:

- the DeNasty MCP server `https://denasty.me/v1/mcp` (tools `recall`, `remember`, `project_brief`,
  `list_projects`, `my_preferences`, `forget`);
- a skill `denasty-memory` that tells Claude to recall at the start of work and remember after
  decisions, and never to store secrets;
- commands `/denasty:recall [words]` and `/denasty:brief [project]`.

Without the plugin, one line does the server part: `claude mcp add --transport http denasty https://denasty.me/v1/mcp`.

## Other tools

Cursor, VS Code, Codex CLI, Gemini CLI, Cline, Zed, Windsurf, Grok API, Claude.ai and ChatGPT:
one-click buttons at https://denasty.me/connect, configs in [docs/OTHER_CLIENTS.md](docs/OTHER_CLIENTS.md).
Official MCP Registry name: `me.denasty/memory`.

## What you should know

- DeNasty is a centralized service run by Denha Nexus s.r.o. (Slovakia, EU). Memories are stored on
  its servers, readable by the service. It is not end-to-end encrypted and not zero-knowledge.
- Known secret patterns (keys, tokens, passwords) are replaced before storage. Unusual secrets can
  be missed, so do not ask Claude to remember them.
- You see and delete every memory at https://denasty.me/connect. Disconnect Claude Code there or
  with `/mcp` (Clear authentication) at any time, and access stops right away.
- The plugin contains no secret and no code that runs on your machine: a manifest, a skill, two
  prompt files and the server URL.

## Develop

```
claude plugin validate .
claude --plugin-dir .
```

License: MIT.
