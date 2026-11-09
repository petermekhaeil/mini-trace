# 04: Display OpenCode-reported cost

**What to build:** See the cost supplied by OpenCode telemetry alongside usage without mistaking it for a bill or an independently calculated estimate.

**Blocked by:** 01: Collect and display OpenCode sessions and tokens.

**Status:** ready-for-agent

- [ ] Ingest and display supported OpenCode-reported cost metrics, clearly labeled as reported rather than billed spend.
- [ ] Distinguish explicit zero from missing or unavailable cost whenever the source preserves that distinction.
- [ ] Avoid duplicate cost totals when the same activity appears in overlapping or cumulative signals.
- [ ] Do not fetch a pricing catalog or calculate independent estimated costs.
- [ ] Verify nonzero, zero, absent, repeated, and cumulative values using representative plugin telemetry.
