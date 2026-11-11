# MCP Evaluation Briefing — Model Context Protocol for Internal AI Tooling

## Executive Summary (90 words)

The Model Context Protocol (MCP) is an open-source standard that connects AI applications to external systems — data sources, tools, and workflows — using a JSON-RPC 2.0 client-server protocol. MCP hosts spawn one client per server; servers expose three core primitives: tools (model-controlled actions), resources (application-read data), and prompts (user-defined templates), plus elicitation. Two transports are supported: stdio for local processes and Streamable HTTP for remote servers. The spec is now at version 2026-07-28 with extension support, and all major clients — Claude, ChatGPT, VS Code, Cursor — support it.

## Fully-Sourced Notes

### 1. Definition
MCP is an open-source standard for connecting AI applications to external systems. It standardizes how Claude, ChatGPT, editors, and agents reach data sources, tools, and workflows. The docs' analogy: MCP is like USB-C for AI applications — one standardized connector for many internal systems ([modelcontextprotocol.io](https://modelcontextprotocol.io/docs/2026-07-28/getting-started/intro)).

### 2. Architecture: Host / Client / Server
- **MCP Host** — the AI application (e.g., Claude Code, VS Code, Claude Desktop) that coordinates one or more MCP clients ([architecture](https://modelcontextprotocol.io/docs/2026-07-28/learn/architecture)).
- **MCP Client** — a component inside the host that maintains a dedicated connection to one MCP server ([architecture](https://modelcontextprotocol.io/docs/2026-07-28/learn/architecture)).
- **MCP Server** — the program providing context/capabilities to clients; local ones use stdio (single client), remote ones use Streamable HTTP (many clients) ([architecture](https://modelcontextprotocol.io/docs/2026-07-28/learn/architecture)).
- Two layers: **data layer** (JSON-RPC 2.0, `server/discover`, primitives, notifications, progress) and **transport layer** (connection framing, auth) ([spec index](https://modelcontextprotocol.io/specification/2026-07-28)).

### 3. Primitives servers expose
| Primitive | Control | Purpose | Protocol ops |
|---|---|---|---|
| Tools | Model | Invocable actions w/ typed inputs/outputs; `tools/list`, `tools/call` | tools/list, tools/call |
| Resources | Application | Read-only context via URI + MIME; direct + templates; subscriptions | resources/list, resources/read, subscriptions/listen |
| Prompts | User | Pre-built instruction templates | prompts/list, prompts/get |
| Elicitation (client feature) | Server→user | Structured user-info requests; sampling deprecated in 2026-07-28 | — ([server-concepts](https://modelcontextprotocol.io/docs/2026-07-28/learn/server-concepts), [spec](https://modelcontextprotocol.io/specification/2026-07-28)). |

### 4. Transports
- **stdio**: local subprocess, stdin/stdout, no port — no network overhead ([arch](https://modelcontextprotocol.io/docs/2026-07-28/learn/architecture)).
- **Streamable HTTP**: HTTP POST + optional SSE; OAuth recommended; one server many clients ([arch](https://modelcontextprotocol.io/docs/2026-07-28/learn/architecture)).

### 5. Ecosystem State
- Latest spec: **2026-07-28** (prior: 2025-11-25, 2025-06-18, 2025-03-26, 2024-11-05) ([spec index](https://modelcontextprotocol.io/specification/2026-07-28) + changelog).
- Broad adoption: Claude, ChatGPT, VS Code, Cursor, MCPJam all support it ([intro](https://modelcontextprotocol.io/docs/2026-07-28/getting-started/intro)).
- SDKs: 10+ languages ([spec index](https://modelcontextprotocol.io/specification/2026-07-28)).
- Registry: registry.modelcontextprotocol.io ([servers README](https://github.com/modelcontextprotocol/servers/blob/main/README.md)).

### 6. Raw Sources Read
Content uncontangled from the spec and docs repos: [repo README](https://github.com/modelcontextprotocol/modelcontextprotocol), [spec index](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/specification/2026-07-28/index.mdx), [architecture](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/docs/2026-07-28/learn/architecture.mdx), [server-concepts](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/docs/2026-07-28/learn/server-concepts.mdx), [intro](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/docs/docs/2026-07-28/getting-started/intro.mdx), [servers repo](https://github.com/modelcontextprotocol/servers — README + transports table).
