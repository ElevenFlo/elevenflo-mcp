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

## Define each run

Give the task a case or case list, timezone, review window and output
destination. Save the last successful window if the client supports persistent
state. If it does not, supply the window explicitly.

A failed run should report the problem. It should not present an empty brief
as evidence that nothing changed.

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
