# MCP Evaluation Briefing

Executive summary (113 words, computed):

MCP is an open standard for connecting AI models to external tools and data through a client-host-server architecture. A host (e.g., Claude Desktop) runs multiple clients; each client has a 1:1 relationship with a server, which exposes three core primitives: tools (model-controlled functions), resources (application-controlled data via URIs), and prompts (user-controlled templates). Servers communicate over JSON-RPC using stdio or Streamable HTTP transports. The spec is stable at revision 2026-07-28, governed by working groups and an SEP process. The ecosystem is genuine: 91.9k stars on the official reference servers repo, official SDKs in Python, TypeScript, and Go (co-maintained with Google), plus a community registry. However, Anthropic still leads governance, so watch the working-group direction.

---

## 1. What MCP Is

The Model Context Protocol (MCP) is an open standard for connecting AI applications to external data sources and tools (servers). It was created by David Soria Parra and Justin Spahr-Summers and is now maintained under the `modelcontextprotocol` GitHub organization. It is built on JSON-RPC and is transport-agnostic — the protocol describes *what* messages mean, and transports only describe *how* they are framed and delivered. Since its November 2024 launch, adoption has spread across major clients (Claude Desktop, Cursor, Windsurf, etc.) and it has graduated to a stable spec revision (`2026-07-28`).

**Sources:** https://modelcontextprotocol.io — https://github.com/modelcontextprotocol/modelcontextprotocol

## 2. Host / Client / Server Architecture

MCP follows a **client-host-server** architecture: the host is the container-coordinator process (e.g., an AI application) that creates and manages multiple client instances. Each client is created by the host and communicates with **exactly one server** (a 1:1 relationship). Servers provide specialized context and capabilities, can be local processes or remote services, and must respect security constraints set by the host. The protocol is stateless: every request is self-contained and carries its own protocol version and capabilities.

**Design principles:** servers should be easy to build, highly composable, isolated (cannot read the whole conversation or "see into" other servers), and instruments can be added progressively. Servers also may request client input (sampling, elicitation, roots) via `InputRequiredResult`.

**Sources:** https://modelcontextprotocol.io/specification/2026-07-28/architecture — https://github.com/modelcontextprotocol/modelcontextprotocol

## 3. Primitives Servers Expose

Three core primitives:

- **Tools** (model-controlled): schema-defined functions the LLM can invoke and that decide when to use them. Protocol operations: `tools/list`, `tools/call`. JSON Schema validation; per-execution user consent.
- **Resources** (application-controlled): structured read-only data as context (file contents, schemas, API docs). Identified by URI with a MIME type; support direct resources and parameterized Resource Templates (`resources/list`, `resources/templates/list`, `resources/read`, `subscriptions/listen`).
- **Prompts** (user-controlled): pre-built, parameterized templates the user explicitly invokes (`prompts/list`, `prompts/get`). Surface as slash commands and command-palette entries.

Plus server-side utilities: capabilities negotiation (`server/discover`, capability flags), subscriptions/notifications, and client-side features like sampling (`createMessage`), elicitation, and roots.

**Sources:** https://modelcontextprotocol.io/specification/2026-07-28/server/index.mdx — https://modelcontextprotocol.io/docs/2026-07-28/learn/server-concepts

## 4. Transports

MCP is built on **JSON-RPC (UTF-8)**. A transport is a *binding* — it defines how messages are framed and delivered, and how request metadata, cancellation, and termination are signaled — while message *patterns* (server capabilities, billing, sampling, etc.) stay constant:

1. **stdio** — newline-delimited messages over the standard streams of a client-launched subprocess. No port; client launches the server with `command`/`args`.
2. **Streamable HTTP** — each message is an HTTP POST to a single MCP endpoint; replies arrive as a JSON object or a request-scoped SSE stream.

Custom transports are allowed (must preserve JSON-RPC format and patterns); custom transports over reliable byte streams (Unix sockets, TCP) should reuse stdio framing instead. Earlier HTTP+SSE transports are deprecated. Each binding defines how a client abandons an in-flight request.

**Sources:** https://modelcontextprotocol.io/specification/2026-07-28/basic/transports — https://github.com/modelcontextprotocol/servers

## 5. Ecosystem State

- **Spec maturity:** Stable revision `2026-07-28` (published as a GitHub release). Multiple spec revisions exist (2024-11-05 → 2026-07-28), plus a versioned docs-sites (2024-11-05 through 2026-07-28).
- **Official repos:** `modelcontextprotocol` (9.7k+ stars, MIT), `servers` (91.9k stars, 11.9k forks — the reference implementations), `python-sdk` (24.8k), `typescript-sdk` (13.7k), `go-sdk` (5.3k, co-maintained with Google), `registry` (7.5k), and additional official SDKs in C#, Java, Kotlin, PHP, Ruby, Rust, Swift.
- **Registry:** A community-driven registry service for MCP servers exists at registry.modelcontextprotocol.io.
- **Adoption:** Clients like Claude Desktop ship MCP support; the architecture overview links to Cursor, Gemini, and OpenAI support.
- **Caveats:** Anthropic still leads governance (MCP is "managed by Anthropic, but built together with the community"), a C# SDK is maintained via the `maintainers.md` list with community collaboration, and security requires careful evaluation (the servers README itself warns reference servers are educational examples, not production-ready).

**Sources:** https://github.com/modelcontextprotocol/servers — https://github.com/modelcontextprotocol/python-sdk — https://registry.modelcontextprotocol.io
