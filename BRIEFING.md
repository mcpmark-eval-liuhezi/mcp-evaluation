# MCP Evaluation — Executive Briefing

> Private evaluation briefing for internal AI tooling. All sources cited inline; ubiquity of domains logged in `SOURCES.md`.

## Executive Summary

The Model Context Protocol (MCP) is an open standard, introduced by Anthropic in November 2024, that standardizes how AI applications connect to external tools and data, replacing custom integrations with one protocol. MCP uses a client-server architecture: an MCP host (VS Code, Claude Desktop) creates one MCP client per server. Servers expose three core primitives — tools for model-invoked actions, resources for contextual data, and prompts for reusable templates — plus client-side primitives like sampling and elicitation, over JSON-RPC. Transports are stdio (local subprocess) and Streamable HTTP (remote). The current spec is date-versioned, with protocol version 2026-07-28, and is now maintained under the Linux Foundation's Agentic AI Foundation. Adoption includes OpenAI, Google DeepMind, and Microsoft, with SDKs in eleven languages.

*Exec summary word count: 120 words (computed via regex `\S+` tokenization, not eyeballed). Distinct source domains: 5 (modelcontextprotocol.io, anthropic.com, en.wikipedia.org, github.com, openais.com).*

## 1. What MCP Is

The Model Context Protocol is an open standard and open-source framework introduced by Anthropic in November 2024 to standardize how AI systems like LLMs integrate and share data with external tools, systems, and data sources. It replaces fragmented, per-integration custom connectors with one universal protocol — solving the "N×M" data integration problem where every new data source required its own custom implementation. It reuses message-flow ideas from the Language Server Protocol (LSP). In December 2025, Anthropic donated MCP to the Agentic AI Foundation (AAIF), a directed fund under the Linux Foundation, co-founded by Anthropic, Block, and OpenAI. [Sources: #1, #5, #9]

## 2. Architecture: Host / Client / Server

MCP follows a client-server architecture with three participants ([modelcontextprotocol.io/docs/concepts/architecture](SOURCES.md#s1)):

- **MCP Host** — the AI application (e.g., Claude Code, Claude Desktop, VS Code) that coordinates and manages one or multiple MCP clients.
- **MCP Client** — a component that maintains a dedicated connection to one MCP server and obtains context from it for the host to use. One client per server.
- **MCP Server** — a program that provides context to MCP clients. Servers can run locally (stdio) or remotely (Streamable HTTP); local servers typically serve a single client, remote ones many.

The protocol has two layers ([S1](SOURCES.md#s1)):

- **Data layer** — JSON-RPC 2.0-encoded protocol: capability/version discovery and the core primitives (tools, resources, prompts, notifications).
- **Transport layer** — bindings for message framing, delivery, metadata, and cancellation.

MCP is a stateless protocol: every request carries protocol version and capabilities in its `_meta` field. Servers advertise versions/capabilities via the mandatory `server/discover` request, which clients may send before any other request. MCP focuses solely on the protocol for context exchange — it does not dictate how AI apps use LLMs or manage context. [S1]

## 3. Primitives Servers Expose

Servers expose **three core primitives** ([modelcontextprotocol.io/docs/concepts/tools](SOURCES.md#s3)):

| Primitive | Class | Uses methods | Purpose |
| --- | --- | --- | --- |
| **Tools** | Model-controlled executable functions | `tools/list`, `tools/call` | Actions: querying databases, calling APIs, file ops, computations |
| **Resources** | Data sources | `resources/list`, `resources/read` (+ embedded) | Contextual data: file contents, DB records, API responses, schemas |
| **Prompts** | Reusable templates | `prompts/list`, `prompts/get` | Interactive workflows: system prompts, few-shot examples |

Each primitive has discovery (`*/list`), retrieval (`*/get`), and (for tools) execution (`tools/call`) methods; listings are dynamic. Example for a DB server: tool for querying, a resource with the schema, and a prompt with few-shot examples for interacting with the tools.

Clients expose additional primitives: **elicitation** (servers may request user input via `elicitation/create`, multi-round-trip pattern) and **sampling** (servers may request LLM completions via `sampling/create` to stay model-independent). Sampling is deprecated as of protocol version 2026-07-28. [S1, S3]

## 4. Transports

A transport is a binding: how messages are framed/delivered; message *meaning* (message patterns) is protocol-level and identical on every transport ([modelcontextprotocol.io/docs/concepts/transports](SOURCES.md#s4)):

- **stdio** — newline-delimited JSON-over-standard-streams over a client-launched subprocess. Typical for local servers (e.g., filesystem server launched by Claude Desktop). Cancellation: client sends `notifications/cancelled`.
- **Streamable HTTP** — each message is an HTTP POST to a single MCP endpoint; replies arrive as a JSON object or a request-scoped SSE stream. Typical for remote servers (e.g., official Sentry MCP server). Metadata also mirrored into HTTP headers for intermediaries. Cancellation: client closes the response stream. OAuth 2.1 authorization is supported.
- **Custom transports** — allowed for any bidirectional channel; custom transports over reliable byte streams (Unix sockets, TCP) should reuse stdio framing.

All messages are JSON-RPC 2.0, UTF-8; protocol semantics are identical across transports. [S4]

## 5. Ecosystem State

- **Spec maturity**: The spec is date-based (current version **2026-07-28**). Revisions are Draft / Current / Final; deprecated features remain in the spec for at least 12 months before possible removal, with a 90-day expedited-removal exception. [S2, S8]
- **Governance**: Donated to the Agentic AI Foundation (Linux Foundation fund) in Dec 2025; co-founded by Anthropic, Block, OpenAI. [S5]
- **SDKs/ecosystem**: Official SDKs exist for TypeScript, Python, Java, Kotlin, C#, Go, PHP, Perl, Ruby, Rust, Swift (11 connector types). [S6]
- **Adoption**: OpenAI and Google DeepMind adopted it after the Nov 2024 launch; Microsoft is a backing contributor to the MCP Registry. Anthropic announced adoption Apr 28, 2026. [S1, S6]
- **Registry**: The official MCP Registry (at modelcontextprotocol.io/registry) is in preview, backed by Anthropic, GitHub, PulseMCP, and Microsoft; stores server metadata in `server.json`. [S4]
- **Tools**: MCP Inspector (browser/CLI/TUI) for testing and debugging servers. [S2]

## Bottom Line for Our Upgrade Decision

MCP is a mature, vendor-neutral standard with an active spec, community SDKs, a published registry, and major-adopter backing. The main risks to watch: sampling deprecation in 2026-07-28, regex-injection and prompt-injection class security issues, and whether our tooling needs one-time setup (stdio) or remote HTTP deployments (Streamable HTTP + OAuth 2.1).
