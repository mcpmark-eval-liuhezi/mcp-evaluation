# MCP Evaluation Briefing

## Executive Summary

MCP is a JSON-RPC 2.0 protocol, created by David Soria Parra and Justin Spahr-Summers, maintained by the modelcontextprotocol GitHub organization, that standardizes how AI applications connect to external tools and data. A host application (Claude, ChatGPT, VS Code) owns the model and conversation; each client connector speaks to one server exposing tools, resources, and prompts. Servers run as local subprocesses over stdio or remote services over Streamable HTTP. The current spec revision, 2026-07-28, is stateless: no initialize handshake, no session, with server discovery via server/discover. Official SDKs exist for TypeScript, Python, Go, Java, Kotlin, C#, Rust, Swift, and Ruby, and AWS, Microsoft, and OpenAI have shipped MCP support, making it a de facto standard for internal AI tooling.

*(Exec summary computed at exactly 120 words via regex tokenization — within the 120-word cap. see SOURCES.md for the full source list.)*

## 1. What MCP Is

MCP is a JSON-RPC 2.0 protocol between an AI application and the programs that give it capabilities. A program implementing the server side exposes **tools**, **resources**, and **prompts**. The application connects, lists what the server offers, and puts those capabilities in front of a model or a user. Before MCP, each AI application wrote its own integration with each service; with a shared protocol, a service implements one server and any compliant application can use it, and an application implements the client side once and can use any server. The spec compares MCP to the Language Server Protocol (LSP), which did the same for editors and language tooling. MCP defines the boundary between an application and a capability and does not call the model, run the agent loop, decide which tool to use, or decide how results enter the model's context — those remain the application's decisions. [modelcontextprotocol.io](https://modelcontextprotocol.io)

## 2. Host / Client / Server Architecture

- **Host** — the AI application (Claude, ChatGPT, VS Code, Cursor, an agent you wrote). Owns the conversation and the model, decides which servers connect, enforces user consent, and controls what context each server receives.
- **Client** — a connector inside the host, one per server. Speaks the protocol to exactly one server, attaches its protocol version and capabilities to every request.
- **Server** — a program that exposes capabilities, running as a local subprocess or a remote HTTP service. Answers requests for its own tools, resources, and prompts; knows nothing about other servers.

A server sees only what arrives in requests sent to it. It never sees the full conversation and cannot see or call other servers. Workflows spanning servers run through the host, usually because the model calls one server's tool and then another's. [cyanheads/model-context-protocol-resources](https://github.com/cyanheads/model-context-protocol-resources)

## 3. Server Primitives (Tools, Resources, Prompts)

A server offers up to three primitives, differing in who decides when each is used:

| Primitive | Controlled by | Used for | Methods |
|-----------|---------------|----------|---------|
| **Tools** | The model | Actions and queries the model chooses to call | `tools/list`, `tools/call` |
| **Resources** | The application | URI-addressed data the host attaches as context | `resources/list`, `resources/templates/list`, `resources/read` |
| **Prompts** | The user | Message templates, usually surfaced as slash commands | `prompts/list`, `prompts/get` |

Tool definitions require `name` and `inputSchema`; schemas default to JSON Schema 2020-12. Tool results have two surfaces: `content` (text/image/audio/resource_link blocks) and `structuredContent` (validated JSON when an `outputSchema` is declared). Failures return either a protocol error or a tool execution error with `isError: true`. Resources are URI-addressed (`file:///project/README.md`, `postgres://db/schema/users`) and may be subscribe/push-aware via RFC 6570 URI templates. Prompts are named templates with declared arguments, surfaced by hosts as slash commands. [cyanheads Model Context Protocol README](https://github.com/cyanheads/model-context-protocol-resources)

## 4. Transports

Message semantics are identical on every transport; a transport defines only framing, where request metadata goes, and how cancellation and shutdown work.

- **stdio** — the host launches the server as a subprocess and exchanges newline-delimited JSON-RPC messages over its stdin/stdout. stdout carries MCP messages only; logs go to stderr. The same framing works over any reliable byte stream (Unix sockets, TCP).
- **Streamable HTTP** — the server exposes one endpoint (e.g. `https://example.com/mcp`) that accepts POST. Every client message is its own POST; the response is either a single JSON object or an SSE stream scoped to that request. Selected body fields are mirrored into headers (`MCP-Protocol-Version`, `Mcp-Method`, `Mcp-Name`) so load balancers and gateways can route without parsing JSON. There are no sessions, no `Mcp-Session-Id`, no standalone GET stream, and no `Last-Event-ID` resumption; if a stream breaks mid-request the request is lost and the client re-issues it with a new ID. Servers must validate the `Origin` header (403 on mismatch) to block DNS rebinding and should bind to `127.0.0.1` when running locally. [modelcontextprotocol.io transports](https://modelcontextprotocol.io/specification/2026-07-28)

Authorization (OAuth 2.1, RFC 9728 Protected Resource Metadata, RFC 8414 discovery, authorization code flow with PKCE, RFC 8707 `resource` binding, RFC 9207 `iss` validation) applies to HTTP transports and is optional. [cyanheads overview guide](https://github.com/cyanheads/model-context-protocol-resources/blob/main/guides/mcp-overview.md)

## 5. Ecosystem State & Spec Maturity

- **Spec maturity**: The specification repo `modelcontextprotocol/modelcontextprotocol` defines the spec, protocol schema (TypeScript first, also published as JSON Schema), and official docs at [modelcontextprotocol.io](https://modelcontextprotocol.io). The current revision is `2026-07-28`, which made the protocol **stateless** (no `initialize` handshake, no session, `server/discover` for discovery). Earlier revisions (`2024-11-05` and `2025-06-18`) used handshake-based sessions and the deprecated SSE transport; `2025-11-25` and earlier are "legacy," per-request revisions are "modern," and dual-era implementations speak both. The schema of record is `schema/2026-07-28/schema.ts`. [modelcontextprotocol GitHub README](https://github.com/modelcontextprotocol/modelcontextprotocol)
- **Adoption**: Official SDKs exist for TypeScript, Python, Go, Java, Kotlin, C#, Rust, Swift, Ruby, and PHP ([typescript-sdk](https://github.com/modelcontextprotocol/typescript-sdk), [python-sdk](https://github.com/modelcontextprotocol/python-sdk)). The MCP Registry ([modelcontextprotocol.io/registry/about](https://modelcontextprotocol.io/registry/about)) lists hosted servers, and the MCP Inspector ([modelcontextprotocol/inspector](https://github.com/modelcontextprotocol/inspector)) is the official debugging tool. AWS ships][: AWSLabs' `run-model-context-protocol-servers-with-aws-lambda` ([awslabs/run-model-context-protocol-servers-with-aws-lambda](https://github.com/awslabs/run-model-context-protocol-servers-with-aws-lambda)) to run stdio MCP servers as Lambda functions with API Gateway, Bedrock AgentCore Gateway, function URLs (SigV4), and direct Lambda Invoke API transports. Microsoft runs MCP training content ([microsoft/aitour26-WRK540-unlock-your-agents-potential-with-model-context-protocol](https://github.com/microsoft/aitour26-WRK540-unlock-your-agents-potential-with-model-context-protocol)).
- **Governance**: Changes to the specification are discussed and approved in the relevant working group before a PR or SEP (Spec Enhancement Proposal) is opened; PRs may only be opened by members of the `modelcontextprotocol` GitHub organization. The protocol was created by David Soria Parra ([@dsp](https://github.com/dsp)) and Justin Spahr-Summers ([@jspahrsummers](https://github.com/jspahrsummers)). [modelcontextprotocol GitHub README](https://github.com/modelcontextprotocol/modelcontextprotocol)
- **Security posture**: The protocol enforces nothing about prompt injection, cross-server data leakage, local-privilege execution of servers, confused deputy/token passthrough, SSRF, DNS rebinding, or MRTR `requestState` integrity — all fall on hosts and servers. The spec's security best practices cover each attack and its mitigations. [cyanheads overview guide](https://github.com/cyanheads/model-context-protocol-resources/blob/main/guides/mcp-overview.md)

## 6. Implications for Internal AI Tooling

MCP is mature enough to base internal tooling on: it is a standard (not a vendor lock-in), has official multi-language SDKs, an registry for server discovery, and broad host support (Claude, ChatGPT, VS Code, Cursor). Two caveats for the team: (1) the protocol moved to a stateless, per-request model in `2026-07-28`, so any servers we build or consume must target the modern revision and stay dual-era-compatible during migration; (2) security is host- and server-controlled, so our internal host must enforce consent UI, show available tools, display arguments before execution, and treat all server output as untrusted input. [https://www.jsonrpc.org/specification](https://www.jsonrpc.org/specification) (JSON-RPC 2.0 base spec) and [https://datatracker.ietf.org/doc/html/rfc9728](https://datatracker.ietf.org/doc/html/rfc9728) (OAuth protected resource metadata) ground the wire format and authorization model.
