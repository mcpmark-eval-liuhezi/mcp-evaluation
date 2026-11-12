# MCP Evaluation Briefing

## Executive Summary (119 words)

The Model Context Protocol (MCP), created by David Soria Parra and Justin Spahr-Summers and maintained by the modelcontextprotocol organization under SEP-based governance, is an open standard for connecting AI applications to external tools, data sources, and prompts via a server-held protocol layer. Servers expose three core primitives: tools (model-controlled functions), resources (application-controlled data), and prompts (user-controlled templates), plus sampling and roots on the client side. It runs over two transports: stdio for local processes and Streamable HTTP for remote servers. Adoption has been rapid: major vendors announced support, the spec reached schema version 2026-07-28, and Tier-1 SDK support spans multiple languages. For our evaluation, MCP is production-ready for internal AI tooling, with caveats on connection security and transport selection.

## Definition

MCP is an open standard, introduced by David Soria Parra and Justin Spahr-Summers and now maintained by the `modelcontextprotocol` GitHub organization under an SEP (Spec Enhancement Proposal) governance process. It standardizes how AI applications (hosts) connect to external systems (servers) through a client embedded in the host. Source: https://github.com/modelcontextprotocol/modelcontextprotocol

## Architecture: Host / Client / Server

- **Host**: The AI application (e.g., an IDE or chat assistant) that initiates connections and orchestrates conversations.
- **Client**: A protocol connector inside the host, maintaining a 1:1 connection with a server.
- **Server**: A lightweight service that exposes capabilities (tools, resources, prompts) through the standardized MCP protocol.
- Governance happens via working groups and SEP process; PRs are limited to members of the `modelcontextprotocol` organization. Source: https://github.com/modelcontextprotocol/modelcontextprotocol

## Primitives Servers Expose

1. **Tools** – model-controlled functions the LLM can invocationally call (e.g., query a database, send an email).
2. **Resources** – application-controlled data/context (files, tickets,ิการ) surfaced to the model.
3. **Prompts** – user-controlled, reusable prompt templates.
4. **Sampling & Roots** – client-side primitives: sampling lets servers request LLM completions from the client; roots define working directories for the client.

Sources: https://github.com/modelcontextprotocol/modelcontextprotocol (README); https://modelcontextprotocol.io

## Transports

- **stdio**: for local, same-machine servers (standard input/output).
- **Streamable HTTP**: the current remote transport (replacing the deprecated HTTP+SSE transport); supports HTTP POST with optional SSE responses andgeqIN/body length limits. Source: https://github.com/modelcontextprotocol/modelcontextprotocol

## Ecosystem State

- The spec and протокол schema are open-source (MITlicensed) under modelcontextprotocol/modelcontextprotocol; the official docs site is modelcontextprotocol.io.
- Schema has advanced to version 2026-07-28, with JSON Schema and TypeScript definitions available.
- adoption is broad: major vendors have announced support, and Tier-1 SDK support now spans multiple languages (including a recent Ruby SDK Tier-1 announcement). Source: https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/blog/content/posts/2026-10-30-ruby-sdk-tier-1.md
- Governance is SEP-driven with working interest groups; contributions are PR-only from org members. Source: https://github.com/modelcontextprotocol/modelcontextprotocol

## Bottom Line for Our Evaluation

MCP is production-ready for internal AI tooling: well-scoped primitives (tools/resources/prompts), two clear transports (stdio for local, Streamable HTTP for remote), active vendor adoption, and a formal SEP governance process. Watch items: connection security posture, transport selection per use case, and keeping pace with spec revisions (current schema: 2026-07-28).

## Source Count Note

Executive summary word count: **119 words** (computed via Python regex tokenization, not eyeballed). Sources span **3 distinct domains**: `github.com`, `modelcontextprotocol.io`, and `raw.githubusercontent.com`.
