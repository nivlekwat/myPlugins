---
name: gated-development-orchestration
description: Coordinate checkpoint development through pinned source documents, frozen GitHub work orders, local Codex execution, GitHub-routed return review, and independent ChatGPT review. Use for gate creation, activation, implementation, evidence review, bounded corrections, and review routing. Choose the current role before acting; reading or reviewing this skill does not authorize a checkpoint or a GitHub write.
compatibility: Requires access to the complete skill references and relevant GitHub sources. Implementation requires an authorized local git environment and configured tools. ChatGPT Chat orchestration does not require access to the user's local filesystem.
metadata:
  version: "1.4.0"
  workflow: "github-codex-gated-development"
---

# Gated Development Orchestration

## Purpose and role selection

Use one shared workflow contract, not separately maintained Codex and ChatGPT variants.

**ChatGPT Chat / Pro normally acts as orchestrator and independent reviewer. Local Codex normally acts as implementer.**

Select the role from the current human request and authorized execution context:

- launcher-delivered activation/correction -> implementer
- routed evidence review request -> reviewer
- planning/discussion -> orchestrator/read-only until write authority exists

A checkpoint implementation run must never promote itself to its own independent reviewer.

| Role | Owns | Must not do |
|---|---|---|
| Human / product owner | Intent, scope, architecture decisions, activation authority, cancellation and overrides | Nothing in this skill delegates final product authority away from the human |
| Orchestrator / reviewer | Source tracing, source-of-truth documents, issue topology, activation, fresh remote review, correction orders, acceptance records, review routing | Treat Codex summaries or Actions success as proof; silently replace ChatGPT review with Codex/Work/API review |
| Codex implementer | Exact work-order preflight, bounded implementation/analysis, verification, authorized commit/push, evidence or blocker publication | Self-approve, activate the next gate, directly invoke the ChatGPT bridge, invent/change a ChatGPT thread ID |
| Implementation launcher | Validate basic delivery and start Codex | Decide scope, branch policy, architecture, gate validity, acceptance, or repository repair |
| Review transport | Recognize review-target comments and route them to the declared ChatGPT thread | Judge evidence, issue PASS/correction, or reinterpret the case |

GitHub is the durable coordination record. Comment markers define message type and routing intent; the thread marker identifies a ChatGPT destination only.

## References to load

- [Authority and lifecycle](references/authority-and-lifecycle.md)
- [Gate and issue templates](references/gate-issue-templates.md)
- [Evidence and review](references/evidence-and-review.md)
- [Automation handoff](references/automation-handoff.md)
- [Model selection](references/model-selection.md)

Read the references required for the current phase. Record the skill version and source actually used when reporting an automated run or review.

## Core invariants

1. The pinned source-of-truth defines feature meaning and architecture. The frozen gate body plus matching activation authorize only the current slice.
2. Keep checkpoints as checkpoints. Corrections stay in the same gate only while objective, architecture and maximum path boundary remain valid.
3. A valid activation is also the execution trigger. A valid correction work order is also the correction trigger. No extra dispatch comment is required.
4. PASS accepts the current checkpoint; it does not itself authorize an unactivated successor.
5. Codex implements and reports; ChatGPT independently inspects remote evidence. Codex never issues its own independent PASS.
6. Preserve the original gate starting SHA/review base through corrections. Only correction execution start changes.
7. Implementation/documentation: verify -> commit -> normal authorized push -> confirm remote containment -> evidence -> stop.
8. Analysis-only: no repository writes/commit/push.
9. Do not merge, rebase, pull, reset, clean, force-push, switch worktrees, create branches, or repair repository state unless the case explicitly authorizes that operation.
10. Discovered cleanup/defects/future work are not automatically authorized.
11. Launch success, CLI exit, a posted URL, or evidence is not acceptance.
12. Preserve history; never fabricate SHAs, hashes, issue numbers, tool outcomes, credentials, runtime settings, or verification.
13. The implementation runner is transport-only. It must not add case rules that are absent from the case/skill.
14. The ChatGPT thread marker is routing metadata only. It does not grant scope, activation, correction, or acceptance authority.
15. Codex never invokes the ChatGPT return bridge. It posts GitHub evidence/blocker and stops.

## ChatGPT thread routing

When automated return review is desired, an executable activation or correction includes exactly one:

```text
<!-- gated-development:chatgpt-thread:v1 id=<UUID> -->
```

Rules:

- The orchestrator uses the current ChatGPT conversation's actual thread ID.
- Put the marker in the executable activation/correction comment.
- Do not require it in the frozen issue body.
- Codex copies the exact marker unchanged into terminal evidence or blocker.
- Codex must not invent, infer, normalize, substitute, or select a thread ID.
- If the triggering work order has no thread marker, Codex still executes and reports normally; no automatic return review may be possible.
- PASS and verification-blocked review comments do not need to carry the thread marker unless another transport explicitly requires it.
- A correction-required comment should contain the current reviewer thread marker so the next Codex evidence returns to that reviewer conversation.

## Orchestrator path

### Define and prepare

Inspect repository behavior and architecture. Separate current behavior from desired behavior. Define ownership, boundaries, non-goals, verification, and independently reviewable checkpoints.

For strict bridge/refactor work, existing behavior is the acceptance oracle unless the gate explicitly authorizes behavior change.

Prepare the gate using the exact templates. Keep scope, source pin, branch/baseline, verification and stop conditions explicit.

### Activate

After human authorization and prerequisite readiness:

1. finalize the exact gate body;
2. fetch the final GitHub body and hash it where the case requires;
3. publish a new activation comment;
4. when automated ChatGPT return review is desired, include the current thread routing marker immediately after the executable marker.

Posting the activation is the real handoff. Do not append another dispatch comment.

## Implementer path

### Revalidate the case

Read repository instructions, this skill/references, the exact triggering comment, relevant issue history, pinned source, and actual repository state.

The launcher only transported the instruction. Codex owns semantic preflight under the case. If the case authorizes bootstrap branch/worktree creation or other repository setup, follow the case; if not, do not invent it.

Stop and report when the trigger is edited/mismatched, superseded/cancelled/accepted, scope conflicts, repository state violates the case, or required evidence cannot be established.

### Execute

Perform only the authorized checkpoint/correction. Run exact required verification. Preserve native behavior for bridge/refactor work. Review the complete gate range and correction delta as applicable.

### Publish

Post one terminal evidence or blocker using the standard markers.

If the trigger contains a ChatGPT thread marker, copy that marker exactly into the report.

After publication, return the report URL and stop. **Do not call the ChatGPT return bridge yourself.**

## Reviewer path

A routed review request identifies an issue/evidence comment but does not prove the evidence.

Independently fetch:

- gate issue
- exact evidence comment
- activation/correction
- pinned source
- remote ending commit/branch
- full gate diff and correction delta
- required verification evidence
- acceptance criteria

Issue one of:

- PASS
- correction-required
- verification-blocked

For correction-required, publish a complete executable correction comment and include the current ChatGPT thread routing marker so the resulting Codex evidence returns to this reviewer thread.

Do not issue a correction marker merely to solve delivery/access/publication problems.

## Runtime configuration

Automated AquaTwin Codex execution defaults to `model_reasoning_effort=max` unless the executable case explicitly supplies a supported reasoning-effort override.

Runtime configuration is separate from product authority. The runner may apply the default mechanically; it must not infer which reasoning level a case deserves.

## Terminal outcomes

Codex evidence:

```text
Evidence posted. Awaiting independent review. Not PASS.
Report: <actual evidence URL>
```

Codex blocker:

```text
Blocked: <concise reason>. Not PASS.
Report: <actual blocker URL>
```

Publication failure:

```text
Report publication blocked. No GitHub comment was posted. Not PASS.
Local report: <actual saved path or unavailable>
```

Reviewer outcomes:

- `PASS — accepted commit <SHA>`
- `PASS — analysis evidence <reference>`
- `NOT PASS — correction required`
- `VERIFICATION BLOCKED`
