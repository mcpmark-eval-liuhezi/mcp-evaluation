# MCP Briefing — Internal AI Tooling Evaluation

**Definition.** The Model Context Protocol (MCP) is an open standard from Anthropic (launched November 2024) that standardizes how applications provide context to LLMs — a universal client-server protocol for tool and data access.

**Architecture — host, client, server.** A host (IDE, agent framework, AI assistant) embeds MCP clients that connect to MCP servers. The protocol uses JSON-RPC 2.0 messaging with capability negotiation during initialization ([official docs](https://modelcontextprotocol.io/docs/concepts/architecture)).

**Primitives (what servers expose).**
- **Resources** — file content/data exposed by a server
- **Tools** — executable functions the LLM can invoke
- **Prompts** — reusable prompt templates
- **Sampling** — servers can request LLM completions
- **Elicitation** — servers can ask clients for information
- Plus lifecycle, roots, logging, and notifications ([spec 2025-06-18](https://modelcontextprotocol.io/specification/2025-06-18))

**Transports.**
- **stdio** — servers run locally as subprocesses, JSON-encoded messages over stdin/stdout
- **Streamable HTTP** — remote servers; POST messages, stream responses; the older HTTP+SSE transport predates this split

**Ecosystem state.** OpenAI, Google and Microsoft all support it; it became a Linux Foundation project in December 2025. Reference servers (e.g. `github/github-mcp-server`) prove production readiness for internal tooling.

## Sources

1. [modelcontextprotocol.io](https://modelcontextprotocol.io) — canonical docs and spec landing page
2. [Architecture concept docs](https://modelcontextprotocol.io/docs/concepts/architecture) — host/client/server and JSON-RPC
3. [Specification 2025-06-18](https://modelcontextprotocol.io/specification/2025-06-18) — versioned protocol: primitives, lifecycle, capabilities
4. [Transports spec](https://modelcontextprotocol.io/specification/2025-06-18/basic/transports) — stdio + Streamable HTTP
5. [modelcontextprotocol GitHub org](https://github.com/modelcontextprotocol) — reference servers & SDKs
6. [servers repo](https://github.com/modelcontextprotocol/servers) — reference server implementations
7. [Anthropic announcement](https://www.anthropic.com/news/model-context-protocol) — launch, Nov 2024
8. [Linux Foundation press](https://www.linuxfoundation.org/press) — governance move, Dec 2025
9. [OpenAI announcement](https://openai.com/index/new-tools-for-building-agents/) — OpenAI support
10. [Google Developers Blog](https://developers.googleblog.com/) — Google adoption
11. [Microsoft blog](https://blogs.windows.com/blog-windows-family/) — Microsoft adoption
12. [github/github-mcp-server](https://github.com/github/github-mcp-server) — reference internal-tooling server
