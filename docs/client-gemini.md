# Gemini

These setup paths require verification with your Google account. Contact
[support](support.md) to confirm ElevenFlo connection availability before setup.

## Gemini Enterprise

An administrator creates a custom MCP server data store using
`https://elevenflo.com/mcp`. Obtain an ElevenFlo OAuth client ID and secret
before configuring the connection. Use scope `mcp:access` and enable PKCE.
Confirm the required resource parameter and token authentication method with
support; a client secret does not replace PKCE or resource binding.

Follow [Google's administrator setup guide](https://docs.cloud.google.com/gemini/enterprise/docs/connectors/custom-mcp-server/set-up-custom-mcp-server)
for organization policy, permissions, data store creation, and action enablement.
Then follow [Verify the connection](verify.md). An answer alone does not prove
that Gemini called ElevenFlo.

## Gemini app

Google offers custom MCP apps through Gemini Spark. Check
[Google's eligibility and connection instructions](https://support.google.com/gemini/answer/17209137?hl=en-SM)
for your account before continuing.

1. On the Gemini website, open **Settings & help**, then **Connected Apps**.
   If needed, open **Personal Intelligence** first.
2. Under **Custom apps for Spark**, select **Add a custom app**.
3. Enter `https://elevenflo.com/mcp` and follow the sign-in prompts.
4. Select ElevenFlo with `@` in your prompt, then
   [verify a tool call](verify.md).

If account linking fails or credentials are requested, contact
[support](support.md). Use Connected Apps settings to disconnect or remove
ElevenFlo when needed.
