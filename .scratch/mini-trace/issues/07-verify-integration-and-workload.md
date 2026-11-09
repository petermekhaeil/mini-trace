# 07: Verify the real integration and workload

**What to build:** Establish confidence that the completed first release works with the real OpenCode V2 plugin and remains useful under a representative workload.

**Blocked by:** 02: Filter usage across projects and time; 03: Show session trees and subagent usage; 04: Display OpenCode-reported cost; 05: Inspect model and tool activity; 06: Show collector and storage health.

**Status:** ready-for-agent

- [ ] Run a real OpenCode V2 session through the compatible OTel plugin, covering model activity, a tool call, and an identifiable subagent where feasible.
- [ ] Verify collection, dashboard results, metadata-only privacy, persistence after restart, and documented coverage limitations.
- [ ] Record tested OpenCode and plugin versions, supported OTLP signals and encodings, and reproducible setup and troubleshooting steps.
- [ ] Benchmark privacy-safe representative data for ingestion, database size, session and aggregate queries, concurrent reads/writes, and restart recovery.
- [ ] Document workload and hardware with measured results; avoid unsupported performance or billing claims.
