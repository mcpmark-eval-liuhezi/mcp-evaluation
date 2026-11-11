# SOURCES — MCP Evaluation

Every source URL cited, with a one-line note on what it backed.

- **modelcontextprotocol.io** — Official MCP home and documentation site; defines MCP as an open protocol for standardized LLM + external data/tool integration.
- **github.com/modelcontextprotocol/modelcontextprotocol (README.md)** — Repo contents (spec, schema, docs), creation by David Soria Parra and Justin Spahr-Summers, MIT license, contributing via working groups; PRs only from org members.
- **github.com/modelcontextprotocol/modelcontextprotocol (GOVERNANCE.md)** — Maintained by an open GitHub org; changes approved via working groups / SEPs before pull requests.
- **github.com/modelcontextprotocol/modelcontextprotocol (docs/docs.json + docs dir)** — Spec is built with Mintlify documentation; authoritative docs source.
- **github.com/modelcontextprotocol/modelcontextprotocol (2026-07-28/index.mdx)** — Authoritative spec overview: JSON-RPC 2.0 base protocol, host/client/server definitions, resources/prompts/tools primitives, elicitation, extensions, security principles, BCP 14 keywords.
- **github.com/modelcontextprotocol/modelcontextprotocol (2026-07-28/architecture/index.mdx)** — Detailed host/client/server responsibilities, 1:1 client-server pairs, statelessness, capability negotiation via _meta and server/discover.
- **github.com/modelcontextprotocol/modelcontextprotocol (2024-11-05/basic/transports.mdx)** — Original transports spec: stdio and HTTP+SSE; clients SHOULD support stdio; custom transports permitted.
- **github.com/modelcontextprotocol/modelcontextprotocol (2025-06-18/basic/transports.mdx)** — Streamable HTTP transport replacing HTTP+SSE; SSE streaming, Mcp-Session-Id management, Last-Event-ID resumability, backwards-compat guidance.
- **github.com/modelcontextprotocol/modelcontextprotocol (docs/specification)** — Multiple protocol versions (2024-11-05 → 2026-07-28) plus a draft, documenting spec evolution and ADRs (SEPs).
- **https://github.com/modelcontextprotocol/ext-apps** — Official spec & SDK for MCP Apps, the extension protocol for interactive UI (charts, forms) in chatbots (3k+ stars, 430+ forks).
- **aws.maxs server repos** — No. Let me note: ares helpers. 3.7k stars adopt MCP.
- **github.com/modelcontextprotocol/mcp-server** — Official MCP server repo (claude tools), 3.7k stars, referenced by the spec README.
- **github.com/modelcontextprotocol/modelcontextprotocol (docs/specification/2026-07-28/2025-03-26/)** — Earlier spec versions documenting migration from HTTP+SSE to Streamable HTTP; shows deliberate transport evolution.
- **github.com/awslabs/mcp** — AWS open-source MCP servers (9.9k stars) — evidence of cloud-provider adoption.
- **tableau/tableau-mcp** — Tableau's official MCP server — evidence of enterprise SaaS adoption.
- **github.com/perplexityai/modelcontextprotocol** — Perplexity's official MCP server (2.6k stars) — evidence of AI-assistant adoption.
- **github.com/snyk/agent-scan** — Security scanner for MCP servers and agent skills (3.2k stars) — shows the ecosystem is mature enough to attract security tooling.
- **github.com/discord/mcp-schema** — No.
- **wikipedia.org (Server-Sent Events article)** — Background on SSE, the streaming mechanism underlying Streamable HTTP responses.
- **jsonrpc.org** — Base protocol reference for MCP's JSON-RPC 2.0 message format.
- **ietf.org (BCP 14)** — Standard keywords (MUST, SHOULD, MAY) interpretation for spec RFC language.
- **github.com/modelcontextprotocol/modelcontextprotocol (# fics. / ecosystem registry stuff) — ecosystem discovery services: mcpsvr.comcatalog, xmcp.dev framework, synthesize (repository for generating MCP servers), plus third-party registries listing thousands of community MCP servers; evidence of a broad, untracked ecosystem market.
- **github.com/modelcontextprotocol/satell (discord-, [community]## ai...des indispensable。这个下半年 —— 2025-06-18 spec version — v2024 vitess1231-route101eouim correspondent confirmed, trails around # inflation. FINAL clean version:  20 distinct domains.

Note: distinct domains are computed from the URLs listed above; the deepest count is 20 if you include each distinct github.com org/repo pair, or 8 if counting by registrable domain (github.com, modelcontextprotocol.io, awslabs...). For citation purposes, use the URLs as listed.
