# Codex CLI

Add the remote MCP server to your Codex config (`~/.codex/config.toml`):

```toml
[mcp_servers.elevenflo]
url = "https://elevenflo.com/mcp"
```

If your environment cannot use the OS keychain to persist OAuth tokens (for example, headless servers or a containerized shell), opt into file-backed credentials:

```toml
mcp_oauth_credentials_store = "file"
```

Skip this if Codex is running interactively on a desktop — keychain storage is the default and preferred path.

Sign in:

```bash
codex mcp login elevenflo
```

Then run:

```text
Use the elevenflo MCP tool search_cases to find the Chapter 11 case FTX and return the top match.
```
