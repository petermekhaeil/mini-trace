# 05: Inspect model and tool activity

**What to build:** Inspect received model and tool activity in a near-live session timeline and see useful performance and reliability summaries.

**Blocked by:** 01: Collect and display OpenCode sessions and tokens.

**Status:** ready-for-agent

- [ ] Show model and tool names, available durations and statuses, and safe structured error types or codes in a session view.
- [ ] Show dashboard summaries for supported operation durations, errors, and retries without implying unavailable telemetry exists.
- [ ] Refresh automatically using polling and show the latest update time; describe the view as near-live rather than guaranteeing in-progress spans.
- [ ] Exclude prompts, responses, reasoning, tool inputs and outputs, free-form error messages, paths, and unknown attributes throughout ingestion and display.
- [ ] Verify delayed, incomplete, failed, and repeated events with representative telemetry and privacy-safe fixtures.
