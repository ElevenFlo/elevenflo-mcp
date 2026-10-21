# Automation

A recurring research task needs an explicit review window, a working
connection and a way to check the result. ElevenFlo MCP supplies research
tools. Your client controls the schedule, saved files and notifications.

## Start with a manual run

1. Connect ElevenFlo using the [setup instructions](https://elevenflo.com/docs/mcp/setup).
2. Run the [daily case brief](https://elevenflo.com/docs/mcp/workflows/daily-case-brief)
   with a named case and explicit start and end times.
3. Open the cited filings and check the material claims.
4. Confirm that the client can use the same ElevenFlo connection in its
   scheduled-task environment.
5. Test the scheduled run, including its review window and failure reporting.

Availability depends on the client, account and organization settings. A
working manual connection does not establish that a scheduled task can use it.
Check the client's current documentation before configuring a schedule.

## Use a ChatGPT Dot

A [ChatGPT Dot](https://learn.chatgpt.com/docs/dots) can own a recurring case
watch and work while your computer is off. Availability depends on your
ChatGPT plan, region and workspace settings. It can use supported plugins
already enabled for your account, within their existing permissions.

Connect ElevenFlo using the [ChatGPT setup](https://elevenflo.com/docs/mcp/setup#chatgpt),
then ask your Dot to run the daily case brief manually. Have it identify the
tools it actually used and open its filing citations. If ElevenFlo is not
available to the Dot, resolve that connection before scheduling; a successful
ordinary ChatGPT conversation is not proof of Dot access.

After verifying that run, adapt this prompt:

```text
Monitor [CASE NAME, COURT, CASE NUMBER AND ELEVENFLO CASE LINK] using the
existing ElevenFlo connection. Run each weekday at [TIME AND TIMEZONE]
until [END DATE]. Return results in this Dot conversation.

Use the maintained daily case brief workflow:
https://elevenflo.com/docs/mcp/workflows/daily-case-brief
For the first run, review [EXPLICIT START] through the actual run time.
For later runs, start at the last successfully reviewed window end.
State the exact window, run time and case identity in every brief.

Page through the docket entries in the window and read material filings.
Keep filing dates separate from detected-update dates. Cite the filings
for amounts, dates, deadlines and operative terms. Include missing text,
pagination limits and source gaps. Do not treat an empty or failed tool
response as evidence that nothing changed.

Advance the saved window only after successfully reviewing its scope.
If access, pagination or required text fails, report the unreviewed window
and retain the checkpoint so the next run can recover it.
If you cannot persist a checkpoint, say so before scheduling.

Draft only: do not send messages, publish, or change account settings.
Use included ChatGPT usage and existing ElevenFlo credits only; do not
purchase credits or upgrades, or use paid APIs or paid external sources.
Confirm the saved schedule, timezone, end date and output destination.
```

Check the saved task in your Dot's Scheduled view and inspect its first
scheduled result. Review the case list and schedule when your priorities
change. Start with one case before expanding to a portfolio.

Dot conversations do not count toward ordinary ChatGPT usage limits.
Deeper work has an included allowance; tasks delegated to Work or Codex
use those products' normal limits. Work and Codex share usage. These are
included allowances, not unlimited capacity. See OpenAI's
[Dot access guidance](https://learn.chatgpt.com/docs/dots#access) and
[usage and pricing](https://learn.chatgpt.com/docs/pricing).
ElevenFlo tool calls still use your ElevenFlo account's access and MCP
credits; a ChatGPT subscription does not replace them.

## Define each run

Give the task a case or case list, timezone, review window and output
destination. Save the last successful window if the client supports persistent
state. If it does not, supply the window explicitly.

A failed run should report the problem. It should not present an empty brief
as evidence that nothing changed.

## Use email when your host cannot run the connection unattended

Test the actual scheduled environment, not just an ordinary conversation.
For a ChatGPT scheduled task or a Claude routine, confirm that it can call
ElevenFlo through your customer connection more than an hour after sign-in.
Check the returned citations, saved checkpoint and next run. A manual call or
a separate script using the same account does not establish that host's access.

If the scheduled environment cannot use the connection, use ElevenFlo's
existing email delivery:

1. Ask your connected client to save a monitor with the case or market filters
   you want, then list your monitors to verify the saved scope.
2. Open [Account](https://elevenflo.com/account-info), find **Tracked case
   updates** under email preferences, and choose an available delivery cadence.
   Delivery requires a verified email address and the account access needed
   for the monitor. The control shows the available choices.
3. Check the first delivered update and its filing citations. Monitor email
   covers matching published changes; it does not run the broader research
   prompt or certify that the court docket is complete.

Use this instruction in either host when its unattended connection test fails:

```text
Keep the saved ElevenFlo monitor and use ElevenFlo email delivery for alerts.
Record that this host's unattended ElevenFlo connection is unverified or
unavailable, with the test time and actual error. Do not describe the schedule
as working. Give me the monitor's saved scope and the Account email-preference
link so I can choose delivery. Continue deeper research manually when asked.
```

Saving a monitor does not turn email delivery on. Choose delivery yourself;
the MCP tools do not change email preferences or send a test message.

## Notifications and drafts

Request a notification or draft through a client feature that supports it.
ElevenFlo MCP does not send messages.

```text
Prepare the case brief for the stated window.
Save the brief using the configured client destination.
If the research fails, report the error and the unreviewed window.
Draft a short summary for my review. Do not send it.
```

## Reuse the prompt

Save a working prompt in the client's supported instructions or skill format.
Keep the case identity, explicit scope, source citations and uncertainty rules.
Link to the maintained workflow rather than copying a second set of setup
instructions.

Use the [verification checklist](https://elevenflo.com/docs/mcp/workflows/safety-and-verification#acceptance-checklist)
when changing the prompt, connection or schedule.
