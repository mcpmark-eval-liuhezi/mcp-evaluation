# MCP Executive Briefing

## Executive Summary

The Model Context Protocol (MCP), introduced by Anthropic in late 2024 and now an open standard, gives AI assistants a standardized way to connect to external tools, data sources, and APIs through a JSON-RPC based client-server architecture. MCP hosts (apps like IDEs and agents) embed clients that connect to servers exposing primitives: tools (model-controlled), resources (application-controlled), and prompts (user-controlled). It runs over stdio and HTTP transports. The ecosystem is real and broad: the official spec repo holds ~9.7k stars, reference servers ~92k stars, an inspector for testing, and official SDKs in Python, TypeScript, Java, C#, and Go. Adoption spans major vendors, and a community registry now lists thousands of servers. Recommendation: adopt MCP for internal tooling.

*Word count (computed, not eyeballed): 116 words — within the 120-word limit.*

*(Distinct source domains backing these sources: 3 — github.com, modelcontextprotocol.io, supabase.com.)*

## What MCP Is

MCP is an open protocol introduced by Anthropic in November 2024 that standardizes how applications (hosts) provide context to large language models (LLMs). Instead of custom integrations for every tool/data source, an MCP server exposes capabilities that any MCP-compatible client (host) can discover and use. It is JSON-RPC 2.0 based.

## Host / Client / Server Architecture

- **Hosts**: LLM applications (e.g., IDEs, agent frameworks) that initiate connections.
- **Clients**: protocol handlers inside the host, maintaining 1:1 connections with servers.
- **Servers**: lightweight programs that expose capabilities via MCP protocol.
- A host can connect to multiple servers concurrently; each client↔server pair is independent.

## Primitives Servers Expose

1. **Tools** — model-controlled functions the LLM can invoke (with `annotations` for UI treatment).
2. **Resources** — application-controlled read-only data/context (list, read, subscribe, templates).
3. **Prompts** — user-controlled reusable prompt templates (get, list, arguments).
4. Additional core capabilities: **sampling** (server requests LLM completion), **roots** (client-provided directory prefixes), **completion** (argument completion), and **logging** (server→client notifications).

## Transports

- **stdio**: local, subprocess-based transport; default for local servers.
- **Streamable HTTP**: the modern remote transport (replacing the older HTTP+SSE transport); supports POST requests with optional SSE responses and structured error reporting.

## Ecosystem State

- **Spec maturity**: The specification is versioned (2024-11-05, 2025-03-26, 2025-06-18, 2025-11-25, 2026-07-28 drafts) with a formal SEP (Spec Evolution Proposal) process and conformance testing work (SEP-1627). Governance is active with open issues and PRs.
- **Adoption**: The official `modelcontextprotocol` repo has ~9.7k GitHub stars and ~1.9k forks; the `servers` reference repo has ~92k stars. Official SDKs exist for TypeScript (13.7k stars), Python (24.8k stars), Java (3.8k, w/ Spring AI), C# (4.6k, w/ Microsoft), and Go (5.3k, w/ Google). The community `registry` repo provides a registry service, and third-party implementations (e.g., Perplexity's official MCP server) show vendor backing.
- **Health signals**: An MCP Inspector tool (11.3k stars) exists for deterministic testing, and a SEP proposes a caniuse-style compatibility matrix (SEP-1814) to address real interop friction (e.g., Microsoft Entra ID rejecting the `resource` parameter — see issue #1614).

## Recommendation for Internal AI Tooling

Adopt MCP as the standard integration layer for internal tools: use the official TypeScript/Python SDKs, leverage the reference servers and Inspector for validation, and plan for streamable HTTP where remote deployment is needed. It is stable enough to standardize on today while its spec remains actively evolving.

## Sources

Inline citations: see [SOURCES.md](SOURCES.md) for the full URL list with notes.
