# Scheduling and notifications

Use this page to decide whether to move a workflow beyond manual prompts to a recurring run.

Start manually. Run the prompt once with an explicit date range, confirm the
output cites filings or exact text, and only then consider a recurring run.

ElevenFlo recipes lead with reviewable runs, not unattended automation.
Scheduling, notifications, email drafts, Slack posts, file writes, and workspace
storage belong to the host AI client or another connected app, not to ElevenFlo
MCP.

## Verification status labels

| Status | Meaning |
| --- | --- |
| Verified | ElevenFlo has tested the exact host surface with the published ElevenFlo MCP app or server. |
| Available but unverified | Host documentation indicates the capability exists, but ElevenFlo has not verified it with the published ElevenFlo MCP app or server. |
| Beta / admin-dependent | Availability depends on plan, workspace, region, admin settings, or rollout state. |
| Not currently supported | Host docs do not support this path or explicitly exclude it. |

## Host capability matrix

| Surface | What to know |
| --- | --- |
| [ChatGPT app / app directory](https://chatgpt.com/apps/elevenflo/asdk_app_6a27946962bc819180664633b81cc507) | Supported for manual use where ElevenFlo is available and connected. |
| ChatGPT Developer Mode / custom MCP | Beta / admin-dependent. Use for testing and private or workspace deployment when the public app directory is not the right path. Availability depends on the current ChatGPT plan, workspace, admin settings, and rollout state; verify in OpenAI's [Developer mode and MCP apps](https://help.openai.com/en/articles/12584461-developer-mode-and-mcp-apps-in-chatgpt). |
| ChatGPT Workspace Agents | Beta / admin-dependent. Best ChatGPT path for scheduled workspace workflows where available and admin-enabled. Verify ElevenFlo access in the exact workspace before publishing step-by-step instructions. See OpenAI's [Workspace Agents guide](https://help.openai.com/en/articles/20001143-chatgpt-workspace-agents-for-enterprise-and-business). |
| ChatGPT Tasks | Available but unverified for ElevenFlo MCP. Tasks can schedule prompts and send push or email notifications, but ElevenFlo has not verified that Tasks can invoke the published ElevenFlo MCP surface. Use manual prompts or Workspace Agents where available until verified. See OpenAI's [Tasks guide](https://help.openai.com/en/articles/10291617-tasks-in-chatgpt). |
| ChatGPT Agent | Not currently the custom-MCP automation path. OpenAI's current Developer Mode FAQ says Agent mode will not use custom apps. |
| Deep Research / company knowledge | Potential read/fetch path where available. Verify with ElevenFlo `search` and `fetch`; do not use it for write or modify claims. |
| Codex | Power-user path for recipe repos, skills, plugins, and automation experiments. Project-scoped automations require the local Codex app, machine, and project to be available. See OpenAI's [Codex automations](https://developers.openai.com/codex/app/automations). |
| Claude + custom connector | Strong individual and team path once the ElevenFlo connector is available in the relevant Claude surface. |
| Claude Cowork scheduled tasks | Available but unverified for ElevenFlo connector behavior. Cowork scheduled tasks can use connected tools, skills, and installed plugins, but currently depend on Claude Desktop/Cowork availability. See Anthropic's [Cowork scheduled tasks guide](https://support.claude.com/en/articles/13854387-schedule-recurring-tasks-in-claude-cowork). |
| Claude web / Cowork skills | Use `skill.md` inside a ZIP folder for claude.ai custom skills. Verify packaging against Anthropic's current help docs before publishing downloadable packs. |
| Claude Code skills | Use `SKILL.md` for Claude Code filesystem skills. |
| Cursor, VS Code, and generic MCP clients | Setup and workflow prompts only. Do not rely on scheduling unless the specific client supports it and has been tested with ElevenFlo. |

## For users

Before relying on a recurring run, confirm:

- The run can access ElevenFlo tools in the scheduled context.
- The output states the run date/time, timezone, review window, case identifier,
  and source basis.
- The run does not depend on model memory for the previous run time.
- Notifications or email language says "notify me when complete" or "draft an
  email for review", not "ElevenFlo sends an email".
- The manual version of the prompt produced a source-backed output that you can
  review against filings or exact text.

## Email and Slack wording

Good wording:

```text
Prepare a daily brief and have your host client notify you when the run completes.
```

```text
Draft an email summary for review.
```

Avoid:

```text
Send me an email every morning.
```

```text
ElevenFlo will email the brief.
```

For Claude Google Workspace, Anthropic's current help docs say Claude can create
Gmail drafts, but emails must be sent manually through Gmail.

## Manual-first setup

For any recurring workflow:

1. Run the recipe manually with an explicit date range.
2. Confirm the output cites filings or exact text.
3. Confirm the client can access ElevenFlo in the surface that will run later.
4. Confirm OAuth refresh or reauthorization behavior.
5. Only then create a recurring host task, workspace agent schedule, or Codex
   automation.
