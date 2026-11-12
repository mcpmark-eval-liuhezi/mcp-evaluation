# MCP Evaluation Briefing

Prepared for internal review of adopting the Model Context Protocol (MCP) for internal AI tooling.

## Executive Summary

The Model Context Protocol (MCP) is an open standard, created by David Soria Parra and Justin Spahr-Summers and now maintained by the `modelcontextprotocol` GitHub organization, that lets developers build servers exposing data and functionality to LLM applications in a secure, standardized way—essentially a web API designed for LLM interactions. An MCP host (e.g., Claude Desktop, an IDE) embeds clients that connect to MCP servers exposing three core primitives: tools (executable functions), resources (data/context), and prompts (reusable templates). Messages flow over two standard transports: stdio for local subprocess deployment and Streamable HTTP for remote deployment, with the older HTTP+SSE transport deprecated. The spec ships on dated revisions (current stable: 2026-07-28), backed by ten official SDKs, an MCP Registry, and broad IDE/vendor adoption. We recommend adopting MCP for internal tooling, starting with reference servers and stdio-based internal servers, while tracking spec revisions and version negotiation.

## Definition

MCP is an open protocol, specified and maintained in the [modelcontextprotocol/modelcontextprotocol](https://github.com/modelcontextprotocol/modelcontextprotocol) repository, that "lets you build servers that expose data and functionality to LLM applications in a secure, standardized way. Think of it like a web API, but designed for LLM interactions." ([python-sdk README](https://github.com/modelcontextprotocol/python-sdk), [spec repo README](https://github.com/modelcontextprotocol/modelcontextprotocol)). It was created by David Soria Parra (@dsp) and Justin Spahr-Summers (@jspahrsummers) and is MIT-licensed, with Changes to the specification discussed in working groups before a Pull Request or SEP is opened ([spec repo README](https://github.com/modelcontextprotocol/modelcontextprotocol)).

## Architecture: Host / Client / Server

- **Host**: The LLM application (e.g., Claude Desktop, an IDE) that wholly or partly implements the MCP client logic and provides access to its tools and context. ([spec repo README](https://github.com/modelcontextprotocol/modelcontextprotocol))
- **Client**: A protocol receiver inside the host that maintains a 1:1 connection with a server, handling invocation, sampling, and other capabilities. ([spec repo README](https://github.com/modelcontextprotocol/modelcontextprotocol))
- **Server**: A program (local or remote) that exposes capabilities (tools, resources, prompts) to clients through the protocol. ([servers README](https://github.com/modelcontextprotocol/servers))
- The [servers README](https://github.com/modelcontextprotocol/servers) documents the reference servers maintained by the MCP steering group: Everything, Fetch, Filesystem, Git, Memory, Sequential Thinking, and Time; archived servers (GitHub, Slack, PostgreSQL, etc.) now live in `servers-archived`.

## Primitives Servers Expose

Per the [python-sdk README](https://github.com/modelcontextprotocol/python-sdk): "With this SDK you can: Build MCP servers that expose tools, resources, and prompts to any MCP host; Build MCP clients that connect to any MCP server; Speak every standard transport: stdio, Streamable HTTP, and SSE." Tools are decorated with `@mcp.tool()`, resources with `@mcp.resource("greeting://{name}")`, and the SDK notes that "no JSON Schema (`a: int, b: int` *is* the schema), no request parsing, no validation code, no protocol handling—two type-hinted Python functions and a docstring." ([servers README](https://github.com/modelcontextprotocol/servers)) Official SDKs exist for TypeScript, Python, Java, Kotlin, C#, Go, Ruby, Rust, Swift, and PHP ([servers README](https://github.com/modelcontextprotocol/servers)).

## Transports

Per the [servers README](https://github.com/modelcontextprotocol/servers): "An MCP server talks to its client over a transport. The protocol defines two standard ones, and they run very differently."

| Transport | How the server runs | How a client reaches it |
| --- | --- | --- |
| **stdio** | Client launches the server as a subprocess; server opens no port | A `command` and `args` in client config |
| **Streamable HTTP** | Server runs as its own HTTP service, can serve many clients | A URL, e.g. `http://localhost:3001/mcp` |

Every server in the reference servers repo serves stdio; only `Everything` also serves Streamable HTTP (and covers the deprecated HTTP+SSE transport). ([servers README](https://github.com/modelcontextprotocol/servers)) The [python-sdk README](https://github.com/modelcontextprotocol/python-sdk) confirms support for stdio, Streamable HTTP, and SSE, and points to the [transport layer](https://modelcontextprotocol.io/docs/learn/architecture#transport-layer) and [spec Transports](https://modelcontextprotocol.io/specification/2026-07-28/basic/transports). AURL-based client means Streamable HTTP is the deploy transport; clients can also launch a local server as a stdio subprocess. ([python-sdk README](https://github.com/modelcontextprotocol/python-sdk))

## Ecosystem State: Spec Maturity & Adoption

- **Spec maturity**: The spec is versioned by date. Stable releases exist for [2024-11-05](https://github.com/modelcontextprotocol/modelcontextprotocol/releases/tag/2024-11-05), [2025-03-26](https://github.com/modelcontextprotocol/modelcontextprotocol/releases/tag/2025-03-26), [2025-06-18](https://github.com/modelcontextprotocol/modelcontextprotocol/releases/tag/2025-06-18), [2025-11-25](https://github.com/modelcontextprotocol/modelcontextprotocol/releases/tag/2025-11-25), and the current stable **[2026-07-28](https://github.com/modelcontextprotocol/modelcontextprotocol/releases/tag/2026-07-28)** ([releases page](https://github.com/modelcontextprotocol/modelcontextprotocol/releases)). The schema is defined in TypeScript first and available as JSON Schema ([spec repo README](https://github.com/modelcontextprotocol/modelcontextprotocol)).
- **Adoption breadth**: The [`servers` repo](https://github.com/modelcontextprotocol/servers) has ~92k stars and 12k forks; [`python-sdk`](https://github.com/modelcontextprotocol/python-sdk) (~25k stars); the spec repo (~9.7k stars). The [awesome-mcp-servers](https://github.com/wong2/awesome-mcp-servers) list has ~4.4k stars, indicating a large community server ecosystem. ([servers README](https://github.com/modelcontextprotocol/servers))
- **Governance & tooling**: Measurable CI exists ([Docs build](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/.github/workflows/docs-build.yml), [Docs validate](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/.github/workflows/docs-validate.yml), [Markdown Format Check](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/.github/workflows/markdown-format.yml), [Cut spec release](https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/.github/workflows/cut-release.yml)); an [MCP Registry](https://registry.modelcontextprotocol.io/) for browsing published servers; and the [MCP Inspector](https://github.com/modelcontextprotocol/inspector) for debugging. ([servers README](https://github.com/modelcontextprotocol/servers))
- **Caveats for our evaluation**: The servers README warns reference servers are "intended as reference implementations... not as production-ready solutions," and that "the prior version of the spec may remain in use for an undetermined amount of time" ([servers README](https://github.com/modelcontextprotocol/servers), [2025-06-18 release](https://github.com/modelcontextprotocol/modelcontextprotocol/releases/tag/2025-06-18)).

## Recommendation

Adopt MCP for internal AI tooling: stand up stdio-based internal servers alongside existing reference servers, lean on the ten official SDKs, and track the dated spec revisions (currently 2026-07-28) with version negotiation. Flag HTTP+SSE as deprecated and prefer Streamable HTTP for remote deployment.
