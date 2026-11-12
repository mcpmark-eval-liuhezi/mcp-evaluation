# SOURCES.md

Every source URL used to prepare this briefing, with a one-line note on what it backed.

## Primary spec & architecture sources

1. **https://github.com/modelcontextprotocol/modelcontextprotocol** — Official MCP specification repo; contains the spec, protocol schema, and official docs. Confirms MCP is an open protocol standardizing LLM-app integration with external data sources and tools; created by David Soria Parra and Justin Spahr-Summers; MIT licensed; contributions via working groups and SEPs. (Definition, authorship, governance.)
2. **https://raw.githubusercontent.com/modelcontextprotocol/modelcontextprotocol/main/docs/specification/2026-07-28/index.mdx** — Current specification index; confirms JSON-RPC 2.0 base protocol, host/client/server roles, the three server features (Resources, Prompts, Tools), client-side Elicitation, additional utilities, optional extensions (Tasks, Skills over MCP, MCP Apps), security principles, and the 2026-07-28 revision status. (Definition, primitives, extensions.)
3. **https://raw.githubusercontent.com/modelcontextprotocol/modelcontextprotocol/main/docs/specification/2026-07-28/architecture/index.mdx** — Architecture page; confirms client-host-server model, statelessness, host responsibilities, client 1:1 server relationship, server responsibilities, design principles, and capability negotiation via `_meta` and `server/discover`. (Architecture.)
4. **https://raw.githubusercontent.com/modelcontextprotocol/modelcontextprotocol/main/docs/specification/2026-07-28/server/index.mdx** — Server overview; confirms the three primitives (Prompts, Resources, Tools) with their control hierarchy (user-controlled / application-controlled / model-controlled). (Primitives.)
5. **https://raw.githubusercontent.com/modelcontextprotocol/modelcontextprotocol/main/docs/specification/2026-07-28/changelog.mdx** — Key changes since 2025-11-25; documents the stateless redesign, removal of sessions/`Mcp-Session-Id`/`initialize` handshake, `server/discover`, `subscriptions/listen`, MRTR pattern, deprecation of Roots/Sampling/Logging and HTTP+SSE, and the SEP-based governance process. (Ecosystem state, spec maturity, transports.)
6. **https://modelcontextprotocol.io** — Official documentation website (Mintlify-built); referenced by the spec repo as the home of official MCP documentation. (Primary docs location.)
7. **https://github.com/modelcontextprotocol/docs** — Archived docs repo that redirects readers to `modelcontextprotocol/modelcontextprotocol` for docs content and PRs. (Docs provenance.)
8. **https://raw.githubusercontent.com/modelcontextprotocol/modelcontextprotocol/main/docs/docs/2026-07-28/sdk.mdx** — SDK guide; confirms official SDK availability for implementations. (Ecosystem/adoption.)

## Adoption & ecosystem evidence

9. **https://github.com/n8n-io/n8n** — Workflow automation platform (210k+ stars) with native MCP client/server support and an `mcp` topic. (Adoption evidence.)
10. **https://github.com/langgenius/dify** — Agentic workflow / RAG platform (160k+ stars) with rich MCP support and an `mcp` topic. (Adoption evidence.)
11. **https://github.com/open-webui/open-webui** — User-friendly AI interface (158k+ stars) supporting Ollama, OpenAI API, and MCP, with an `mcp` topic. (Adoption evidence.)
12. **https://github.com/browser-use/browser-use** — Open-source Python library for AI browser agents (120k+ stars) with MCP integration and an `mcp` topic. (Adoption evidence.)
13. **https://github.com/search?q=topic%3Amcp+stars%3E5000** — GitHub repository search for MCP-topic repos with >5k stars; returned 272 results, confirming broad ecosystem présence. (Ecosystem scale.)

## Quotable figures (computed)

- **Executive summary word count: 118 words** (computed via Python regex tokenization `[A-Za-z0-9][A-Za-z0-9\-']*`; split-based count is 115). This is within the 120-word hard limit.
- **Distinct domains spanned by sources: 3** — `github.com`, `raw.githubusercontent.com`, and `modelcontextprotocol.io` (computed from the source URL list in this file).
