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
| [techjarves/Mobile-Harness](https://github.com/techjarves/Mobile-Harness) | [JONIMONI09/Mobile-Harness](https://github.com/JONIMONI09/Mobile-Harness) | #3 (hardcoded DeepSeek API key in fork git history, awaiting upstream rotation) |
| [MoinMornhart/vibeworks](https://github.com/MoinMornhart/vibeworks) | [JONIMONI09/vibeworks](https://github.com/JONIMONI09/vibeworks) | #5 (get_code_graph returns graph.errors.noFiles despite working repo copy, [analysis](docs/vibeworks-code-graph-nofiles.md)) |

## Automation (since 2026-09-15)
- **Claude cron:** `.github/workflows/claude-cron.yml` runs the daily duty from [PROMPT.md](PROMPT.md) inside GitHub Actions (daily 05:17 UTC + manual "Run workflow" button). Auth is repo-scoped by design: built-in `GITHUB_TOKEN` (exists only during the run) + one repository secret (`ANTHROPIC_API_KEY` or `CLAUDE_CODE_OAUTH_TOKEN`) for Claude. Without the Claude secret the run skips gracefully - activation = add the secret, then trigger one manual run.
- **Deploy key:** a dedicated ed25519 deploy key (local `~/.ssh/issuehub_deploy_ed25519`, SSH alias `github-issuehub`) is bound to THIS repository only (GitHub deploy keys are per-repo by design). It never touches other repos and is excluded from version control via `.gitignore`.

## Related
- Self-hosted instance: https://vibeworks.morncloud.de (project "Harness")
- Product repo: https://github.com/JONIMONI09/Mobile-Harness
