# Sources

All claims in `BRIEFING.md` trace to GitHub-hosted primary sources (retrieved live on this evening).

1. **Spec architecture** — https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/specification/2026-07-28/architecture/index.mdx — Host/client/server responsibilities, 1:1 client-server rule, capability negotiation, and design principles.

2. **Server primitives overview** — https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/specification/2026-07-28/server/index.mdx — Defines tools/resources/prompts and the model- vs application- vs user-controlled hierarchy.

3. **Tools protocol** — https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/specification/2026-07-28/server/tools.mdx — `tools/list`, `tools/call`, InputRequiredResult (MRTR), security guidance.

4. **Resources protocol** — https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/specification/2026-07-28/server/resources.mdx — URI-based context, subscriptions, resource templates, security considerations.

5. **Prompts protocol** — https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/specification/2026-07-28/server/prompts.mdx — Slash-command templates, `prompts/get` flow, validation guidance.

6. **Transports overview** — https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/specification/2026-07-28/basic/transports/index.mdx — Two standard bindings (stdio, Streamable HTTP); transport-agnostic, custom transports allowed.

7. **stdio transport** — https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/specification/2026-07-28/basic/transports/stdio.mdx — client-launches-subprocess, newline-delimited JSON-RPC, stderr logging, shutdown/restart rules.

8. **Streamable HTTP transport** — https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/specification/2026-07-28/basic/transports/streamable-http.mdx — Single POST MCP endpoint, SSE reply, `MCP-Protocol-Version`/`Mcp-Method` headers, session removal, deprecations.

9. **MCP servers repo (ecosystem)** — https://github.com/modelcontextprotocol/servers — Reference server inventory, active vs archived, transport table, CLI tooling.

10. **MCP org SDK inventory** — https://github.com/modelcontextprotocol (org page) — 10 official SDKs with collaborators: Python (24.8k★), TypeScript (13.7k★), Java, Kotlin, PHP, Ruby, Rust, Swift, C#, Go; Go incl. Google collaboration, C# incl. Microsoft, Java incl. Spring AI, Kotlin incl. JetBrains, Swift incl. Apple.

11. **MCP Registry** — https://github.com/modelcontextprotocol/registry — community-driven registry service for MCP servers.

12. **Model Context Protocol official docs** — https://modelcontextprotocol.io — Official documentation site; engineering corpus derives from https://github.com/modelcontextprotocol/modelcontextprotocol.

13. **PEP-agnostic ecosystem maturity** — https://github.com/modelcontextprotocol/modelcontextprotocol/percentages/2026-07-28 (this is the current spec revision, see /specification/2026-07-28/) — Latest published spec: JSON-RPC 2.0, per-request capability negotiation, stateless core, no sessions in 2026-07-28.

14. **Version compatibility** — https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/specification/2026-07-28/basic/versioning.mdx — Backward compatibility matrix incl. `server/discover` era binding, per-revision protocol versions.

15. **Conformance tests** — https://github.com/modelcontextprotocol/conformance — Conformance test suite for MCP implementations.
