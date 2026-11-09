# 01: Collect and display OpenCode sessions and tokens

**What to build:** Run mini-trace locally in Docker, receive OpenCode V2 telemetry, and show recent sessions and token totals on a basic dashboard. This is the first end-to-end slice and establishes the metadata-only privacy boundary.

**Blocked by:** None (can start immediately).

**Status:** ready-for-agent

- [ ] Start one application container with persistent SQLite storage, automatic restart, and ports published to loopback by default.
- [ ] Receive the OTLP/HTTP signals and encodings needed for session and token usage from the compatible OpenCode V2 plugin; document what is supported.
- [ ] Show received sessions and available token totals in a TanStack Start dashboard using TanStack Query and shadcn/ui.
- [ ] Apply an explicit metadata allowlist before storage, logs, and dashboard responses; exclude raw payloads, conversation and tool content, paths, free-form errors, and unrecognized attributes.
- [ ] Put SQLite behind a small storage interface and preserve data across application and container restarts.
- [ ] Demonstrate the behavior with representative plugin telemetry, including privacy tests with synthetic secrets and a restart test.
- [ ] Document setup without silently installing the plugin or changing OpenCode configuration.
