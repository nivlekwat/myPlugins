# Gated Development Orchestration Plugin 1.4.0

This plugin wraps the shared **Gated Development Orchestration 1.4.0** skill for both ChatGPT Chat / Pro orchestration-review and local Codex implementation.

## Included

- `.codex-plugin/plugin.json` — plugin manifest
- `.app.json` — existing GitHub app reference; unchanged
- `skills/gated-development-orchestration/` — shared workflow contract

## 1.4.0 routing model

Implementation and independent review are routed by GitHub comment markers:

- `gated-development:activation:v1` -> Codex implementation
- `gated-development:review:v2 status=correction-required` -> Codex correction
- `gated-development:codex-evidence:v2` -> independent ChatGPT review transport
- `gated-development:blocker:v2` -> blocker record; may be routed to ChatGPT when blocker triage is configured
- PASS / verification-blocked / state comments -> durable ledger only

Executable activation and correction comments carry a ChatGPT thread routing marker. Codex copies that marker unchanged into its evidence or blocker. An external review workflow may then return the review request to the exact ChatGPT conversation.

The thread marker is transport metadata only. It never grants product scope, implementation permission, correction authority, or acceptance.

## Launcher separation

The AquaTwin implementation runner is intentionally transport-only: it validates the delivery origin/basic shape and starts Codex. Case rules remain in the issue, source-of-truth documents, repository instructions, and this skill.

The ChatGPT return workflow is separate from the Codex launcher. Codex never directly invokes the ChatGPT bridge.

## Compatibility

No MCP server is declared by this plugin. Existing GitHub app configuration remains subject to normal user/workspace connection and permission controls.
