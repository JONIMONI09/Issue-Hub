# vibeworks: get_code_graph returns `graph.errors.noFiles` despite working repo copy

Tracking issue: JONIMONI09/Issue-Hub#5 · Discovered 2026-09-16 · Affects MoinMornhart/vibeworks (upstream), code mirror JONIMONI09/vibeworks

## Evidence (single session, same token, same project "Harness" / JONIMONI09/Mobile-Harness)
| Time (UTC) | Call | Result |
|---|---|---|
| ~14:52 | `get_repo_status` | syncedAt 14:52:42, syncError null, failuresInRow 0 |
| after | `get_code_graph` | `graph.errors.noFiles`, files 0 (commit c7c21d8 shown) |
| after | `list_code_files` | **127 files**, refreshed copy at commit fffabe8 |
| seconds later | `get_code_graph` | again `graph.errors.noFiles`, files 0 — but commit ref now shows fffabe8 and memos still returned |

## Interpretation
- The copy fetch (used by `list_code_files`) and the graph index fetch are separate server paths. The copy fetch works on demand; the graph fetch fails even when the copy demonstrably exists seconds earlier.
- `get_code_graph` does read the repo metadata (branch/commit updated) and returns pinned memos, so the failure is isolated to loading the file tree for import analysis.
- Likely a one-time graph index build (triggered by the UI sync action) that either fails silently or is skipped in the API-only flow.

## Impact
- MCP-based anti-hallucination workflow degrades: import graph ("synapse map") unavailable; `search_code` + memos still fully functional (verified live).
- No data loss; memos intact.

## Reproduction
1. `list_code_files` (project) → succeeds.
2. `get_code_graph` (same project) → `graph.errors.noFiles`.
Reliable across repeated attempts, also right after a fresh sync.

## Suggested upstream fix direction
Have `get_code_graph` fall back to the same on-demand copy fetch used by `list_code_files` (or trigger the index build lazily on first graph call), and surface the real underlying error instead of `noFiles`.

## Status
- Upstream report: pending (duplicate check first)
- Watchlist: README entry added 2026-09-16
