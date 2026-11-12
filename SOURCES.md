# Sources

Every source URL used in BRIEFING.md, with a one-line note on what it backed.

1. https://github.com/modelcontextprotocol/modelcontextprotocol
   - Primary spec repository README: confirms MCP is the specification and documentation repo, created by David Soria Parra and Justin Spahr-Summers, MIT License, contributions via working groups and SEPs, and that the schema is defined in TypeScript and published as JSON Schema for wider compatibility.

2. https://modelcontextprotocol.io
   - Official documentation site referenced by the spec repo as the home of the MCP specification, protocol schema, and official docs built with Mintlify.

3. https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/specification/2026-07-28/architecture/index.mdx
   - architecture(): the canonical description of the client-host-server architecture, host/client/server responsibilities, stateless protocol semantics, design principles, and per-request capability negotiation (clientCapabilities, server/discover).

4. https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/specification/2026-07-28/basic/transports/index.mdx
   - transports(): defines stdio and Streamable HTTP as the two standard bindings, the transport-as-binding distinction, JSON-RPC UTF-8 messaging, custom transport guidance, and backward compatibility with earlier initialize-based revisions.

5. https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/specification/2026-07-28/server/
   - server/ directory: enumerates the core primitives (tools.mdx, resources.mdx, prompts.mdx) and utilities (sampling, elicitation, roots) that servers expose, plus server/discover for up-front capability discovery.

6. https://github.com/modelcontextprotocol/servers
   - Official reference servers repository: lists the maintained reference servers (Everything, Fetch, Filesystem, Git, Memory, Sequential Thinking, Time), the ten official language SDKs, the MCP Inspector debugging tool, and the MCP Registry pointer; notes the repo is managed by Anthropic with community contributions.

7. https://github.com/openai/codex
   - OpenAI adoption evidence: the Codex coding agent repo contains MCP conformance scripts (scripts/mcp_conformance/README.md) and MCP integration in its TypeScript SDK, demonstrating first-party MCP support.

8. https://github.com/openai/openai-agents-python
   - OpenAI adoption evidence: docs/mcp.md documents MCP usage, and examples/mcp/git_example/README.md shows a concrete MCP server example, confirming MCP is embedded in OpenAI's agent frameworks.

9. https://registry.modelcontextprotocol.io/
   - MCP Registry: the published servers browsing registry referenced by the servers repo, evidence of a growing ecosystem of pre-built, discoverable MCP servers.

## Domain Count (computed)

Distinct source domains across the 9 URLs above: **3** (github.com, modelcontextprotocol.io, registry.modelcontextprotocol.io).
