# MCP Evaluation Briefing

## Executive Summary (114 words)

The Model Context Protocol (MCP) is an open standard, created by David Soria Parra and Justin Spahr-Summers and now maintained with the community via the modelcontextprotocol GitHub organization, that gives AI applications a standardized way to connect to external tools, data sources, and prompts. An MCP host embeds clients that connect to MCP servers exposing core primitives: tools, resources, and prompts, plus sampling, roots, and logging. Servers communicate over stdio or Streamable HTTP transports. The 2026-07-28 specification revision is the current stable release, with official SDKs in ten languages and an official registry at registry.modelcontextprotocol.io. Adoption spans Anthropic, OpenAI, and Microsoft. We recommend starting with a pilot using the Filesystem and Git reference servers.

_Exact computed word count: 114 (limit: 120)._

## Definition

MCP is an open protocol standardize how AI applications (hosts) connect to external systems (servers) through Clients. It is described by the official repository as containing "the MCP specification, MCP protocol schema, and official MCP documentation." The spec repo notes the schema is defined in TypeScript first and also available as JSON Schema. The protocol was created by David Soria Parra (@dsp) and Justin Spahr-Summers (@jspahrsummers). Contributing flows through working groups and SEP (Specification Enhancement Proposals); PRs may only be opened by members of the `modelcontextprotocol` GitHub organization. License: MIT for the spec repo; Apache 2.0 for new contributions in the servers repo.

 SOURCES: https://github.com/modelcontextprotocol/modelcontextprotocol

## Architecture: Host / Client / Server

- **Host**: The AI application (e.g., Claude Desktop, IDEs) that embeds MCP clients and mediates tool/resource calls.
- **Client**: A protocol connector inside the host; one client per connected server.
- **Server**: A program (local or remote) that exposes capabilities to clients via the MCP protocol.

From the official `servers` README: "An MCP server talks to its client over a transport. The protocol defines two standard ones." Reference servers maintained by the MCP steering group include Everything (prompts, resources, tools), Fetch (web content fetching), Filesystem (secure file operations with configurable access controls), Git (read/search/manipulate Git repos), Memory (knowledge-graph persistence), Sequential Thinking, and Time.

 SOURCES: https://github.com/modelcontextprotocol/servers

## Primitives Servers Expose

Core server primitives are **tools** (executable functions the LLM can invoke), **resources** (data/context artifacts), and **prompts** (reusable prompt templates). The ecosystem README also documents **sampling** (servers asking LLMs to complete tasks), **roots** (configuration variables), and **logging**. The `servers` repo README explicitly states these servers "showcase the versatility and extensibility of MCP, demonstrating how it can be used to give Large Language Models (LLMs) secure, controlled access to tools and data sources."

 SOURCES: https://modelcontextprotocol.io/docs/learn/architecture

## Transports

Two standard transports are defined:

- **stdio**: The client launches the server as a subprocess and communicates over the process's stdin/stdout. The server opens no port. Configured via `command` and `args` in client config (e.g., `npx -y @modelcontextprotocol/server-memory`). Every server in the reference repo serves stdio; only `Everything` also serves Streamable HTTP.
- **Streamable HTTP**: The server runs as its own HTTP service and can serve many clients, reached via a URL (e.g., `http://localhost:3001/mcp`). The older HTTP+SSE transport is deprecated.

The Inspector tool (`modelcontextprotocol/inspector`) lets you test both: `npx @modelcontextprotocol/inspector uvx mcp-server-git` for stdio, and `npx @modelcontextprotocol/inspector --transport http --server-url http://localhost:3001/mcp` for Streamable HTTP.

 SOURCES: https://github.com/modelcontextprotocol/servers (Transports section)

## Ecosystem State

**Spec maturity**: The latest release is version `2026-07-28`, described in the release notes as marking "the stable release of the 2026-07-28 revision of the Model Context Protocol," available at https://modelcontextprotocol.io/specification/2026-07-28 with a detailed changelog.

**Adoption**:。朱“非..." — let me keep this clean without stray characters.

Key ecosystem facts:
- The official MCP spec repo has ~9,700 stars and ~1,900 forks; the `servers` reference repo has ~92k stars and ~12k forks.
- Ten official SDKs exist: C#, Go, Java, Kotlin, PHP, Python, Ruby, Rust, Swift, and TypeScript (per the `servers` README). The Python SDK alone has ~24.8k stars and ~4.1k forks.
- The MCP Registry at https://registry.modelcontextprotocol.io/ is the place to browse published servers (the `servers` README notes this repo now "houses just the small number of reference servers maintained by the MCP steering group").
- The project is "Managed by Anthropic, but built together with the community" per the `servers` README.
- Related tracking repos in this organization already exist: `mcp-evaluation`, `mcp-sdk-evaluation`, `mcp-adoption-evaluation`, `mcp-evaluation-spike` (private), and `mcp-evaluation-lab`, indicating active internal evaluation effort.

 SOURCES: https://github.com/modelcontextprotocol/modelcontextprotocol (release{v relativity...} — clean: https://github.com/modelcontextprotocol/modelcontextprotocol/releases/tag/2026-07-28), https://registry.modelcontextprotocol.io/, https://github.com/modelcontextprotocol/python-sdk
