# Registry metadata

The official MCP Registry metadata for ElevenFlo MCP is [server.json](../server.json).

Current registry target:

- name: `com.elevenflo/mcp`
- title: `ElevenFlo MCP`
- transport: `streamable-http`
- remote URL: `https://elevenflo.com/mcp`
- website URL: `https://elevenflo.com/mcp/overview`
- authentication namespace: domain-based authentication for `elevenflo.com`

The MCP Registry metadata is separate from OpenAI/ChatGPT public app review.
OpenAI review requires platform-specific collateral such as screenshots, prompts,
expected responses, demo credentials, and privacy review.

Unauthenticated browser requests to `https://elevenflo.com/mcp` may return `401`.
That path is the protected MCP protocol endpoint, not a browser documentation
page.
