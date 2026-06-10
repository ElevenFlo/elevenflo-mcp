# Support

For MCP support, email support@elevenflo.com.

Copy this block, fill it in, and include it in your message:

```text
Organization:
Account email:
MCP client and version:
Endpoint URL:
Case or filing involved:
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
Use ElevenFlo MCP to find the FTX Trading Ltd. Chapter 11 case. Return the top match with case name, court, case number, and case identifier.
```

Expected:

```text
FTX Trading Ltd.
case_id: 1857
case_watch_id: 1857
```

If the smoke test fails, include the failure text in the support request.
