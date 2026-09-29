# Codex CLI

Add the server to `~/.codex/config.toml`:

```toml
[mcp_servers.elevenflo]
url = "https://elevenflo.com/mcp"
```

Then sign in:

```bash
codex mcp login elevenflo
codex mcp get elevenflo
```

If no usable OS keychain is available, set
`mcp_oauth_credentials_store = "file"` at the top level of the config, before
`[mcp_servers.elevenflo]`. Otherwise, keep the client's default credential store.

OAuth refresh depends on your client retaining the refreshed credentials. The
default refresh-token family expires 30 days after authorization; revocation
or a client storage failure can require an earlier sign-in. If access fails,
confirm `codex mcp get elevenflo` reports `https://elevenflo.com/mcp`, then
run `codex mcp login elevenflo` again. See the
[official MCP guide](https://learn.chatgpt.com/docs/extend/mcp?surface=cli) for
current client configuration.
