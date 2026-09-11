---
name: gated-development-orchestration
description: Coordinate checkpoint development through pinned source documents, frozen GitHub work orders, local Codex execution, GitHub-routed return review, and independent ChatGPT review. Use for gate creation, activation, implementation, evidence review, bounded corrections, and review routing. Choose the current role before acting; reading or reviewing this skill does not authorize a checkpoint or a GitHub write.
compatibility: Requires access to the complete skill references and relevant GitHub sources. Implementation requires an authorized local git environment and configured tools. ChatGPT Chat orchestration does not require access to the user's local filesystem.
metadata:
  version: "1.4.1"
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
| Review transport | Recognize review-target comments and route them to the declared ChatGPT thread | Judge evidence, issue PASS/correction, reinterpret the case, or substitute a different destination |

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
12. Preserve history; never fabricate SHAs, hashes, issue numbers, tool outcomes, credentials, runtime settings, verification, or ChatGPT thread IDs.
13. The implementation runner is transport-only. It must not add case rules that are absent from the case/skill.
14. The ChatGPT thread marker is routing metadata only. It does not grant scope, activation, correction, or acceptance authority.
15. Codex never invokes the ChatGPT return bridge. It posts GitHub evidence/blocker and stops.
16. In the configured automated AquaTwin workflow, activation and correction comments must carry the actual current ChatGPT conversation ID unless the human explicitly requests manual/no-return routing.
17. Automated return routing must fail closed. A missing, invalid, substituted, fixed/default, or mismatched ChatGPT destination is a routing failure, not successful delivery.
18. Never use a Codex session/thread ID as a ChatGPT conversation destination. The two identifiers have different meanings.

## ChatGPT thread routing

### Normal automated workflow

For the configured AquaTwin automated workflow, every executable activation or correction must include exactly one routing marker immediately after its executable marker:

```text
<!-- gated-development:chatgpt-thread:v1 id=<UUID> -->
```

Activation:

```text
<!-- gated-development:activation:v1 -->
<!-- gated-development:chatgpt-thread:v1 id=<actual current ChatGPT conversation UUID> -->
```

Correction:

```text
<!-- gated-development:review:v2 status=correction-required -->
<!-- gated-development:chatgpt-thread:v1 id=<actual current reviewer ChatGPT conversation UUID> -->
```

### Source of the thread ID

The orchestrator must obtain the actual current ChatGPT conversation ID from an authoritative runtime source.

It must not:

- infer the ID from another identifier;
- reuse an ID from a prior activation, issue, project, Codex session, or unrelated conversation;
- use `CODEX_THREAD_ID` as the ChatGPT destination;
- copy a thread ID merely because it worked in a previous test;
- invent a UUID;
- substitute a fixed/default test conversation.

If the actual current ChatGPT conversation ID cannot be established authoritatively, **do not publish the executable activation or correction**.

Report the routing problem instead. Do not silently downgrade the automated workflow to manual return review.

### Explicit manual-routing exception

The thread marker may be omitted only when the human explicitly requests manual/no-return routing for that specific activation or correction.

Absence of an exposed thread ID is not, by itself, permission to choose manual routing.

### Propagation rules

- Put the marker in the executable activation/correction comment.
- Do not require it in the frozen issue body.
- Codex copies the exact marker unchanged into terminal evidence or blocker.
- Codex must not invent, infer, normalize, substitute, or select a thread ID.
- PASS and verification-blocked comments do not need the marker unless another transport explicitly requires it.
- A correction-required comment must use the current reviewer conversation ID, not automatically reuse the original activation ID.

### Review transport rules

The review transport:

1. reads the declared thread marker from evidence/blocker;
2. passes that UUID through an explicit input intended specifically for ChatGPT destination routing;
3. must not overload a Codex-session identifier for that purpose;
4. must route to the declared destination;
5. when the bridge reports the selected/actual destination, must compare it to the requested UUID;
6. must fail closed on mismatch;
7. must not silently fall back to a fixed/default ChatGPT conversation.

A transport that cannot accept an explicit ChatGPT destination is not production-ready for automated return review.

## Orchestrator path

### Define and prepare

Inspect repository behavior and architecture. Separate current behavior from desired behavior. Define ownership, boundaries, non-goals, verification, and independently reviewable checkpoints.

For strict bridge/refactor work, existing behavior is the acceptance oracle unless the gate explicitly authorizes behavior change.

Prepare the gate using the exact templates. Keep scope, source pin, branch/baseline, verification and stop conditions explicit.

### Activate

After human authorization and prerequisite readiness:

1. finalize the exact gate body;
2. fetch the final GitHub body and hash it where the case requires;
3. obtain the actual current ChatGPT conversation ID from an authoritative runtime source;
4. publish a new activation comment containing the activation marker followed immediately by the thread routing marker;
5. let the configured launcher perform the handoff.

Do not append another dispatch comment.

If step 3 cannot be completed, stop before publishing the executable activation unless the human explicitly requested manual/no-return routing.

Do not post language such as:

> No ChatGPT thread marker is supplied because the current conversation UUID is unavailable.

for a normal automated AquaTwin activation. That is a routing blocker, not a valid automatic-return activation.

## Implementer path

### Revalidate the case

Read repository instructions, this skill/references, the exact triggering comment, relevant issue history, pinned source, and actual repository state.

The launcher only transported the instruction. Codex owns semantic preflight under the case. If the case authorizes bootstrap branch/worktree creation or other repository setup, follow the case; if not, do not invent it.

Stop and report when the trigger is edited/mismatched, superseded/cancelled/accepted, scope conflicts, repository state violates the case, or required evidence cannot be established.

### Execute

Perform only the authorized checkpoint/correction. Run exact required verification. Preserve native behavior for bridge/refactor work. Review the complete gate range and correction delta as applicable.

### Publish

Post one terminal evidence or blocker using the standard markers.

If the trigger contains a ChatGPT thread marker, copy that marker **exactly and unchanged** immediately after the evidence/blocker marker.

Example:

```text
<!-- gated-development:codex-evidence:v2 -->
<!-- gated-development:chatgpt-thread:v1 id=<copied exact UUID> -->
```

Codex must not:

- look up another ChatGPT destination;
- replace the marker with a Codex thread/session ID;
- invoke the ChatGPT bridge;
- decide whether the declared ChatGPT destination is appropriate.

After publication, return the report URL and stop.

## Reviewer path

A routed review request identifies an issue/evidence comment but does not prove the evidence.

Independently fetch:

- gate issue;
- exact evidence/blocker comment;
- activation/correction;
- pinned source;
- remote ending commit/branch;
- full gate diff and correction delta;
- required verification evidence;
- acceptance criteria.

Issue one of:

- PASS
- correction-required
- verification-blocked

### Correction-required routing

A correction-required comment is itself an executable work order.

Before publishing it:

1. obtain the actual current reviewer ChatGPT conversation ID from an authoritative runtime source;
2. include it immediately after the correction executable marker;
3. publish the complete bounded correction work order.

If the actual reviewer conversation ID cannot be established, do not publish the executable correction unless the human explicitly requested manual/no-return routing.

The next Codex evidence must return to the reviewer conversation that issued the correction, not necessarily the conversation that issued the original activation.

Do not issue a correction marker merely to solve delivery/access/publication problems.

## Runtime configuration

Automated AquaTwin Codex execution defaults to:

```text
model_reasoning_effort=max
```

unless the executable case explicitly supplies a supported reasoning-effort override.

Runtime configuration is separate from product authority. The runner may apply the default mechanically; it must not infer which reasoning level a case deserves.

When reporting runtime, report the actual exposed runtime/configuration when available. Do not claim MAX merely because it is the configured default if the executing runtime does not expose confirmation.

## Automation flow

Normal AquaTwin flow:

```text
ChatGPT Chat
  |
  | activation + actual ChatGPT thread marker
  v
GitHub checkpoint issue
  |
  v
Implementation Action
  |
  | detached launch only
  v
Local Codex
  |
  | evidence/blocker + exact copied thread marker
  v
GitHub
  |
  v
Independent Review Action
  |
  | explicit ChatGPT target thread
  v
Original/reviewer ChatGPT conversation
  |
  v
Independent review
```

The GitHub Actions jobs are transport. They must not become the authority for scope, architecture, acceptance, or development policy.

The implementation Action should finish after successful dispatch rather than waiting for long-running Codex implementation.

The independent review Action should send the review request once and exit. It should not sleep or poll waiting for the ChatGPT response.

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

Routing unavailable before activation/correction:

```text
BLOCKED — actual current ChatGPT conversation ID is unavailable.
Executable activation/correction was not published.
```

Review transport destination mismatch:

```text
REVIEW ROUTING FAILED — declared ChatGPT destination did not match the bridge-selected destination.
No fallback destination is permitted.
```

Reviewer outcomes:

- `PASS — accepted commit <SHA>`
- `PASS — analysis evidence <reference>`
- `NOT PASS — correction required`
- `VERIFICATION BLOCKED`
