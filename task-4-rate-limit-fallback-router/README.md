# Task 4 — LLM Rate-Limit & Fallback Router

Source repository: https://github.com/sricharansoma-gif/llm-rate-limit-fallback-router

Implements a resilient TypeScript/Express LLM gateway with token-aware sliding-window rate limiting and provider fallback.

Highlights:
- 50,000-token rolling 60-second per-tenant limit
- SQLite WAL persistence
- Atomic `BEGIN IMMEDIATE` reservations for concurrency safety
- SHA-256 hashing of tenant API keys before persistence
- Primary-provider fallback on HTTP 429 or 3-second timeout
- Late primary completion cannot overwrite fallback output
- Standardized sanitized public errors
- 45 automated tests
- CI for tests, typecheck, and build

See the source repository for implementation, setup, and test instructions.
