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
Use ElevenFlo MCP to find the FTX Trading Ltd. Chapter 11 case. Return the top match with case name, court, case number, and case identifier.
```

Codex is a power-user path for recipe repos, skills, plugins, and automation
experiments. For recurring workflows, validate the manual prompt first and see
[Scheduling and notifications](/docs/mcp/workflows/scheduling-and-notifications).

Workflow recipes:

- [Daily case brief](/docs/mcp/workflows/daily-case-brief)
- [Skill files](/docs/mcp/workflows/skill-files)
