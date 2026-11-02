# milanote-cc-session-audit

Redacted records of Claude Code sessions for the Milanote screenshot-organizing project, kept so a reviewer (human or AI) can read the actual execution evidence instead of summaries.

- **Visibility:** Private.
- **Authorization status:** IT department gave **verbal consent only; no written record exists**. Sharing these records with ChatGPT or any other service is **NOT authorized / still unconfirmed**. Do not connect this repo to other services until that is settled.
- **Human review required:** nothing is pushed automatically. A push only happens after a person reviews `export/review/` and runs the sync command with `--approved-by-human`.

## Start here
1. `reports/latest.md` — latest progress, open issues, next steps (has an auto-generated status block at the bottom).
2. `reports/pending-actions.md` — what is waiting on the user.
3. `reports/progress.md` — cumulative progress (append only).
4. `reports/sessions-index.md` — every exported session with counts and time range.
5. `reports/hooks-design.md` — what each Claude Code hook can and cannot capture.
6. `sessions/<session-id>/` — `conversation.md` (user prompts + assistant visible replies), `tool-calls.md`, `terminal-output.log`, `session-metadata.json`.
7. `logs/execution-history.md` — append-only event log (auto-exports and approved pushes).

## What is NOT here
Hidden reasoning, system prompts, system-reminder blocks, images, raw Milanote screenshots, cookies, tokens, browser profiles, unredacted terminal output (kept locally, outside git). Redacted values appear as `[REDACTED:type]`.
Redaction is rule-based and is not proof of safety; it is backed by a manual review step.

## Known limits
- Shell exit codes are only present when tool result text includes them (the PostToolUseFailure `error` field starts with "Exit code N").
- The session transcript is written asynchronously, so the newest messages can appear one turn late.
- Hook field names come from unversioned docs; they were verified in practice on Claude Code 2.1.316 (see sessions-index and hooks-design).

## Local automation (on the author's machine, not in this repo)
Project-level hooks in `.claude/settings.local.json` run `export/tools/hook-log.js` on UserPromptSubmit, PostToolUse, PostToolUseFailure, Stop and SessionEnd: raw events go to `export/spool/`, then `export.js` and `update-reports.js` regenerate `raw/` (local only) and `review/` (redacted). `export/tools/sync.js` is the human-gated push (dry-run by default).
