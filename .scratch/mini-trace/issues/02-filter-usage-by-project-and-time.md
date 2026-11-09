# 02: Filter usage across projects and time

**What to build:** Explore token usage across OpenCode projects and models over useful time ranges, including data that cannot be reliably attributed.

**Blocked by:** 01: Collect and display OpenCode sessions and tokens.

**Status:** ready-for-agent

- [ ] Default to the last seven days and support today, 30 days, and a custom date range, displaying the local timezone.
- [ ] Filter usage by the OpenCode-provided project identifier and model without Git discovery or filesystem-path retention.
- [ ] Display available token categories separately, including cache usage when reported.
- [ ] Include uncorrelated usage in an explicit unattributed group without assigning a guessed project or session.
- [ ] Label time-series charts as telemetry received and show zero for periods with no received events.
- [ ] Verify filtering, time boundaries, missing attribution, and aggregation using representative telemetry, avoiding double counting overlapping or cumulative signals.
