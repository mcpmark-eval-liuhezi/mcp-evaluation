# MCP Evaluation Briefing

## Executive Summary

MCP is an open protocol, now maintained by the modelcontextprotocol GitHub organization, that standardizes how LLM applications connect to external data sources and tools. It uses a client-host-server architecture: hosts create client instances; each client has a 1:1 relationship with a server; and servers expose three core primitives — Resources (context/data), Prompts (templates), and Tools (executable functions) — plus client-side Elicitation. Servers run over JSON-RPC 2.0 via two transports: stdio for local processes and Streamable HTTP for remote services (HTTP+SSE is deprecated). The current revision, 2026-07-28, made the protocol stateless and added server/discover and MRTR patterns. Adoption is broad, with major IDEs, chat tools, and agent frameworks such as n8n, Dify, and open-webui supporting it, and the spec keeps evolving through SEP proposals.

*Executive summary word count (computed, regex tokenization): 118 words — within the 120-word limit.*

## Definition

The Model Context Protocol (MCP) is an open protocol that enables seamless integration between LLM applications and external data sources and tools. Whether building an AI-powered IDE, enhancing a chat interface, or creating custom AI workflows, MCP provides a standardized way to connect LLMs with the context they need. It takes inspiration from the Language Server Protocol (LSP), which standardized language support across dev tools; MCP standardizes integrating additional context and tools into the ecosystem of AI applications. ([spec index](https://raw.githubusercontent.com/modelcontextprotocol/modelcontextprotocol/main/docs/specification/2026-07-28/index.mdx), [repo README](https://github.com/modelcontextprotocol/modelcontextprotocol))

## Architecture: Host / Client / Server

MCP follows a **client-host-server architecture** where each host can run multiple client instances. The model protocol is stateless: every request is self-contained and carries its own protocol version and capabilities.

- **Host**: LLM applications that initiate connections. It creates and manages multiple client instances, controls client connection permissions and lifecycle, enforces security policies and consent requirements, handles user authorization decisions, coordinates AI/LLM integration and sampling, and manages context aggregation across clients. ([architecture](https://raw.githubusercontent.com/modelcontextprotocol/modelcontextprotocol/main/docs/specification/2026-07-28/architecture/index.mdx))
- **Clients**: Connectors within the host application. Each client is created by the host and communicates with **exactly one server** (1:1 relationship). Clients attach protocol version and capabilities to every request, route protocol messages bidirectionally, manage subscriptions and notifications, and maintain security boundaries between servers. ([architecture](https://raw.githubusercontent.com/modelcontextprotocol/modelcontextprotocol/main/docs/specification/2026-07-28/architecture/index.mdx))
- **Servers**: Services that provide context and capabilities. They expose resources, tools, and prompts via MCP primitives, operate independently with focused responsibilities, can request client input (sampling, elicitation, roots) via `InputRequiredResult` within a reply, must respect security constraints, and can be local processes or remote services. ([architecture](https://raw.githubusercontent.com/modelcontextprotocol/modelcontextprotocol/main/docs/specification/2026-07-28/architecture/index.mdx))

**Design principles**: (1) servers should be extremely easy to build — host apps handle orchestration; (2) servers should be highly composable — multiple servers combine seamlessly; (3) servers should not be able to read the whole conversation or "see into" other servers — full history stays with the host and cross-server interactions are controlled by the host; (4) features can be added progressively with backwards compatibility maintained. ([architecture](https://raw.githubusercontent.com/modelcontextprotocol/modelcontextprotocol/main/docs/specification/2026-07-28/architecture/index.mdx))

**Capability negotiation**: clients declare capabilities in `_meta.io.modelcontextprotocol/clientCapabilities` on every request; servers advertise capabilities via `server/discover`, which clients may call before any other request for up-front discovery. ([architecture](https://raw.githubusercontent.com/modelcontextprotocol/modelcontextprotocol/main/docs/specification/2026-07-28/architecture/index.mdx))

## Server Primitives

Servers offer any of the following features to clients ([spec index](https://raw.githubusercontent.com/modelcontextprotocol/modelcontextprotocol/main/docs/specification/2026-07-28/index.mdx)):

| Primitive | Control | Description | Example |
| --- | --- | --- | --- |
| **Prompts** | User-controlled | Interactive templates invoked by user choice | Slash commands, menu options ([server/prompts](https://raw.githubusercontent.com/modelcontextprotocol/modelcontextprotocol/main/docs/specification/2026-07-28/server/prompts.mdx)) |
| **Resources** | Application-controlled | Contextual data attached and managed by the client | File contents, git history ([server/resources](https://raw.githubusercontent.com/modelcontextprotocol/modelcontextprotocol/main/docs/specification/2026-07-28/server/resources.mdx)) |
| **Tools** | Model-controlled | Functions exposed to the LLM to take actions | API POST requests, file writing ([server/tools](https://raw.githubusercontent.com/modelcontextprotocol/modelcontextprotocol/main/docs/specification/2026-07-28/server/tools.mdx)) |

Clients may offer **Elicitation**: server-initiated requests for additional information from users. Additional utilities include configuration, progress tracking, cancellation, and error reporting. Beyond the core protocol, MCP defines optional **extensions** (opt-in, negotiated during initialization): **Tasks** (async long-running operations with polling and durable handles), **Skills over MCP** (rich structured instructions for agent workflows), and **MCP Apps** (interactive UI elements rendered inline in conversations). ([spec index](https://raw.githubusercontent.com/modelcontextprotocol/modelcontextprotocol/main/docs/specification/2026-07-28/index.mdx), [server overview](https://raw.githubusercontent.com/modelcontextprotocol/modelcontextprotocol/main/docs/specification/2026-07-28/server/index.mdx))

## Transports

Servers run over **JSON-RPC 2.0** messages. The spec defines two�Viable current transports ([basic/transports](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/specification/2026-07-28/basic/transports)):

- **stdio**: for local, singleton processes — simple, low-overhead, ideal for local tools and IDEs.
- **Streamable HTTP**: for remote services. This replaced the deprecated HTTP+SSE transport (deprecated since protocol version `2025-03-26`). It uses a single long-lived POST-response stream for opted-in server-to-client change notifications via `subscriptions/listen`, with standard MCP request headers (`Mcp-Method`, `Mcp-Name`) required on POST requests. ([changelog](https://raw.githubusercontent.com/modelcontextprotocol/modelcontextprotocol/main/docs/specification/2026-07-28/changelog.mdx))

The `2026-07-28` revision removed protocol-level sessions and the `Mcp-Session-Id` header from Streamable HTTP; list endpoints (`tools/list`, `resources/list`, `prompts/list`) no longer vary per-connection. Servers needing cross-call state use explicit, server-minted handles passed as ordinary tool arguments. ([changelog](https://raw.githubusercontent.com/modelcontextprotocol/modelcontextprotocol/main/docs/specification/2026-07-28/changelog.mdx))

## Ecosystem State: Spec Maturity & Adoption

**Spec maturity**: The protocol has Iterated through formal revisions — `2024-11-05`, `2025-03-26`, `2025-06-18`, `2025-11-25`, and the current **`2026-07-28`**. Governance is now formal: changes are proposed via **SEPs** (Specification Enhancement Proposals) with PR-derived numbering, sponsor responsibilities, and status management via PR labels. A feature lifecycle and deprecation policy defines Active, Deprecated, and Removed states with a minimum twelve-month deprecation window and a deprecated-features registry. ([changelog](https://raw.githubusercontent.com/modelcontextprotocol/modelcontextprotocol/main/docs/specification/2026-07-28/changelog.mdx), [community](https://github.com/modelcontextprotocol/modelcontextprotocol)) The schema is defined in TypeScript first (`schema/2026-07-28/schema.ts`), then published as JSON Schema for wider compatibility; the official docs site is [modelcontextprotocol.io](https://modelcontextprotocol.io).

**Notable adoption**: Usage is widespread across the AI tooling ecosystem. High-star repositories with `mcp` topics include [n8n-io/n8n](https://github.com/n8n-io/n8n) (210k+ stars), [langgenius/dify](https://github.com/langgenius/dify) (160k+ stars), [open-webui/open-webui](https://github.com/open-webui/open-webui) (158k+ stars), [dify](https://github.com/langgenius/dify), and [browser-use/browser-use](https://github.com/browser-use/browser-use) (120k+ stars) — all list `mcp`/`mcp-server`/`mcp-client` topics. The spec repo `modelcontextprotocol/docs` reports 431 stars and 275 forks. ([repo search](https://github.com/search?q=topic%3Amcp+stars%3E5000)), [n8n](https://github.com/n8n-io/n8n), [dify](https://github.com/langgenius/dify), [open-webui](https://github.com/open-webui/open-webui)

**Key considerations for our evaluation**: (1) the protocol is still evolving rapidly (multiple revisions in under two years), so pin to a specific revision; (2) Roots, Sampling, and Logging features are now deprecated with migration guidance; (3) HTTP+SSE transport is deprecated — plan for Streamable HTTP; (4) security principles (user consent, data privacy, tool safety) are implementor responsibility, not enforced at protocol level; (5) official SDKs and an MCP registry exist ([sdk.mdx](https://raw.githubusercontent.com/modelcontextprotocol/modelcontextprotocol/main/docs/docs/2026-07-28/sdk.mdx)).

## Sources

Full source list with notes is in [SOURCES.md](./SOURCES.md).
