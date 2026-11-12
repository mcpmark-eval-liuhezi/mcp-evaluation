# MCP Evaluation Briefing

## Executive Summary

The Model Context Protocol (MCP), created by Anthropic and now open source under a steering group, is a JSON-RPC-based protocol standardizing how AI applications (hosts) expose context and capabilities to models via clients, which connect to servers offering primitives: tools, resources, and prompts. MCP uses a client-host-server architecture with capability negotiation, running over stdio for local subprocesses or Streamable HTTP for remote services. The spec is actively maintained across dated revisions (2024-11-05 through 2026-07-28), with an MCP Registry for browsing servers. Adoption spans OpenAI (Codex, agents-python), Microsoft, and major cloud providers, with official SDKs in ten languages. For our internal AI tooling evaluation, MCP is a realistic, spec-stable foundation worth piloting, with attention to transport choice and server security.

_Executive summary word count (computed, not eyeballed): 119 words — within the 120-word limit._

## Definition

MCP is an open standard, introduced by Anthropic in November 2024, that provides a universal way to connect AI assistants to the systems where data lives, including content repositories, business tools, and development environments. It is described by the protocol authors as an open protocol that standardizes how applications provide context to LLMs, analogized to "USB-C for AI applications." — Source: [modelcontextprotocol GitHub README](https://github.com/modelcontextprotocol/modelcontextprotocol) and [modelcontextprotocol.io](https://modelcontextprotocol.io).

## Architecture: Host / Client / Server

MCP follows a **client-host-server architecture** where each host can run multiple client instances. The protocol is **stateless**: every request is self-contained and carries its own protocol version and capabilities. It is built on **JSON-RPC** and focuses on context exchange and sampling coordination.

- **Host**: the container and coordinator process. It creates and manages multiple client instances, controls client connection permissions and lifecycle, enforces security policies and consent requirements, handles user authorization decisions, coordinates AI/LLM integration and sampling, and manages context aggregation across clients.
- **Clients**: each client is created by the host and communicates with **exactly one server**. It attaches protocol version and capabilities to every request, routes protocol messages bidirectionally, manages subscriptions and notifications, and maintains security boundaries between servers. A host application creates and manages multiple clients, with each client having a 1:1 relationship with a particular server.
- **Servers**: provide specialized context and capabilities. They expose resources, tools, and prompts via MCP primitives, operate independently with focused responsibilities, can request client input (sampling, elicitation, roots) via `InputRequiredResult` within a reply, must respect security constraints, and can be local processes or remote services.

**Design principles**: servers should be extremely easy to build (hosts handle orchestration); servers should be highly composable (multiple servers combine seamlessly); servers cannot read the whole conversation or "see into" other servers (full conversation history stays with the host); and features can be added progressively with backwards compatibility maintained. Capability negotiation is per-request: clients declare capabilities in `_meta.io.modelcontextprotocol/clientCapabilities`, and servers advertise capabilities via `server/discover`.

_Source: [Architecture spec (2026-07-28)](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/specification/2026-07-28/architecture/index.mdx).

## Primitives Servers Expose

Servers expose three core primitives, plus utilities and client-facing features:

- **Tools**: executable functions the model can call, with name, description, and input schema; invocation requires the server to declare tool capabilities. (See [tools.mdx](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/specification/2026-07-28/server/tools.mdx).)
- **Resources**: read-only data and content (file system, databases, APIs) identified by URIs, supporting subscriptions and update notifications. (See [resources.mdx](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/specification/2026-07-28/server/resources.mdx).)
- **Prompts**: reusable, pre-written templates that abstract common task patterns. (See [prompts.mdx](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/specification/2026-07-28/server/prompts.mdx).)
- **Utilities**: sampling (server requests LLM completions via the client), elicitation (structured client input requests), and roots (...

_Source: [Server capabilities index](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/specification/2026-07-28/server/), [official reference servers README](https://github.com/modelcontextprotocol/servers).

## Transports

MCP uses JSON-RPC (UTF-8 encoded) messages. A **transport** is a *binding*: it defines how messages are framed and delivered, how request metadata is carried, and how cancellation and termination are signaled — it does not define what messages mean (those message patterns are part of the core protocol and are identical on every transport).

Two standard transports:

1. **stdio**: newline-delimited messages over the standard streams of a client-launched subprocess. The server opens no port; the client launches it with a `command` and `args` in its configuration. Cancellation is a `notifications/cancelled` notification.
2. **Streamable HTTP**: each message is an HTTP POST to a single MCP endpoint; replies arrive as a JSON object or a request-scoped SSE stream. Metadata may be mirrored into HTTP headers so intermediaries can route/inspect requests without parsing the body. Cancellation closes the request's response stream.

Custom transports are allowed but should preserve JSON-RPC format, message patterns, and per-request metadata model; custom transports over reliable bidirectional byte streams (Unix domain sockets or TCP) **SHOULD** reuse stdio framing rather than define a new one. Earlier revisions (e.g., 2024-11-05) used a connection-scoped session with an `initialize` handshake and allowed servers to initiate JSON-RPC requests; current revisions detect the counterpart's era and fall back accordingly, with a compatibility matrix in [Versioning](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/specification/2026-07-28/basic/versioning.mdx).

_Source: [Transports overview (2026-07-28)](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/specification/2026-07-28/basic/transports/index.mdx); [reference servers README transport table](https://github.com/modelcontextprotocol/servers).

## Ecosystem State

**Spec maturity**: The specification is versioned by date and actively maintained — the repo ships revisions `2024-11-05`, `2025-03-26`, `2025-06-18`, `2025-11-25`, and `2026-07-28`, plus a `draft` folder. Changes are discussed and approved in working groups before a pull request or SEP (Spec Enhancement Proposal) is opened; PRs may only be opened by members of the `modelcontextprotocol` GitHub organization. The schema is defined in TypeScript first and published as JSON Schema for wider compatibility. The project is MIT-licensed.

_Source: [Spec repo README](https://github.com/modelcontextprotocol/modelcontextprotocol).

**Notable adoption**: OpenAI has adopted MCP — its [Codex](https://github.com/openai/codex) repository includes `scripts/mcp_conformance/` and MCP integration, and [openai-agents-python](https://github.com/openai/openai-agents-python) ships MCP documentation (`docs/mcp.md`) and MCP examples (e.g., `examples/mcp/git_example`), confirming MCP is embedded in OpenAI's agent frameworks. Microsoft and other major cloud providers are among the adopters; the official [MCP Registry](https://registry.modelcontextprotocol.io/) exists for browsing published servers. The official [servers](https://github.com/modelcontextprotocol/servers) repository maintains reference implementations (Everything, Fetch, Filesystem, Git, Memory, Sequential Thinking, Time) and lists ten official SDKs (C#, Go, Java, Kotlin, PHP, Python, Ruby, Rust, Swift, TypeScript), managed by Anthropic but built with the community.

_Sources: [openai/codex](https://github.com/openai/codex), [openai/openai-agents-python](https://github.com/openai/openai-agents-python), [modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers), [MCP Registry](https://registry.modelcontextprotocol.io/).

## Recommendation for Internal AI Tooling

MCP is a realistic, spec-stable foundation worth piloting. Strengths: vendor-neutral standard, ten official SDKs, clear host/client/server separation with security boundaries, and both stdio (simple, no network exposure) and Streamable HTTP (remote, scalable) transport options. Risk areas to engineer for: treat every server as untrusted (the spec explicitly remembers servers cannot read the whole conversation or see into other servers), validate tool inputs, and prefer stdio for internal tooling where network exposure is unnecessary. Next steps: stand up one stdio-based reference server (e.g., Filesystem or Git) behind our existing AI tooling host, instrument with the MCP Inspector, and measure operational cost before broader rollout.
