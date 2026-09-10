# Authority and Lifecycle Reference

Use when choosing issue topology, defining gate types, freezing/hashing work orders, interpreting state, or classifying discoveries. Read the automation-handoff reference as well for automated authoring/execution.

## Artifact authority

| Artifact | Authority |
|---|---|
| Source-of-truth document | Feature meaning, preserved/desired behavior, architecture, ownership, non-goals, risks, and gate sequence |
| Parent issue | Overall tracking, accepted checkpoints, dependencies and deferred work; not an execution command |
| Frozen gate body | Initial work order: type, objective, starting state, authorized/prohibited work, acceptance IDs, verification, stop conditions |
| Activation comment | Frozen-body hash, work-order version, baseline/branch, review base, push policy, and execution authorization/trigger |
| Amendment comment | Approved clarification/narrowing; not silent body replacement or independent launch authorization |
| Correction comment | Independent failed-review decision and versioned narrow repair work order/trigger |
| Commits and complete gate diff | Actual implementation truth |
| Codex evidence | Claimed execution evidence, never independent acceptance |
| Independent review | PASS, correction-required, or verification-blocked decision |
| Launcher records | Delivery/process facts, never scope authorization or gate PASS |
| Chat | Discussion and human decisions; durable work orders/decisions are written back to GitHub or pinned source documents |

Repair a conflicting parent summary rather than elevating it above controlling gate records. An Actions launch acknowledgment does not prove that Codex completed semantic preflight or started implementation.

## Topology and discovered work

Use one issue for one independently accepted outcome with uniform ownership, risk and verification. Use a parent with child gates for separately accepted checkpoints, different risk/host/mutation boundaries, or dependencies on earlier accepted commits.

Normally create the next executable gate only, unless the human requests the full issue set. Pre-creation is not activation. Record historical accepted gates rather than recreating them.

Keep a correction in the same gate only when the objective, source-of-truth architecture, ownership and maximum allowed path boundary remain valid. Create another gate/issue for a distinct product defect, material architecture change, substantial boundary expansion, different owner/risk model, or independently schedulable work. Do not hide native bug fixes inside bridge corrections.

Classify discoveries before acting:

| Classification | Action |
|---|---|
| In-scope correction | Repair under this gate's versioned correction order |
| Blocking external defect | Stop, publish evidence/blocker, obtain the required decision; do not silently fix it |
| Nonblocking deferred defect | Record in evidence for the orchestrator to track separately; do not broaden implementation |
| Architecture/replanning issue | Stop, revise the durable source of truth under authorization, supersede/replace the gate |

The implementer must not create unrelated issues/commits merely because a deferred discovery deserves tracking; respect the current role and write authority.

## Gate types

### Implementation and documentation

Require original starting SHA, expected remote branch, complete gate diff, required commit message, `Push policy: agent`, and a remotely inspectable ending commit for final PASS.

Codex runs required verification, commits, normal-pushes only the authorized branch, confirms remote containment, posts evidence and stops. A failed push/verification gets a truthful blocker or partial evidence; it does not authorize merge/rebase/pull/reset/clean/force-push or unrelated repairs. A separately authorized repository repair is not inferred from the requirement to push.

Documentation follows the same ancestry and remote-evidence requirements as implementation. Creating a new repository report is a documentation write, not analysis-only.

### Analysis-only

Declare `Gate type: analysis-only`, `Push policy: none`, a pinned baseline, acceptance criteria assessable without a code diff, and a durable non-repository-write destination such as the gate issue comment.

No repository files, commits or pushes are allowed, including for corrections. Local automation logs outside the repository are transport records, not an authorization to create project files. Link an existing report when appropriate; do not create a repository report under a no-write gate.

For PASS, identify accepted evidence and baseline; `Accepted commit` is `N/A`. New-commit containment, ancestry-of-new-commits and changed-path/diff criteria are `N/A — no-write gate`, but the reviewer must still establish baseline identity, substantive criteria, evidence accessibility and the no-write boundary. Do not treat missing evidence as N/A or ignore unauthorized modifications.

## Exact freezing and hashes

The body is editable during DRAFT/READY. On authorized activation it is frozen. Use `Initial status`, not a mutable current-status field; immutable acceptance IDs rather than checkboxes to be changed later; and an original starting SHA/review base fixed through all correction cycles.

After activation, place implementation evidence, review, criteria outcomes, corrections, amendments and state transitions in comments. Do not edit the body to make history appear current. Do not edit/delete an activation or correction after delivery.

Compute the body hash as follows:

1. Fetch the finalized gate from GitHub.
2. Take the exact returned `body` string, not rendered Markdown or a saved draft.
3. Encode it as UTF-8 with no added BOM, trim, newline or line-ending normalization.
4. Compute lowercase hexadecimal SHA-256 and store algorithm/digest in activation.

```text
body_hash = hex_lower(SHA256(UTF8(exact_github_issue_body)))
```

For an automated trigger, apply the same convention to the exact triggering comment body and compare it to the supplied trigger hash. Codex re-fetches/rechecks before editing; the reviewer rechecks the frozen gate before acceptance.

A mismatch blocks execution and acceptance. It does not establish an implementation defect. Never guess the intended version or repair hashes inside a local request file.

When a body change is explicitly approved before implementation begins, reconcile any launched/delivered attempt first; issue a deliberately versioned replacement activation with a fresh body/hash. Once implementation began, prefer supersession for a necessary body rewrite; an approved narrow clarification belongs in an amendment comment and must not alter the frozen body. Preserve history.

## Version meanings

- Skill package version: release of these instructions, currently `1.3.0`.
- Marker version: syntax contract, such as `activation:v1` or `review:v2`.
- Work-order version: numeric initial/correction/replacement instruction sequence for one gate.
- Checkpoint identity: CP1, CP2, CP3A, and so on.

These are independent. Increase the work-order version for a new correction, not the marker version. Do not reset versions to defeat a duplicate-execution claim. The frozen body's initial version remains fixed; correction versions are recorded in comments.

## Instruction precedence and exact binding

After platform/safety instructions, follow current explicit human instructions, applicable repository instructions, pinned source of truth, frozen gate/activation, applicable approved corrections/amendments, actual source/evidence, then other comments/summaries.

A gate can narrow but not contradict the source of truth. An amendment/correction cannot silently expand architecture or paths. Record unresolved conflicts and stop.

The automated implementer uses the exact supplied triggering comment/version. Read newer controlling history to identify supersession, cancellation or invalidation. Do not silently switch this process to a different latest work order. The orchestrator must deliver any replacement deliberately. A process-start receipt does not establish scope validity.

## State history

```text
DRAFT -> READY -> IN_PROGRESS -> EVIDENCE_POSTED -> UNDER_REVIEW
                                                    |
                       +----------------------------+-----------------+
                       |                            |                 |
                CORRECTION_REQUIRED                PASS      VERIFICATION_BLOCKED
                       |                            |                 |
                  IN_PROGRESS                     CLOSED        UNDER_REVIEW

A gate may also become SUPERSEDED or CANCELLED through an authorized state record.
```

DRAFT means decisions remain. READY means a complete gate exists but has not necessarily been activated. IN_PROGRESS requires evidence of actual execution, not just a posted activation or launch receipt. EVIDENCE_POSTED means a report is available, never PASS. UNDER_REVIEW is fresh independent inspection. CORRECTION_REQUIRED authorizes only a finalized in-scope correction. VERIFICATION_BLOCKED means no defensible merits verdict can yet be reached. PASS requires all mandatory criteria. SUPERSEDED/CANCELLED stop further work under the old authority.

Labels and parent tables may summarize state but are not substitutes for marked comments. Do not emit a controlling state record merely as a launch acknowledgment, particularly while an activation is still queued.

## Correction invariants

Define `B` as the original gate starting SHA/review diff base, `P` as the reviewed prior ending SHA (or unchanged baseline for analysis-only), and `E` as the newly submitted ending SHA for a write gate.

A correction records its incremented work-order version, same branch/upstream, original `B`, required execution start `P`, prior reviewed ending `P`, activation and evidence references, source pin, bounded repair, prohibited expansion, verification, and gate-type-appropriate push policy/commit message.

`P` changes execution start only. Review still considers the whole gate `B...E` after proving `B` is an ancestor of `E`, plus correction delta `P...E` after proving `P` is an ancestor of `E`. Do not review only the last commit or the whole feature branch against dev.

For implementation/documentation corrections, verify -> commit -> normal authorized push -> report. For analysis-only corrections, keep the repository baseline unchanged and publish corrected analysis evidence without commit/push.

A correction-required marker must not be used for a mere delivery, local credential or reviewer-access problem. If required verification is genuinely missing or fails due to implementation, a bounded correction can be appropriate; explain the evidence rather than guessing the cause.

## Independence and advancement

Independence requires fresh remote source/evidence inspection, not a new GitHub username or blindly trusting a second model. The orchestration conversation may review work implemented elsewhere. A separate adversarial reviewer is optional where warranted; the implementer cannot accept its own work.

Human activation is sufficient execution authorization. Do not request a second manual dispatch or start command. Preserve checkpoint acceptance and activation boundaries: after PASS, use the approved gate sequence and existing explicit human authority to prepare/activate the next gate. When new activation authority is missing, keep it READY instead of inventing blanket permission. An existing authorization must not be re-requested simply because a launcher is automated.

The installed launcher does not automatically start the next checkpoint merely from PASS, nor does this Markdown add that capability.
