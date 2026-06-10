# Claude Desktop

Open **Customize → Connectors → Add custom connector** and paste:

```text
https://elevenflo.com/mcp
```

Use `elevenflo` when Claude asks for a server label. Claude Desktop opens the ElevenFlo OAuth sign-in and consent flow on the next screen.

Smoke test:

```text
Use ElevenFlo MCP to find the FTX Trading Ltd. Chapter 11 case. Return the top match with case name, court, case number, and case identifier.
```

> [!NOTE]
> Anthropic's current remote-MCP custom connector path is documented on Free,
> Pro, Max, Team, and Enterprise plans. Free users can add one custom
> connector.

> [!TIP]
> On Team and Enterprise, owners can add a custom connector centrally in
> `Admin Settings → Connectors`, and members can then connect it from
> `Customize → Connectors`.

For Claude skills and Cowork scheduling posture, see
[Skill files](/docs/mcp/workflows/skill-files) and
[Scheduling and notifications](/docs/mcp/workflows/scheduling-and-notifications).

> _Reviewed 2026-06-04 against current Claude connector, skill, and Cowork docs. Anthropic occasionally renames these menus; if a path differs, the destination is the same — add a custom connector by URL. Verify ElevenFlo access in your exact Claude surface before relying on recurring workflows._
