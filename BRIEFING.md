# MCP Evaluation Briefing — Executive Summary

MCP is an open standard that standardizes how LLM applications connect to external data sources and tools. Its client-host-server architecture separates the host application (which manages clients, security, and consent) from clients (one per server) and servers (which expose context and capabilities). Servers offer three core primitives - tools (model-controlled execution), resources (app-controlled context), and prompts (user-controlled templates) - plus elicitation. Transport options are stdio for local subprocess servers and Streamable HTTP for remote services, with a custom-transport extension point. The spec is versioned and maintained under governance; adoption spans IDEs, cloud providers, and thousands of community servers, with official SDKs in TypeScript, C#, Python, Java, Kotlin, Ruby, Rust, Swift, and PHP.

---

## What MCP Is

The Model Context Protocol (MCP) is an open protocol that enables seamless integration between LLM applications and external data sources and tools. Whether you're building an AI-powered IDE, enhancing a chat interface, or creating custom AI workflows, MCP provides a standardized way to connect LLMs with the context they need.

It takes design inspiration from the Language Server Protocol (LSP), which standardized how to add support for programming languages across a whole ecosystem of development tools. In a similar way, MCP standardizes how to integrate additional context and tools into the ecosystem of AI applications.

The protocol is built on JSON-RPC 2.0 messages and is an open-source MIT-licensed standard. It is maintained by an open GitHub organization with contributing and working-group governance.

## Architecture: Host / Client / Server

MCP uses a **client-host-server architecture** where each host can run multiple client instances. MCP is a stateless protocol: every request is self-contained and carries its own protocol version and capabilities.

### Host
- Acts as the container and coordinator
- Creates and manages multiple client instances
- Controls client connection permissions and lifecycle
- Enforces security policies and consent requirements
- Handles user authorization decisions
- Coordinates AI/LLM integration and sampling
- Manages context aggregation across clients

### Clients
- Each is created by the host and communicates with exactly one server
- Attaches protocol version and capabilities to every request
- Routes protocol messages bidirectionally
- Manages subscriptions and notifications
- Maintains security boundaries between servers

### Servers
- Provide specialized context and capabilities
- Expose resources, tools and prompts via MCP primitives
- Operate independently with focused responsibilities
- Can be local processes or remote services
- Must respect security constraints (sampling, elicitation, roots)

**Design principles:** servers should be easy to build (hosts handle complex orchestration), highly composable (multiple servers combine seamlessly), not able to read the whole conversation or see into other servers (full history stays with the host), and features can be added progressively with backward compatibility.

## Primitives Servers Expose

Servers offer three core features to clients:

| Primitive | Control | Description | Example |
| --- | --- | --- | --- |
| **Tools** | Model-controlled | Functions exposed to the LLM to take actions | API POST requests, file writing |
| **Resources** | Application-controlled | Contextual data attached and managed by the client | File contents, git history |
| **Prompts** | User-controlled | Pre-defined templates invoked by user choice | Slash commands, menu options |

Clients may offer **elicitation** to servers: server-initiated requests for additional information from users. Additional utilities include configuration, progress tracking, cancellation, and error reporting. Optional extensions (Tasks, Skills, MCP Apps) are negotiated during initialization.

## Transports

Standard transports: **stdio** (client launches server as a subprocess, JSON-RPC newline-delimited over stdin/stdout; суставs on this for local tools) and **Streamable HTTP** (accepted in 2025-06-18, replacing the deprecated HTTP+SSE transport from 2024-11-05). Custom transports are also permitted for any bidirectional channel.

## Ecosystem State

The spec is actively versioned: 2024-11-05 → 2025-03-26 → 2025-06-18 → 2025-11-25 → 2026-07-28 (current). Notable adopters include AWS (awslabs/mcp), Tableau, Perplexity, and early partners Claude Desktop — a broad, standards-backed ecosystem with multiple official SDKs.
