# Task 3 — LLM Streaming Guardrail

Source repository: https://github.com/sricharansoma-gif/llm-streaming-guardrail

Implements a TypeScript/Express streaming gateway that redacts PII before LLM output reaches the client.

Highlights:
- Email, U.S. SSN, and credit-card-like pattern detection
- Rolling holdback buffer for PII split across chunks
- Redaction before client delivery
- Injectable provider for HTTP integration testing
- Sanitized provider failures
- 17 automated unit/integration tests
- CI for tests, typecheck, and build

See the source repository for implementation, setup, and test instructions.
