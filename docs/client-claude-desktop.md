# Claude Desktop

[Open ElevenFlo in Claude](https://claude.ai/directory/connectors/elevenflo),
choose **Connect**, and sign in to ElevenFlo. This works in Claude on the web
and in the desktop app.

On Team and Enterprise plans, an owner adds ElevenFlo once under
**Admin Settings → Connectors**. Members then connect their own accounts.

If the listing isn't available in your account,
[add ElevenFlo as a custom connector](https://claude.ai/customize/connectors?modal=add-custom-connector&connectorName=ElevenFlo&connectorUrl=https%3A%2F%2Felevenflo.com%2Fmcp).

## Enterprise managed auth

An ElevenFlo team owner or admin can enable **Claude-issued** managed
authorization for existing team members. Members must have verified ElevenFlo
emails matching their Claude emails. Their research access, personal monitors
and individual credit allowances stay the same. ElevenFlo does not create users
or add team members from assertions.

This requires Claude Team or Enterprise with the Claude-issued identity source
available. Claude currently does not offer that source to organizations using
Okta sign-in. IdP-managed assertions are not supported by ElevenFlo.

1. In Claude, add a custom connector at `https://elevenflo.com/mcp`.
   Use the custom connector for initial testing; the directory connector shows
   managed authorization only after Anthropic enables that designation.
   Claude permits one connector per URL in an organization.
2. Open the connector's **Configuration → Authentication → Managed
   authorization → Set up** and choose **Claude-issued**.
3. Copy Claude's **Issuer URL**. In ElevenFlo, open
   [Account settings → Team](https://elevenflo.com/account-info/team), find
   **Claude managed authorization**, enter that exact URL, turn on
   **Enable for this team**, and save. The administrator's ElevenFlo email must
   also be verified.
4. Copy the **Tenant ID** shown in ElevenFlo into Claude's **Tenant ID** field.
   This is the ElevenFlo team ID, not a Claude organization ID.
5. In Claude, select **Run test**. Then ask ElevenFlo for a case's recent
   documents to verify the connector.

Only the issuer registered for that team is trusted. Changing the issuer or
disabling the setting revokes that team's managed connections; removing an
ElevenFlo team member also revokes their managed connections. These changes
reach MCP workers within 30 seconds. Removing someone only from Claude stops
new assertions; an already-issued ElevenFlo access token can remain valid
until it expires (at most one hour). Claude renews it automatically using a new
identity assertion, without asking the member to sign in again.

Claude-issued identity establishes Claude organization membership. It does not
establish corporate SSO, MFA, or group membership. Regular interactive OAuth
remains available.

If **Run test** fails, check the exact issuer URL and tenant ID, verify that the
user is an active member of this ElevenFlo team with a verified matching email,
and confirm the team's subscription is active. Dots and plus tags in email
addresses are significant. A shared issuer requires the tenant ID to
disambiguate teams.
