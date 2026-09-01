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

Headless servers and containerized shells have no OS keychain. There, add
`mcp_oauth_credentials_store = "file"` to the same config. On a desktop, keep
the keychain default.

Codex should not ask you to sign in again within 30 days. If it does, confirm
`codex mcp get elevenflo` reports `https://elevenflo.com/mcp`. With file-backed
credentials, an unchanged modified time on `~/.codex/.credentials.json` after a
refresh means the new token was never saved; run `codex mcp login elevenflo`
again.
