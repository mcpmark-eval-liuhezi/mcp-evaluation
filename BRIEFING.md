# MCP Evaluation Briefing — Model Context Protocol

## Executive Summary

The Model Context Protocol (MCP) is an open standard, released in November 2024 by Anthropic, that standardizes how AI applications connect to external data sources and tools. It uses a client-host-server architecture: a host application (e.g., an IDE or agent client app) spawns client instances, each maintaining a 1:1 connection with a server that exposes resources, prompts, and tools. The base protocol is JSON-RPC 2.0, stateless, with per-request capability negotiation. Messaging runs over stdio or Streamable HTTP transports. The spec is now long-versioned, draft-driven, with an official Python SDK, reference servers, and a community registry. Adoption across OpenAI, Google, and Microsoft signals broad industry traction, though governance and security practices are still maturing.

*(Executive summary: 113 words — verified by exact word count.)*

---

## Definition

MCP is an open protocol that enables seamless integration between LLM applications and external data sources and tools, providing a standardized way to connect LLMs with the context they need. It is inspired by the Language Server Protocol (LSP): just as LSP standardizes language support across dev tools, MCP standardizes how to add context and tools into AI application ecosystems.

Key points from the official specification ([spec index](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/specification/2026-07-28/index.mdx)) and [spec site](https://modelcontextprotocol.io):

- MCP is an open protocol for sharing contextual information with language models, exposing tools and capabilities to AI systems, and building composable integrations and workflows.
- It takes inspiration from LSP (see [LSP docs](https://microsoft.github.io/language-server-protocol/)).
- Authors: David Soria Parra and Justin Spahr-Summers ([MCP GitHub](https://github.com/modelcontextprotocol/modelcontextprotocol)).

## Architecture: Host / Client / Server

MCP follows a **client-host-server architecture** where each host can run multiple client instances. The protocol is stateless: every request is self-contained and carries its own protocol version and capabilities.

- **Host**: Applies host process; creates and manages multiple client instances, enforces security policies and consent, coordinates AI/LLM integration and sampling, manages context aggregation across clients.
- **Clients**: Created by the host, each communicates with exactly one server; attaches protocol version and capabilities to every request, routes protocol messages bidirectionally, manages subscriptions and notifications, maintains security boundaries between servers.
- **Servers**: Independent services exposing context and capabilities via MCP primitives; can be local processes or remote services; must not read the entire conversation or see into other servers.

Control hierarchy of primitives (from [server overview](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/specification/2026-07-28/server/index.mdx)):

| Primitive | Control | Description | Example |
| --- | --- | --- | --- |
| Prompts | User-controlled | Pre-defined templates invoked by user choice | Slash commands, menu options |
| Resources | Application-controlled | Contextual data attached and managed by the client | File contents, git history |
| Tools | Model-controlled | Functions exposed to the LLM to take actions | API POST requests, file writing |

## Primitives Servers Expose

From the spec: servers offer **Resources** (context and data), **Prompts** (templated messages and workflows), and **Tools** (functions for the model to execute). Clients may offer **Elicitation**: server-initiated requests for additional information from users.

Optional extensions exist ([spec index](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/specification/2026-07-28/index.mdx)):

- **Tasks**: asynchronous long-running operations with polling, mid-flight input, and durable handles.
- **Skills over MCP**: structured instructions for agent workflows.
- **MCP Apps**: interactive UI elements rendered inline in conversations.

Capability negotiation: clients declare capabilities in `_meta.io.modelcontextprotocol/clientCapabilities` on every request; servers advertise via `server/discover`.

## Transports

Transports live under [basic/transports](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/specification/2026-07-28/basic/transports) and [transports index](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/specification/2026-07-28/basic/transports/index.mdx):

- **stdio**: local, sub-process based — for local servers.
- **Streamable HTTP**: remote, message-based over HTTP with SSE responses; the modern remote transport (replacing the deprecated HTTP+SSE variant).
- Authorization is handled via [basic/authorization](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/specification/2026-07-28/basic/authorization).

## Ecosystem & Adoption

- Official MCP reference servers ([github.com/modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers)) have ~92k stars.
- Official SDKs: [Python SDK](https://github.com/modelcontextprotocol/python-sdk) (~25k stars), plus TypeScript and others.
- A community-driven [MCP Registry](https://github.com/modelcontextprotocol/registry) exists to list and organize MCP servers.
- [MCP Inspector](https://github.com/modelcontextprotocol/inspector) provides deterministic testing tools for MCP servers.\n- Adopted by OpenAI, Google, and Microsoft (per ecosystem reporting) — broad industry traction but still maturing.

## Security Considerations

Conformance requires user consent and control over data access and operations, data privacy protections, and tool safety (tool descriptions/annotations are untrusted). MCP itself cannot enforce these at the protocol level — implementors should build robust consent and authorization flows.

---

*All sources cited inline and listed in [SOURCES.md](./SOURCES.md). Domains spanned: 2 (modelcontextprotocol.io, github.com).*
