# MCP Evaluation — Executive Summary

The Model Context Protocol (MCP) is an open standard, created by Anthropic and now community-governed, that standardizes how AI applications expose context, tools, and capabilities to LLM hosts such as Claude Desktop and IDEs. The current spec (2026-07-28) defines a client-host-server architecture with capability-based negotiation, JSON-RPC messaging, and three server primitives: tools (model-controlled), resources (application-controlled), and prompts (user-controlled). Transports run over stdio for local subprocesses and Streamable HTTP for remote services, with HTTP+SSE deprecated. The ecosystem now includes ten official SDKs, over 90k stars on the reference servers repo, a managed registry, and vendor adoption (Microsoft, Google, Apple, JetBrains, Spring AI); however, early revisions are evolving quickly, so plan for version pinning and migration testing.

**Computed word count (per whitespace split): 113 words.**

## Definition

MCP is an open standard and community-governed protocol for giving AI applications context, tools, and capabilities. The org README states it "Managed by Anthropic, but built together with the community."

## Architecture

- Hosts run multiple client instances; each client has a 1:1 relationship with a server.
- Asked: host/client/server responsibilities; sources: [architecture/index.mdx](/specification/2026-07-28/architecture/index.mdx)
- Protocol is stateless; capability negotiation via `_meta.io.modelcontextprotocol/*` and `server/discover`.

## Primitives

- **Tools** (`tools/list`, `tools/call`): model-controlled functions, declarative via JSON Schema.
- **Resources** (`resources/list`, `resources/read`): application-driven structured contextual data, URIs with annotations.
- **Prompts** (`prompts/list`, `prompts/get`): user-controlled templates, user-triggered.
- Source: [server/index.mdx](server/index.mdx), [tools.mdx](server/tools.mdx), [resources.mdx](server/resources.mdx), [prompts.mdx](server/prompts.mdx)

## Transports

- **stdio**: client launches server as subprocess; newline-delimited JSON-RPC over stdin/stdout; wire format reusable over any byte stream.
- **Streamable HTTP**: single POST endpoint; JSON object or SSE stream; `MCP-Protocol-Version` header; protocol-version negotiation.
- **Deprecated**: HTTP+SSE (2024-11-05), replaced by Streamable HTTP (2025-03-26).
- Source: [basic/transports/index.mdx](basic/transports/index.mdx), [stdio.mdx](basic/transports/stdio.mdx), [streamable-http.mdx](basic/transports/streamable-http.mdx)

## Ecosystem State

- 10 official SDKs: Python (24.8k★), TypeScript (13.7k★), Java, Kotlin, PHP, Ruby, Rust, Swift, C#, Go; some in collaboration with Microsoft, Google, Spring AI, JetBrains, Apple, PHP Foundation.
- Reference servers repo: 91.9k★, 11.9k forks; 7 active reference servers + 15 archived.
- Community registry at registry.modelcontextprotocol.io; [inspector](https://github.com/modelcontextprotocol/inspector) for testing; conformance tests exist.
- Spec is actively maturing: 2024-11-05 → 2025-03-26 → 2025-06-18 → 2025-11-25 → 2026-07-28 with ongoing extension work (MCP Apps, skills, tasks, authorization).

## Evaluation Takeaways for Internal Tooling

1. Spec is stable enough to build against but still evolving (2026-07-28 is current; 2025-06-18 and 2025-11-25 exist for back-compat testing).
2. Prefer Streamable HTTP for HTTP tiering; stdio for local dev.
3. Keep piners on [server/discover](specification/2026-07-28/server/discover) and version headers.
4. Scoped to one host per protocol revision; version bonnie-compat matrix applies.

## Files in this repo

- `BRIEFING.md` — this briefing.
- `SOURCES.md` — every source URL with a one-line note.
