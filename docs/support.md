# Support

For beta support, contact your ElevenFlo representative.

Copy this block, fill it in, and paste it into your support message:

```text
Organization:
Account email:
MCP client and version:
Endpoint URL:
Tool or prompt attempted:
Approximate time:
Error message:
Affects one user or multiple users:
```

## Severity

### Access blocked

Use when no authorized user in your organization can connect.

### Research blocked

Use when the client connects but core read tools fail.

### Incorrect or missing data

Use when a case, filing, summary, or source result appears wrong or incomplete.

### Client setup help

Use when a supported client cannot complete OAuth or cannot find the `elevenflo` server.

## Before contacting support

Run the smoke test:

```text
Use ElevenFlo MCP to find the Chapter 11 case for FTX and return the top match with the case name and case_watch_id.
```

Expected:

```text
FTX Trading Ltd.
case_watch_id: 1857
```

If the smoke test fails, include the failure text in the support request.
