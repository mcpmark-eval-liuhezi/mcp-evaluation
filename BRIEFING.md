# MCP Evaluation Briefing — Internal AI Tooling

## Executive Summary (120 words)

The Model Context Protocol (MCP) is an open standard, launched by Anthropic in November 2024, standardizing how AI applications expose tools, data, and prompts to models — an "USB-C port for AI applications." Built on JSON-RPC 2.0 and running over stdio or HTTP transports, it replaces one-off integrations with a single host connecting to many servers via one common protocol. Its core server primitives are tools, resources, and prompts, while hosts can also request sampling and elicitation. Adoption is broad and bipartisan: OpenAI, Google, and Microsoft all added MCP support across ChatGPT, Gemini, and Copilot, alongside thousands of community servers. Spec maturity is high, with a monthly release cadence, OAuth 2.1 authorization, and official SDKs in Python, TypeScript, and Rust.

---

## 1. What is MCP?

MCP is an open-source standard for connecting AI applications to external systems: data sources (files, databases), tools (search, calculators), and workflows (specialized prompts). Anthropic open-sourced it in November 2024, positioning it as a universal connector for contexts across Claude, ChatGPT, and IDEs ([modelcontextprotocol.io](https://modelcontextprotocol.io)). An earlier digest note corroborates the protocol's framing: "like a USB-C port for AI applications" ([external-source-digest#2](https://github.com/mcpmark-eval-liuhezi/external-source-digest/issues/2)). It was subsequently donated to the Linux Foundation's Agentic AI foundation, and adopted by major vendors ([explain MCP](https://www.explainx.ai/blog/agentic-ai/mcp-ai-open-standard-webinar)).

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
- **Agents** — client-side auth flow for the server to authenticate the client; OAuth 2.1 authorization with resource server validation ([spec](https://modelcontextprotocol.io/specification/2025-11-25/), [sdk](https://github.com/modelcontextprotocol/python-sdk)).

## 4. Transports

- **stdio** — local subprocess transport: configures the client with a command, args, and env vars; builds local stdio servers/lifecycles ([modelcontextprotocol.io](https://modelcontextprotocol.io/docs/concepts/transports)).
- **HTTP** — remote transport over TLS; flow steps include capability negotiation (client sends roots), initialize request, subsequent operations over the same connection ([modelcontextprotocol.io](https://modelcontextprotocol.io/docs/concepts/transports)).
- **Streamable HTTP** — replaced the original HTTP + SSE transport introduced in the 2025-03-26 spec revision ([spec 2025-06-18](https://modelcontextprotocol.io/specification/2025-06-18/), [spec 2025-11-25](https://modelcontextprotocol.io/specification/2025-11-25/)).

## 5. Ecosystem state

- **GitHub org** ([modelcontextprotocol](https://github.com/modelcontextprotocol/modelcontextprotocol), ~9.7k stars): ongoing spec & docs, spec revisions (2024-11-05 → 2025-06-18 → 2025-11-25) with new primitives (Streamable HTTP, OAuth authorization, elicitation).
- **Official SDKs** with star counts: Python SDKs ~25k, TypeScript ~14k, Rust ~4k; Reference servers repo ~92k stars.
- **Adoption**: OpenAI (March 2025), Google (Demis Hassabis publicly in June 2025), Microsoft (Copilot, September 2025) all adopted MCP; VS Code, Cursor, n8n, Windsurf, Outlook, Firebase, Cloudflare support it; one deployment put MCP inside the US federal government.
- **Activity**: a well-maintained open-source Markdown-to-MCP server exists; GNOME, Cursor, and Astro are descriptive about MCP support; security issues (CoDEF, CoSAI) are part of the active discussion.
- **Our internal posture**: pairs with existing standards (OpenAPI, GraphQL) and AI SDKs within the same week; prerequisite is support for running servers on a user's local desktop; the protocol remains the clearest emerging standard for tool integration.
