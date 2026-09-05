# Microsoft 365 Copilot

The ElevenFlo package is a draft for administrator setup. It is not a published
Microsoft listing, and its sign-in flow has not been verified in a customer
tenant. Contact [support](support.md) to confirm availability before setup.

An administrator or developer can prepare a declarative agent with an MCP
plugin for `https://elevenflo.com/mcp`. Follow
[Microsoft's plugin authentication guide](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/plugin-authentication)
in Microsoft 365 Agents Toolkit. Use scope `mcp:access`.

Dynamic registration requires a client secret in Microsoft's flow. Confirm
ElevenFlo's deployed support with your administrator before provisioning.
Static registration also requires a compatible OAuth client; it does not
bypass the sign-in requirements. See
[Microsoft's dynamic registration requirements](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/plugin-authentication-dynamic-client-registration).

After your administrator validates and installs the configured package:

1. Open the assigned agent in Microsoft 365 Copilot.
2. Request a case lookup and follow the ElevenFlo sign-in prompts.
3. Approve the `mcp:access` scope with your own ElevenFlo account.
4. Follow [Verify the connection](verify.md) and check the cited filing.

Available tools come from authenticated discovery. Your administrator must
verify which tools Microsoft enables for the agent.
