# Auto-sync design (planning only, nothing installed)

Source: official hooks docs (code.claude.com/docs/en/hooks.md), fetched 2026-11-02 via subagent. The page is not version-stamped, so alignment with Claude Code 2.1.316 is UNVERIFIED.

## What each hook can actually see
| Hook | Useful data | Limits |
|---|---|---|
| UserPromptSubmit | `prompt`, session_id, transcript_path | No tool output (runs before tools) |
| PostToolUse | tool_name, tool_input, tool_response (Bash: stdout, stderr) | Exit code NOT in tool_response; only on success |
| PostToolUseFailure | tool_input, `error` (starts "Exit code N", stdout+stderr merged) | Routing rule inferred from one doc example |
| Stop | `last_assistant_message` | Not run on user interrupt |
| StopFailure | error type, API error text | Not Claude's output; no control |
| SessionEnd | `reason` | Default timeout 1.5 s (raise via per-hook timeout); transcript completeness UNVERIFIED |
| MessageDisplay | exists, display-only | stdin fields UNVERIFIED |

All hooks get `transcript_path`, but it is written asynchronously and may lag, so it may miss the latest messages. No single hook captures all terminal content.

## Proposed design (human review kept)
1. `Stop` hook (node script, exec form on Windows): append `last_assistant_message` and re-run the exporter against `transcript_path` into local `raw/` only. Never network.
2. `PostToolUse` + `PostToolUseFailure`: append tool_response / error to a local spool file, covering exit codes the transcript lacks.
3. `SessionEnd`: final re-export with a raised timeout.
4. Redaction step produces `review/`. A diff summary is shown to the user.
5. Push is manual: the user runs a sync script after reviewing. No hook performs git push.

## Status: NOT CONFIGURED (design only). Requires user approval to edit settings.json.
