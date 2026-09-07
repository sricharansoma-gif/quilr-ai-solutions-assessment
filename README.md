# Quilr AI Solutions Engineer Assessment

This repository is an index for four independently runnable TypeScript projects covering MCP tooling, gateway security, streaming data protection, and resilient LLM routing.

Each task is maintained as a separate repository so it can be reviewed, installed, tested, and run independently while preserving a focused project history.

## Assessment Projects

| Task | Project | Focus | Verification |
| --- | --- | --- | --- |
| 1 | [Customer Refund MCP Server](https://github.com/sricharansoma-gif/customer-refund-mcp) | MCP tools, Zod input validation, structured tool results | 11 automated tests + typecheck + build |
| 2 | [MCP Security Gateway](https://github.com/sricharansoma-gif/mcp-security-gateway) | Bearer authentication, RBAC, JSON-RPC validation, secure downstream proxying | 19 automated security tests + typecheck + build |
| 3 | [LLM Streaming Guardrail](https://github.com/sricharansoma-gif/llm-streaming-guardrail) | Streaming PII redaction across chunk boundaries | 17 automated unit/integration tests + typecheck + build |
| 4 | [LLM Rate-Limit & Fallback Router](https://github.com/sricharansoma-gif/llm-rate-limit-fallback-router) | Token-aware sliding-window limiting, SQLite concurrency, provider timeout/fallback | 45 automated tests + typecheck + build |

## Architecture Overview

The four tasks represent complementary controls in an enterprise AI platform:

```text
AI / MCP Client
      |
      v
MCP Security Gateway
  - authentication
  - role-based authorization
  - JSON-RPC validation
      |
      v
MCP Business Tools
  - validated tool inputs
  - controlled customer/refund operations

LLM Request
      |
      v
Rate-Limit & Fallback Router
  - per-tenant token accounting
  - atomic SQLite reservations
  - primary provider timeout / 429 fallback
      |
      v
Streaming PII Guardrail
  - cross-chunk detection
  - redaction before client delivery
      |
      v
Client
```

## Task 1 — Customer Refund MCP Server

Implements an MCP server over stdio using the Model Context Protocol SDK. It exposes `get_customer_record` and `trigger_refund` tools with Zod validation and structured business errors.

Key decisions:
- Strict `CUST-XXXXX` customer ID validation.
- Positive refund amounts and bounded, trimmed reasons.
- Synthetic customer data and mock refund processing only.
- MCP protocol traffic remains on stdout; diagnostic output uses stderr.
- Tool handlers are separated from server registration so business behavior can be tested directly.

## Task 2 — MCP Security Gateway

Implements an Express gateway in front of a downstream MCP service.

Key controls:
- Bearer-token authentication with viewer/admin roles for the assessment.
- Role-based authorization before forwarding tool calls.
- `admin_*` tools restricted to the admin role.
- JSON-RPC request validation.
- 100 KB request body limit.
- Server-controlled downstream URL to reduce SSRF risk.
- Client bearer credentials are not forwarded downstream.
- Downstream failures are sanitized before returning to clients.

## Task 3 — LLM Streaming Guardrail

Implements a streaming gateway that redacts PII before LLM output reaches the client.

PII coverage includes:
- Email addresses.
- U.S. Social Security numbers in `XXX-XX-XXXX` form.
- Credit-card-like digit sequences.

A rolling holdback buffer provides enough context to detect sensitive values split across provider chunks. HTTP integration tests verify that unredacted PII is not released to the client even when a sensitive value spans multiple chunks.

## Task 4 — LLM Rate-Limit & Fallback Router

Implements a resilient LLM gateway with a persistent token-aware sliding-window rate limiter and provider fallback.

Key decisions:
- Default capacity of 50,000 tokens per tenant in a rolling 60-second window.
- SQLite WAL persistence for the single-node assessment implementation.
- Atomic `BEGIN IMMEDIATE` reservations prevent concurrent requests from bypassing capacity checks.
- Tenant API keys are SHA-256 hashed before persistence.
- Primary provider fallback occurs on HTTP 429 or a 3-second timeout.
- Timed-out primary requests are cancelled with `AbortController` before fallback completes.
- Late primary completion cannot overwrite a fallback result.
- Public gateway errors are standardized and sanitized.

For a horizontally scaled production deployment, rate-limit state would move to a centralized system such as Redis, and token usage would be calculated server-side rather than trusted from clients.

## Running the Projects

Each linked project contains its own `README.md`, `package.json`, lockfile, tests, and configuration examples.

A typical verification flow inside each repository is:

```bash
npm ci
npm test
npm run typecheck
npm run build
```

Task 1 also supports MCP stdio execution, while Tasks 2–4 expose local HTTP services as described in their individual READMEs.

## Security Notes

The assessment uses synthetic data and demonstration credentials only. No real customer records, payment operations, provider API keys, or external production systems are required. Local environment files and generated artifacts are excluded from source control where applicable.

GitHub Actions CI is configured on the individual repositories with read-only repository-content permissions and verifies tests, TypeScript correctness, and builds on pull requests and pushes to `main`.
