# Issue Hub

Central issue-tracking hub for JONIMONI09. This repo tracks issues for repositories where issues are **not wanted or disabled** (release forks, mirrors) and keeps a **watchlist for upstream projects**.

## What lives here
| Area | Purpose |
|---|---|
| **Issues** | Tracking issues for repos that have their own issues disabled, and mirrors of upstream findings (upstream link always included) |
| **docs/** | Cross-project incident analyses that belong to no single code repo (e.g. `docs/vibeworks-mcp-401-analysis.md`) |
| `.github/ISSUE_TEMPLATE/` | Minimal template for upstream findings |

## Policy
1. **One topic = one issue.** Status updates go into the issue body checklist, not into new issues.
2. **Upstream first:** if an upstream issue exists, link it - the local issue tracks *our* view (impact, workarounds, fix status).
3. **Forks carry no issues** (GitHub default for forks, kept as-is): e.g. [JONIMONI09/vibeworks](https://github.com/JONIMONI09/vibeworks) is the code mirror of [MoinMornhart/vibeworks](https://github.com/MoinMornhart/vibeworks) reserved for potential upstream PRs - its issues live here.
4. **Code repos stay clean:** release/product repos (e.g. JONIMONI09/Mobile-Harness) keep only issues about the product itself; tooling/incident topics move here.
5. Labels: `upstream`, `vibeworks`, `finding`, `usability`, `diagnosability`, `watchlist`.

## Current watchlist
| Upstream | Our fork | Tracking issue |
|---|---|---|
| [MoinMornhart/vibeworks](https://github.com/MoinMornhart/vibeworks) | [JONIMONI09/vibeworks](https://github.com/JONIMONI09/vibeworks) | #1 (MCP 401 + idle logout, upstream issue [#25](https://github.com/MoinMornhart/vibeworks/issues/25)) |

## Related
- Self-hosted instance: https://vibeworks.morncloud.de (project "Harness")
- Product repo: https://github.com/JONIMONI09/Mobile-Harness
