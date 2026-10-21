# MCP Evaluation — Briefing

**Date:** 2026-10-21 · **Prepared by:** HeziLiu · **Status:** Draft

## Executive Summary

> MCP is an open protocol, introduced by Anthropic in November 2024, that standardizes how AI applications connect to external systems. A host application such as Claude Desktop, VS Code, or ChatGPT runs one MCP client per server; servers expose tools, resources, and prompts, while clients can offer sampling, roots, and elicitation. Messages are JSON-RPC 2.0 over two transports: stdio for local servers, Streamable HTTP for remote servers, with HTTP+SSE deprecated in 2025. The current 2026-07-28 revision made the protocol stateless and added an extensions framework. Adoption is real: OpenAI, Google, and Microsoft ship support, with over 10,000 active servers and 97M+ monthly SDK downloads. Governance now sits with the Linux Foundation's Agentic AI Foundation. Security review remains essential.

*Word count: 118 (machine-computed; hard limit 120).*

---

## 1. What MCP is

MCP (Model Context Protocol) is an open-source standard for connecting AI applications to external systems — data sources (local files, databases), tools (search engines, calculators), and workflows ([official docs](https://modelcontextprotocol.io/docs/2026-07-28/getting-started/intro)). Anthropic introduced and open-sourced it on November 25, 2024, positioning it as an open standard for connecting AI assistants to "the systems where data lives" ([Anthropic announcement](https://www.anthropic.com/news/model-context-protocol)); Wikipedia records the launch with partners Block and Apollo plus IDE vendors Zed, Replit, Codeium, and Sourcegraph ([Wikipedia](https://en.wikipedia.org/wiki/Model_Context_Protocol)). The stated aim is to replace the N×M problem — one bespoke connector per model–data-source pair — with a single universal protocol, commonly described as "USB-C for AI applications" ([official docs](https://modelcontextprotocol.io/docs/2026-07-28/getting-started/intro); [Wikipedia](https://en.wikipedia.org/wiki/Model_Context_Protocol)).

## 2. Architecture: hosts, clients, servers

MCP is client-server. An **MCP host** — the AI application, e.g. Claude Code, Claude Desktop, VS Code — establishes connections to one or more MCP servers, instantiating **one MCP client per server**, each holding a dedicated 1:1 connection ([architecture overview](https://modelcontextprotocol.io/docs/2026-07-28/learn/architecture)). Typically, client and host run on the same machine while servers may be local or remote; local stdio servers generally serve a single client, remote Streamable HTTP servers serve many ([architecture overview](https://modelcontextprotocol.io/docs/2026-07-28/learn/architecture); [Wikipedia](https://en.wikipedia.org/wiki/Model_Context_Protocol)).

The protocol has two layers: a **data layer** (JSON-RPC message protocol, capability/version discovery, core primitives, notifications) and a **transport layer** (communication mechanisms, connection establishment, message framing, authorization) ([architecture overview](https://modelcontextprotocol.io/docs/2026-07-28/learn/architecture)).

## 3. Primitives: what servers and clients expose

**Servers offer three features to clients** ([2025-11-25 specification](https://modelcontextprotocol.io/specification/2025-11-25)):

- **Tools** — functions for the AI model to execute (model-controlled)
- **Resources** — context and data for the user or model to use (application-controlled)
- **Prompts** — templated messages and workflows for users (user-controlled)

**Clients may offer three features to servers:**

- **Sampling** — server-initiated LLM completions through the client's model (deprecated as of 2026-07-28 in favor of direct LLM-provider-API integration) ([architecture overview](https://modelcontextprotocol.io/docs/2026-07-28/learn/architecture))
- **Roots** — filesystem/URI boundaries for the server to operate within (also deprecated in 2026-07-28)
- **Elicitation** — server-initiated requests for additional structured input from the user, added in the 2025-06-18 revision ([2025-03-26 changelog](https://spec.modelcontextprotocol.io/specification/2025-03-26/changelog/); [release notes summary](https://www.speakeasy.com/mcp/release-notes))

## 4. Transports

Two transports are current ([spec changelog](https://modelcontextprotocol.io/specification/2026-07-28/basic/transports/streamable-http); [architecture overview](https://modelcontextprotocol.io/docs/2026-07-28/learn/architecture)):

- **stdio** — local servers spawned as subprocesses, typically with the user's privileges, usually serving a single client.
- **Streamable HTTP** — remote servers over the network, introduced in 2025-03-26, serving many clients; sessions were managed via the `Mcp-Session-Id` header in 2025-03-26 through 2025-11-25.

History: the original 2024-11-05 spec shipped stdio + HTTP+SSE; 2025-03-26 replaced HTTP+SSE with Streamable HTTP, which is now formally **deprecated** under the feature-lifecycle policy (SEP-2596) and should not be adopted by new implementations ([Streamable HTTP spec](https://modelcontextprotocol.io/specification/2026-07-28/basic/transports/streamable-http)). The 2026-07-28 revision reshaped Streamable HTTP again — removing the GET stream endpoint and protocol-level sessions ([Streamable HTTP spec](https://modelcontextprotocol.io/specification/2026-07-28/basic/transports/streamable-http)). Remote servers can use OAuth 2.1 to authorize clients ([2025-03-26 changelog](https://spec.modelcontextprotocol.io/specification/2025-03-26/changelog/)).

## 5. Specification maturity

The spec has shipped dated revisions, tracked publicly ([version timeline](https://hidekazu-konishi.com/entry/mcp_specification_version_timeline.html)):

| Version | Headline changes |
|---|---|
| 2024-11-05 | Initial: client-server model, JSON-RPC 2.0, tools/resources/prompts, stdio + HTTP+SSE |
| 2025-03-26 | OAuth 2.1 authorization, Streamable HTTP replaces HTTP+SSE, tool annotations, audio content, JSON-RPC batching |
| 2025-06-18 | Structured tool output, elicitation, resource links; servers as OAuth Resource Servers with RFC 8707 Resource Indicators; batching removed |
| 2025-11-25 | OpenID Connect Discovery, icons metadata, OAuth Client ID metadata docs, experimental Tasks, JSON Schema 2020-12 |
| 2026-07-28 (current) | Stateless core (initialize handshake removed; version/capabilities in `_meta`; `server/discover`), extensions framework, MCP Apps, Tasks extension, deprecation policy |

Sources: [version timeline](https://hidekazu-konishi.com/entry/mcp_specification_version_timeline.html), [2025-03-26 changelog](https://spec.modelcontextprotocol.io/specification/2025-03-26/changelog/), [release notes summary](https://www.speakeasy.com/mcp/release-notes), [2026-07-28 changelog](https://modelcontextprotocol.io/specification/2026-07-28/changelog), [official 2026-07-28 post](https://blog.modelcontextprotocol.io/posts/2026-07-28/).

The 2026-07-28 revision also formalized a feature lifecycle (Active → Deprecated → Removed, ≥12 months notice) and a Tier-based SDK system scored against a conformance suite; Dynamic Client Registration is deprecated in favor of CIMD ([official 2026-07-28 post](https://blog.modelcontextprotocol.io/posts/2026-07-28/)).

## 6. Ecosystem state

**Adoption.** OpenAI adopted MCP in March 2025 across the Agents SDK, Responses API, and ChatGPT ("People love MCP and we are excited to add support across our products" — Sam Altman, March 26, 2025); Google DeepMind's Demis Hassabis confirmed Gemini support on April 9, 2025; Microsoft deepened commitment mid-April 2025 ([winbuzzer report](https://winbuzzer.com/2025/04/22/openai-adopts-rival-anthropics-mcp-standard-joining-industry-push-for-ai-interoperability-xcxwbn/)). First-class client support spans Claude, ChatGPT, Cursor, Gemini, Microsoft Copilot, and VS Code ([MCP blog](https://blog.modelcontextprotocol.io/posts/2025-12-09-mcp-joins-agentic-ai-foundation/)).

**Scale.** Per the MCP maintainers (December 2025): **97M+ monthly SDK downloads** and **10,000+ active servers** ([MCP blog](https://blog.modelcontextprotocol.io/posts/2025-12-09-mcp-joins-agentic-ai-foundation/); [Anthropic](https://www.anthropic.com/news/donating-the-model-context-protocol-and-establishing-of-the-agentic-ai-foundation)). Independent tracking corroborates: ~17,000 public servers across registries by Q1 2026 ([datawalk.com](https://datawalk.com/what-is-mcp-the-model-context-protocol-explained/)); directory counts vary widely by methodology — official registry ~9,652 records, Smithery ~7,000, mcp.so ~20,222, PulseMCP 21,650+ ([ecosystem survey](https://medium.com/@chierhu/the-mcp-server-ecosystem-a-rigorous-survey-for-builders-cbb15b98cd1e); [registry comparison](https://www.truefoundry.com/blog/best-mcp-registries); [PulseMCP](https://www.pulsemcp.com/servers)).

**SDKs.** Official SDKs span TypeScript, Python, Java, Kotlin, C#, Go, PHP, Ruby, Rust, and Swift, tiered by feature completeness and protocol support ([GitHub org](https://github.com/modelcontextprotocol); [SDK docs](https://modelcontextprotocol.io/docs/sdk)). Reference server implementations exist as educational examples, with published servers indexed in the MCP Registry ([modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers)).

**Governance.** On December 9, 2025, Anthropic donated MCP to the **Agentic AI Foundation (AAIF)**, a directed fund under the Linux Foundation co-founded by Anthropic, Block, and OpenAI, with support from Google, Microsoft, AWS, Cloudflare, and Bloomberg; MCP joined goose (Block) and AGENTS.md (OpenAI) as founding projects; governance and maintainer autonomy are unchanged ([Linux Foundation press release](https://www.linuxfoundation.org/press/linux-foundation-announces-the-formation-of-the-agentic-ai-foundation); [MCP blog](https://blog.modelcontextprotocol.io/posts/2025-12-09-mcp-joins-agentic-ai-foundation/); [Anthropic](https://www.anthropic.com/news/donating-the-model-context-protocol-and-establishing-of-the-agentic-ai-foundation)).

## 7. Security posture

MCP servers are untrusted by design and are the primary attack vector. Documented threats include indirect prompt injection via tool metadata ("tool poisoning"), rug-pull attacks (tool definitions amended after approval), and exploitation of the sampling feature for token theft and conversation hijacking ([Unit 42](https://unit42.paloaltonetworks.com/model-context-protocol-attack-vectors/); [arXiv threat model](https://arxiv.org/html/2603.22489v1)). Mitigations: server allowlisting, human-in-the-loop authorization for tool calls, tool-description inspection, and OAuth flow hardening ([arXiv threat model](https://arxiv.org/html/2603.22489v1)).

## 8. Implications for internal tooling

1. **Maturity is adequate for pilot work**, but the spec moved three times in 18 months — pin a spec version and track the SEP process before committing to production.
2. **Map our components now**: our AI applications become hosts; candidate services become servers; local-first (stdio) for internal tools, Streamable HTTP for shared services.
3. **Reuse before build**: a large server ecosystem exists, but treat it as untrusted third-party code.
4. **Security review is a gate, not an afterthought** — see the open security tracking issue in this repository.

---

*Full source list with per-URL annotations: see [SOURCES.md](SOURCES.md). 24 sources across 16 distinct domains (both figures machine-computed).*
