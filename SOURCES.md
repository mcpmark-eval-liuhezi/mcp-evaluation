# MCP Evaluation — Source Registry

Distinct source domains (computed): **3** — `github.com`, `modelcontextprotocol.io`, `registry.modelcontextprotocol.io`.

## Primary sources (spec & docs)

| URL | What it backed |
|---|---|
| https://modelcontextprotocol.io | Official docs site — MCP overview, architecture, learn series (served from the `modelcontextprotocol/modelcontextprotocol` GitHub repo). |
| https://modelcontextprotocol.io/specification/2026-07-28/architecture | Client-host-server architecture: host/client/server responsibilities, design principles, capability negotiation (server/discover). |
| https://modelcontextprotocol.io/specification/2026-07-28/basic/transports | Transports: stdio and Streamable HTTP bindings, JSON-RPC framing, custom transports, backward compatibility. |
| https://modelcontextprotocol.io/specification/2026-07-28/server/index.mdx | The three server primitives (prompts, resources, tools) with the user/application/model control hierarchy. |
| https://modelcontextprotocol.io/docs/2026-07-28/learn/server-concepts | Deep-dive on tools (JSON Schema, call workflow), resources (URIs, templates), and prompts (invocation controls). |
| https://modelcontextprotocol.io/specification/2026-07-28 | Spec index: revision listing, changelog, working group/SEP process. |

## Reference implementations & SDKs

| URL | What it backed |
|---|---|
| https://github.com/modelcontextprotocol/modelcontextprotocol | Spec repo: created by David Soria Parra & Justin Spahr-Summers; MIT license; schema in TypeScript + JSON Schema. |
| https://github.com/modelcontextprotocol/modelcontextprotocol/releases/tag/2026-07-28 | Stable `2026-07-28` spec release (created 2026-07-28). |
| https://github.com/modelcontextprotocol/servers | Reference-servers README: transports (stdio/Streamable HTTP), client config examples, MCP Inspector, 7 active + 13 archived servers. |
| https://github.com/modelcontextprotocol/python-sdk | Official Python SDK (24.8k stars). |
| https://github.com/modelcontextprotocol/typescript-sdk | Official TypeScript SDK (13.7k stars). |
| https://github.com/modelcontextprotocol/go-sdk | Official Go SDK (5.3k stars), co-maintained with Google. |
| https://github.com/modelcontextprotocol/registry | Community-driven MCP server registry (7.5k stars). |
| https://github.com/modelcontextprotocol/inspector | MCP Inspector: stdio/Streamable HTTP debugging tool for testing servers. |

## Ecosystem evidence

| URL | What it backed |
|---|---|
| https://registry.modelcontextprotocol.io | Public MCP server registry service — real deployment surface. |
| https://github.com/mcp-evaluation (this repo) | Internal evaluation artifact. |
