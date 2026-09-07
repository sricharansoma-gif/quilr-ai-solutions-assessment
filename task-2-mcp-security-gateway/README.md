# Task 2 — MCP Security Gateway

Source repository: https://github.com/sricharansoma-gif/mcp-security-gateway

Implements a TypeScript/Express security gateway in front of a downstream MCP service.

Highlights:
- Bearer-token authentication
- Viewer/admin role-based authorization
- JSON-RPC validation
- Unauthorized calls blocked before forwarding
- Authorization header not forwarded downstream
- Server-controlled downstream URL
- Sanitized downstream failures
- 100 KB body limit
- 19 automated security tests
- CI for tests, typecheck, and build

See the source repository for implementation, setup, and test instructions.
