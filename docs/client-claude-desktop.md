# Claude Desktop

Open **Customize -> Connectors -> Add custom connector** and paste:

```text
https://elevenflo.com/mcp
```

Use `elevenflo` when Claude asks for a server label. Claude Desktop opens the ElevenFlo OAuth sign-in and consent flow on the next screen.

Smoke test:

```text
Use the elevenflo MCP tool search_cases to find the Chapter 11 case FTX and return the top match.
```

> [!NOTE]
> Anthropic's current remote-MCP custom connector path is documented on Free,
> Pro, Max, Team, and Enterprise plans. Free users can add one custom
> connector.

> [!TIP]
> On Team and Enterprise, owners can add a custom connector centrally in
> `Admin Settings -> Connectors`, and members can then connect it from
> `Customize -> Connectors`.

> _Last verified 2026-04-29 against the current Claude Desktop UI. Anthropic occasionally renames these menus; if the path differs, the destination is the same — adding a custom connector by URL._
