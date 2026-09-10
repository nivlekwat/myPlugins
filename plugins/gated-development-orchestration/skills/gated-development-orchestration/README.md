# Gated Development Orchestration 1.4.0

One shared instruction package for ChatGPT Chat / Pro orchestration and independent review, and local Codex implementation.

## What changed from 1.3.0

- Added explicit ChatGPT thread routing metadata:
  `<!-- gated-development:chatgpt-thread:v1 id=<UUID> -->`
- Activation and correction work orders include the current ChatGPT thread marker when automated return review is desired.
- Codex copies the exact marker unchanged into evidence or blocker reports.
- An external review-only workflow may consume Codex evidence and send a review request back to that exact ChatGPT conversation.
- Codex must not invoke the ChatGPT bridge itself.
- Updated AquaTwin launcher guidance: the implementation runner is transport-only and must not become a second policy engine.
- Updated runtime guidance: automated Codex execution defaults to `model_reasoning_effort=max` unless the executable case explicitly overrides it.
- Preserved independent-review, frozen-work-order, bounded-correction, no-self-PASS, remote-evidence, and checkpoint-boundary rules.

## Package layout

```text
gated-development-orchestration/
  SKILL.md
  README.md
  references/
    authority-and-lifecycle.md
    gate-issue-templates.md
    evidence-and-review.md
    automation-handoff.md
    model-selection.md
```

## Deployment model

Use the same package on both sides.

- ChatGPT Chat / Pro: orchestrator + independent reviewer.
- Local Codex: implementer.
- GitHub: durable coordination record and routing ledger.
- Implementation runner: transport-only Codex launcher.
- Review workflow: transport-only return path into the designated ChatGPT thread.

Do not maintain divergent ChatGPT and Codex versions of this skill.

## Routing marker

For automated return review, the executable activation or correction contains exactly one marker:

```text
<!-- gated-development:chatgpt-thread:v1 id=01a087a5-978e-7861-8894-ceabf6c4d3ec -->
```

Codex reproduces the exact same marker in its evidence or blocker. It must not invent, infer, replace, normalize, or select another thread ID.

The marker belongs to the executable comment, not necessarily the frozen issue body. A later checkpoint may legitimately route to a different ChatGPT conversation.

## Runtime default

The AquaTwin automated Codex launcher supplies `model_reasoning_effort=max` by default. A current executable case may explicitly override reasoning effort. Runtime configuration never changes product scope or checkpoint authority.

## Important boundary

The return bridge is external transport. Codex posts evidence to GitHub and stops. The review workflow, not Codex, sends the review request to ChatGPT.
