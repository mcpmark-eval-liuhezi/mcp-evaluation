# MCP Evaluation Briefing — Internal AI Tooling

## Executive Summary (120 words, computed programmatically)

MCP is an open standard, launched by Anthropic in November 2024, that standardizes how AI applications expose tools, data, and prompts to models — often called a "USB-C port for AI applications." Built on JSON-RPC 2.0, it replaces one-off integrations with a single host connecting to many servers. Servers expose three core primitives: tools, resources, and prompts, plus client-side features like sampling and elicitation. It runs over two standard transports: stdio for local subprocess servers and Streamable HTTP for remote deployment. Adoption is broad: OpenAI, Google, and Microsoft all support it; official SDKs exist for Python, TypeScript, and Rust; and the reference servers repo has roughly 92k stars. For our internal tooling, MCP is a low-risk, well-governed standard worth piloting.

 word-count verify: 120 words (whitespace split, computed via Python; under the 120-word limit).

---

## 1. What is MCP?

MCP is an open standard, launched by Anthropic in November 2024, that standardizes how AI applications expose tools, data, and prompts to models — often described as a "USB-C port for AI applications." Built on JSON-RPC 2.0, it replaces one-off integrations with a single host connecting to many servers via one common protocol. Servers expose three core primitives: tools (LLM-callable functions), resources (read-only data), and prompts (reusable templates), plus client-side features like sampling and elicitation. It was subsequently donated to the Linux Foundation's Agentic AI foundation, and adopted by major vendors ([explain MCP](https://www.explainx.ai/blog/agentic-ai/mcp-ai-open-standard-webinar)).

## 2. Architecture: host / client / server

- **Host applications** (Claude Desktop, ChatGPT, VS Code, Cursor, n8n) are applications that connect to servers and expose MCP **clients** that manage 1:1 connections with each MCP server.
- **Clients** carry the protocol implementation (the SDKs do this), handle capability negotiation, request routing, and context passing between host, server, and other clients (sampling conversations can span multiple hosts/clients).
- **Servers** expose primitives via JSON-RPC 2.0 to tool, resource, and prompt calls. Two stacks are available: **stdio** (local, subprocess transport) for local servers that talk to terminal applications, and **HTTP** (remote, TLS) for remote servers, diffusion across the network, and forking Pipelines ([modelcontextprotocol.io](https://modelcontextprotocol.io/docs/concepts/architecture)).

## 3. Primitives servers expose

**Server-side primitives:**
- **Tools** — LLM-callable function definitions / callable logic, including schema, descriptions, and handling.
- **Resources** — content data that can be read and referenced by tools and context (files, database rows, prompts).
- **Prompts** — reusable templates for prompting an LLM, e.g., specialized prompts.
- **Resources templates** — dynamic URIs (e.g., `file://{path}`) to template resource access.

**Client-side primitives:**
- **Sampling** — servers can request LLM completions through the client (`nimbus/protocol`, rpc name: `sampling`).
- **Roots** — URI scheme anchors a client exposes for server context understanding.
- **Elicitation** — servers (or hosts) can prompt users for additional information before completing a tool or resource call.
- **Logging notification** — trace server processes for debug and security.
- **Agents** — client-side auth flow for the server to authenticate the client; OAuth 2.1 authorization with resource server validation ([spec](https://modelcontextprotocol.io/specification/2025-11-25/), [SDK](https://github.com/modelcontextprotocol/python-sdk)).

## 4. Transports

- **stdio** — local subprocess transport: configures the client with a command, args, and env vars; builds local stdio servers/lifecycles ([modelcontextprotocol.io](https://modelcontextprotocol.io/docs/concepts/transports)).
- **HTTP** — remote transport over TLS; flow steps include capability negotiation (client sends roots), initialize request, subsequent operations over the same connection ([modelcontextprotocol.io](https://modelcontextprotocol.io/docs/concepts/transports)).
- **Streamable HTTP** — replaced the original HTTP + SSE transport introduced in the 2025-03-26 spec revision ([spec 2025-06-18](https://modelcontextprotocol.io/specification/2025-06-18/), [spec 2025-11-25](https://modelcontextprotocol.io/specification/2025-11-25/)). Every server in the reference servers repo serves stdio; only Everything also serves Streamable HTTP.

## 5. Ecosystem state

- **GitHub org** ([modelcontextprotocol](https://github.com/modelcontextprotocol/modelcontextprotocol), ~9.7k stars): ongoing spec and docs, spec revisions (2024-11-05 → 2025-06-18 → 2025-11-25) with new primitives (Streamable HTTP, OAuth authorization, elicitation).
- **Official SDKs with star counts:** Python ~25k, TypeScript ~14k, Rust ~4k; Reference servers repo ~92k stars ([github.com/modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers)).
- **Adoption:** OpenAI (March 2025), Google (Demis Hassabis publicly in June 2025), Microsoft (Copilot, September 2025) all adopted MCP; VS Code, Cursor, n8n, Windsurf, Outlook, Firebase, Cloudflare support it; one deployment put MCP inside the US federal government.
- **Our internal posture:** pairs with existing standards (OpenAPI, GraphQL) and AI SDKs within the same week; prerequisite is support for running servers on a user's local desktop; the protocol remains the clearest emerging standard for tool integration.
