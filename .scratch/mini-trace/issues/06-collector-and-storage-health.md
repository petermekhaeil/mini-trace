# 06: Show collector and storage health

**What to build:** Understand whether mini-trace is receiving data and how much local storage it uses, without adding deletion features.

**Blocked by:** 01: Collect and display OpenCode sessions and tokens.

**Status:** ready-for-agent

- [ ] Show the latest received update and useful collection or storage failure information without exposing raw payloads or sensitive content.
- [ ] Show database size and a warning when a configurable disk-use threshold is reached.
- [ ] Preserve data without automatic deletion; do not introduce retention or deletion controls.
- [ ] Verify failure reporting, size display, threshold behavior, and safe diagnostics.
