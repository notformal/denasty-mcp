# Installing DeNasty Memory (for AI agents such as Cline)

DeNasty Memory is a remote MCP server. There is nothing to build or run locally.

1. Ask the user to sign in at https://denasty.me, open Connect, create a token and paste it to you. The token starts with
   `dn_pat_`. Never write it into a file that could be committed, and never echo it back.
2. Add this server to the MCP settings:

```json
{
  "mcpServers": {
    "denasty": {
      "type": "streamableHttp",
      "url": "https://denasty.me/v1/mcp",
      "headers": { "Authorization": "Bearer <TOKEN>" }
    }
  }
}
```

3. Verify by calling `list_projects`. An empty list is normal for a new account. A 401 means the token is wrong or revoked.
4. Usage: at the start of work on a project call `recall` (or `project_brief`) with the repository or folder name. After
   the user makes a decision or sets a convention call `remember` with a short, self-contained title and body.
   Never store secrets; the service removes API keys, tokens and passwords it detects, but do not rely on that.
   Use `forget` only when the user asks to delete a memory.
