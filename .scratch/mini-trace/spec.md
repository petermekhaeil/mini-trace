# mini-trace: OpenCode observability

Status: ready-for-agent

## Problem Statement

I use OpenCode extensively across multiple projects and subagent sessions. I want to understand token usage, session activity, model and tool performance, errors, and reported cost without sending telemetry to a hosted observability product or operating a large monitoring stack. Existing tools demonstrate that this is possible, but I want a small standalone application I can build and control.

My existing OpenCode history contains hundreds of thousands of model steps and substantial subagent activity. The first release should collect new data reliably and make it useful without storing conversation content or building a search and memory product prematurely.

## Solution

Run mini-trace locally in Docker. OpenCode V2 sends telemetry through its existing OpenTelemetry plugin to mini-trace, which stores an allowlisted set of metadata in SQLite and serves a near-live web dashboard. The overview shows usage and activity across projects; session details show available model, tool, and subagent activity. The first working slice delivers session and token analytics end to end before adding the other agreed views.

## User Stories

1. As a developer, I want to run mini-trace in Docker, so that it fits my local tooling.
2. As a developer, I want one application container for the first release, so that I do not have to operate a separate collector or database service.
3. As a developer, I want the container to restart automatically, so that telemetry collection normally continues between machine restarts.
4. As a developer, I want to retain the database outside the container, so that restarting or replacing it does not erase my history.
5. As a developer, I want the collector and dashboard accessible only on the local machine by default, so that other machines cannot access my activity.
6. As a developer, I want to configure OpenCode V2's existing OTel plugin, so that I do not have to install a custom mini-trace plugin.
7. As a developer, I want clear setup and verification instructions, so that I can confirm telemetry is reaching mini-trace.
8. As a developer, I want mini-trace to collect new OpenCode activity, so that I can inspect it after setup.
9. As a developer, I want a dashboard covering all my OpenCode projects, so that I can see my overall usage.
10. As a developer, I want to filter by the project identity supplied by OpenCode, so that I can focus on a project without extra repository discovery.
11. As a developer, I want project identifiers preserved even if they lack friendly names, so that attribution does not rely on guessing.
12. As a developer, I want a seven-day overview by default, so that I can see recent activity immediately.
13. As a developer, I want today, 30-day, and custom date filters, so that I can compare the periods I care about.
14. As a developer, I want dates displayed in my local timezone with the timezone visible, so that I can interpret daily totals correctly.
15. As a developer, I want session and subagent counts, so that I can understand my agent activity.
16. As a developer, I want token usage by model, project, day, and session where attribution exists, so that I can see where usage occurs.
17. As a developer, I want separate token categories, including cached usage when reported, so that I do not confuse cache reads with unique input.
18. As a developer, I want a session tree total that includes known descendant sessions, so that I can understand the usage associated with a task.
19. As a developer, I want root-session usage shown separately from its session tree total, so that I can see the contribution of subagents.
20. As a developer, I want initially unattributed subagent usage linked later when reliable parentage becomes available, so that totals improve without guessing.
21. As a developer, I want uncorrelated usage in an unattributed group, so that overall totals do not silently omit received telemetry.
22. As a developer, I want to see OpenCode's reported cost when available, so that I can inspect the cost value it supplies without confusing it with a bill.
23. As a developer, I want absent cost distinguished from a reported zero when the source permits it, so that missing data is not presented as free usage.
24. As a developer, I want recent sessions on the overview, so that I can open relevant work quickly.
25. As a developer, I want session details showing model and tool names, so that I can understand what the agent did.
26. As a developer, I want identifiable subagent relationships in the session view, so that I can follow a session tree.
27. As a developer, I want operation durations and statuses when available, so that I can identify slow or failed work.
28. As a developer, I want error and retry summaries using safe structured metadata, so that I can investigate reliability without storing free-form error text.
29. As a developer, I want the dashboard to update automatically, so that I can see recently received telemetry without refreshing manually.
30. As a developer, I want a latest-update indicator, so that I understand that the dashboard is near-live rather than guaranteed to show every in-progress operation.
31. As a developer, I want charts to show zero received events in empty periods, so that they accurately represent telemetry received without claiming that no agent work occurred.
32. As a developer, I want prompts, responses, reasoning, and tool inputs and outputs excluded, so that conversation content is not stored in the first release.
33. As a developer, I want unknown attributes, filesystem paths, and raw payloads excluded by default, so that unexpected telemetry does not silently become retained data.
34. As a developer, I want database size visible, so that I can monitor storage growth.
35. As a developer, I want a configurable disk-use warning, so that growth is visible without automatic deletion.
36. As a developer, I want collection and storage failures surfaced without exposing payload contents, so that I can diagnose missing data safely.
37. As a developer, I want existing data to survive restarts, so that I can analyze sessions over time.
38. As a developer, I want the application to work without Sentry, Grafana, or another hosted backend, so that I own its operation and data.

## Implementation Decisions

- Build the application in TypeScript. Use TanStack Start and TanStack Query for the dashboard and shadcn/ui for interface components.
- Package the application as a Docker deployment with a persistent SQLite volume, automatic restart, and loopback-only published ports by default. The initial deployment assumes OpenCode and Docker are on the same machine.
- Receive OpenTelemetry directly over OTLP/HTTP; do not require a separate OpenTelemetry Collector. Determine which trace, metric, and log signals and encodings the agreed views actually require by examining real OpenCode V2 plugin output. Document supported endpoints and encodings rather than claiming general OTLP compatibility.
- Target OpenCode V2 and the compatible 2.x line of the existing OpenCode OTel plugin. Do not silently install the plugin or modify OpenCode configuration.
- Normalize received telemetry into session, model, tool, usage, and relationship concepts needed by the dashboard. Build the first end-to-end slice around sessions and token usage, then add performance, reliability, and the other agreed views.
- Use the project identifier provided by OpenCode telemetry. Do not scan Git repositories, infer shared worktree identities, or retain filesystem paths for attribution in the first release.
- Group a root session and confidently linked descendants as a session tree. Show root and tree usage separately. Handle late-arriving links and leave uncertain relationships unattributed rather than guessing.
- Treat metrics and spans as potentially overlapping, delayed, incomplete, repeated, or cumulative. Define source-specific aggregation and deduplication based on observed plugin behavior. Do not double-count token or cost values when the same activity appears in multiple signals.
- Display OpenCode's reported cost separately from tokens and label it as reported, not billed spend. Preserve explicit zero and missing cost as different values where the source permits; do not infer that zero proves a request was free. Do not independently calculate pricing or fetch a pricing catalog in this release.
- Store only explicitly allowlisted metadata required for the agreed views. Never retain raw incoming payloads, unrecognized attributes, conversation text, tool inputs or outputs, reasoning, filesystem paths, or free-form error messages. Show structured status and safe error type/code only. Treat even apparently structured values as untrusted: validate or reject values that cannot be safely retained under the allowlist.
- Do not build a scrubber or custom redaction rules in this release. The data-minimization policy must apply before persistence, logging, and dashboard responses; operational diagnostics must not reveal incoming payloads.
- Use a small storage interface based on actual application operations, with SQLite as its only initial implementation. Keep database-specific schema, migrations, transactions, indexing, and query details in that adapter. Do not implement PostgreSQL or a generic adapter plugin system yet.
- Make successful ingestion mean accepted data has been durably committed; handle storage failures explicitly and do not claim exporter-side replay guarantees.
- Use automatic dashboard refresh, starting with polling. Show the latest update time and label graphs as telemetry received; lack of received events is not proof that no agent activity occurred.
- Show database size and a configurable warning threshold without automatic deletion. Keep collected data until the user manages it outside the application; do not build deletion or retention features in this release.
- Build and benchmark against a representative dataset before claiming SQLite meets the target workload. Measure ingestion, storage growth, session and aggregate query latency, concurrent writes and reads, and restart recovery. The existing roughly 10 GB OpenCode database and roughly 211,000 historical steps provide scale context, not a guarantee that mini-trace will store the same volume.
- Keep the core deployment local and independent of third-party observability products. Teams are a future concern; a database interface alone does not provide authentication, tenant isolation, or secure remote access.

## Testing Decisions

- A good test checks externally observable behavior at the highest useful seam, rather than depending on private implementation details.
- Exercise the running application by sending representative OTLP telemetry and checking dashboard-facing query results, near-live updates, and persisted results after restart. Use fixtures captured or constructed from the actual OpenCode V2 plugin's supported signals.
- Verify session and token analytics first, then model/tool durations, structured failures, reported cost, and session-tree attribution as each feature is added.
- Test out-of-order events, uncertain and late parent links, duplicate delivery where identifiers allow it, cumulative metrics, overlapping signals, absent versus explicit-zero cost, and unattributed usage.
- Test the SQLite adapter against the small storage contract, including batch failure, migrations, concurrent reads/writes where supported, and restart recovery.
- Test the privacy boundary with synthetic secrets in prompts, tool content, paths, errors, resource attributes, and unexpected fields. Verify excluded values are absent from persisted data, logs, and dashboard-facing responses.
- Run an integration smoke test with a real compatible OpenCode V2 OTel plugin, covering a session, model activity, a tool call, and an identifiable subagent where feasible. Record the tested versions and actual signal coverage.
- Test Docker persistence, loopback-only published ports, dashboard polling, empty chart periods, storage-size display, and safe failure reporting.
- Benchmark a representative workload before setting performance claims. Use privacy-safe fixtures and document the hardware and workload.
- There is no existing application or test-suite prior art in this repository; establish the first tests alongside the initial vertical slice.

## Out of Scope

- Searchable conversation content, full-text search, MCP, and long-term memory.
- Importing sessions created before mini-trace was installed.
- Claude Code, Codex, mini-agent, OpenCode V1, and generic claims of first-class support for other agents.
- Prompt, response, reasoning, tool-input, tool-output, or raw OTLP retention.
- A scrubber, customizable secret patterns, or outbound secret blocking.
- An independent model-pricing catalog, automatic pricing updates, and calculated API-equivalent costs.
- Individual or bulk deletion, automatic retention, and a data-management interface.
- Remote access, multi-user accounts, teams, PostgreSQL, and cloud hosting.
- A separate OpenTelemetry Collector requirement, live push infrastructure, or guaranteed in-progress spans.
- mini-proxy and request interception.

## Further Notes

- OpenCode's OTel plugin exposes cost metrics, while the inspected Sentry OpenCode monitor emits token metrics without a documented cost metric. AI Observer uses the OpenCode plugin's native cost metrics rather than deriving additional OpenCode pricing.
- The app should not conflate received telemetry with all agent activity. An exporter or collector can be offline or delayed.
- Some exporter fields may already contain sensitive content even when a source's prompt logging is disabled. The receiver's allowlist is the first release's privacy boundary.
- Search, MCP, historical import, and additional agent integrations remain desired future work and should be designed when their actual data sources and privacy requirements are understood.
