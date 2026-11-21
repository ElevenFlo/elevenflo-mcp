# ChatGPT

Start with the published listing:
[Open ElevenFlo in ChatGPT](https://chatgpt.com/apps/elevenflo/asdk_app_6a27946962bc819180664633b81cc507).

For a private or organization-managed connection, use **Apps & Connectors** in
developer mode on ChatGPT web, with Apps enabled for the organization. Create an
app named `ElevenFlo MCP` pointing at `https://elevenflo.com/mcp`, keep OAuth
enabled, and complete sign-in in the browser.

In a new chat, [try a question and open the cited filing](https://elevenflo.com/docs/mcp/setup#verify-the-connection).
ChatGPT modes and background-task environments have separate availability
and tool-access rules; verify the ElevenFlo connection in the environment
where the work will run.

For recurring research with a ChatGPT Dot, follow the
[Dot case-monitoring recipe](https://github.com/ElevenFlo/elevenflo-mcp/blob/main/docs/workflow-automation.md#use-a-chatgpt-dot).
Dots can use supported plugins already enabled for your account, subject to
their existing permissions. Verify a manual Dot run before saving a schedule.
