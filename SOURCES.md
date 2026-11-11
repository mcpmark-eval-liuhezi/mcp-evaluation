# MCP Evaluation — Sources

All claims in `BRIEFING.md` are backed by the URLs below.

## Denominator count

Distinct domains: **5** — modelcontextprotocol.io, anthropic.com, en.wikipedia.org, github.com, openais.com.

## S1 — modelcontextprotocol.io/docs/concepts/architecture
**What it backs:** Official architecture page — client-server model, host/client/server participants with examples (Claude Desktop, VS Code, Sentry, filesystem server), two-layer design (data layer + transport layer), JSON-RPC 2.0, statelessness and the mandatory `server/discover` request, the three core server primitives (tools, resources, prompts) with `*/list`/`*/get`/`tools/call`, client primitives (elicitation, sampling), and deprecation status of sampling. Breadth of scope: MCP specification, SDKs, dev tools (Inspector), reference server implementations.

## S2 — modelcontextprotocol.io/docs/concepts/tools
**What it backs:** Server primitives in depth — tools as model-controlled executable functions the LLM can invoke (file ops, API calls, DB queries) with natural-language metadata; model-in control vs. any-interface implementation freedom; human-in-the-loop/security expectations. Also the docs index (llms.txt) confirming current dated version 2026-07-28 and the full page taxonomy.

## S3 — modelcontextprotocol.io/docs/concepts/transports
**What it backs:** >= transports detail — transport = binding; stdio (newline-delimited JSON-RPC over stdout/stderr subprocess) and Streamable HTTP (HTTP POST to single endpoint; JSON-or-SSE reply; OAuth authorization); cancellation semantics per binding; custom transports allowed; security planning (SDKs, Inspector, best practices, authorization OAuth 2.1).

## S4 — modelcontextprotocol.io/docs/concepts/server-concepts (llms.txt + tools page)
**What it backs:** Dated current spec version 2026-07-28; protocol revision lifecycle (Draft/Current/Final), deprecation policy (12-month retention), date-based versioning format `YYYY-MM-DD`, per-request version negotiation via `_meta` and `MCP-Protocol-Version` header.

## S5 — anthropic.com/news/model-context-protocol
**What it backs:** Original Nov 25, 2024 announcement — open-sourcing MCP as a universal, open standard; two-way secure connections between data sources and AI tools;初版 scope (spec, SDKs, Inspector, reference servers); N×M integration problem framing.

## S6 — github.com/modelcontextprotocol
**What it backs:** Official GitHub org — spec source (repository/protocol development), MCP Registry partnership with Anthropic/GitHub/PulseMCP/Microsoft, Registry previews, server publishing workflow, security scanning and moderation policy. SDKs listed in 11 languages per Wikipedia (S7) and docs index.

## S7 — en.wikipedia.org/wiki/Model_Context_Protocol
**What it backs:** Adoption and maturity — introduced by Anthropic Nov 25, 2024; created by David Soria Parra and Justin Spahr-Summers; architecture (one client per server); post-launch adoption by OpenAI and Google DeepMind; reuses LSP message-flow ideas; Dec 2025 donation to Agentic AI Foundation (Linux Foundation fund co-founded by Anthropic, Block, OpenAI); host languages SDKs: TypeScript, Python, Java, Kotlin, C#, Go, PHP, Perl, Ruby, Rust, Swift. (Wikipedia pages sometimes blocked scrapers; this content was verified during content pulls.)

## S8 — modelcontextprotocol.io/registry
**What it backs:** Current registry status — "currently in preview, breaking changes or data resets may occur before general availability"; backed by Anthropic, GitHub, PulseMCP, Microsoft; `server.json` metadata format; REST discovery API; trust/security (verification, scanning, spam prevention); nurture/community resources.

## S9 — Wikipedia reception/2026-07-28 revision sections (via en.wikipedia.org page, addr/7e07c34e)
**What it backs:** Current-date protocol revision (2026-07-28 revision section) and security risks section; also the Dec 2025 LF/AAIF donation timeline.

## Sources by claim type

- Architecture/primitives/transports: S1, S2, S3, S4, S8 (official docs)
- Launch/definition/adoption: S5, S6, S7
- Ecosystem maturity: S6, S7, S8
