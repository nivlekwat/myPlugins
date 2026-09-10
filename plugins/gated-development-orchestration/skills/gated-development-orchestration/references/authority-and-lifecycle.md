# Authority and Lifecycle Reference

Use when defining gate authority, freezing/hashing work orders, interpreting state, corrections, routing metadata, or acceptance.

## Artifact authority

| Artifact | Authority |
|---|---|
| Source-of-truth document | Feature meaning, behavior, architecture, ownership, non-goals, gate sequence |
| Parent issue | Overall tracking and accepted checkpoints |
| Frozen gate body | Initial work order |
| Activation comment | Execution authorization/trigger for current gate |
| Correction-required comment | Independent failed-review decision and narrow correction authorization/trigger |
| Commits and full gate diff | Actual implementation truth |
| Codex evidence/blocker | Implementer report; never acceptance |
| Independent review | PASS, correction-required, verification-blocked |
| Launcher records | Delivery/runtime facts only |
| ChatGPT thread routing marker | Review transport destination only; no product authority |
| Chat | Discussion/human decisions until written to durable GitHub/source records |

## Version meanings

- Skill package version: `1.4.0`
- Marker version: e.g. `activation:v1`, `review:v2`
- Work-order version: gate execution/correction sequence
- Checkpoint identity: CP1, CP2, etc.
- Thread-routing marker version: `chatgpt-thread:v1`

These are independent.

## Routing authority

The thread marker:

```text
<!-- gated-development:chatgpt-thread:v1 id=<UUID> -->
```

identifies where independent review should return. It does not:

- activate a gate;
- authorize implementation;
- broaden/narrow scope;
- authorize repository operations;
- establish evidence validity;
- issue PASS;
- prove who authored a comment.

Executable authority still comes from activation/correction plus the controlling case.

## Freezing and history

The gate body may be edited while draft/ready. On activation, treat the controlling body/activation as frozen according to the case. Preserve old comments. Use new comments for evidence, review, correction, amendment, supersession, and cancellation.

Do not rewrite history merely to add a missing thread marker. If an old execution lacks routing metadata, review may be initiated manually.

## Gate types

### Implementation/documentation

Require remotely reviewable ending evidence according to the case. Codex verifies, commits, normally pushes as authorized, posts evidence, and stops.

### Analysis-only

No repository writes, commits, or pushes. Review baseline/evidence/no-write compliance.

## State model

```text
DRAFT -> READY -> ACTIVATED -> IN_PROGRESS -> EVIDENCE_POSTED -> UNDER_REVIEW
                                                               |
                                   +---------------------------+------------------+
                                   |                           |                  |
                         CORRECTION_REQUIRED                  PASS       VERIFICATION_BLOCKED
                                   |
                              IN_PROGRESS
```

SUPERSEDED and CANCELLED may occur through authorized state records.

## Correction invariants

Keep the original gate review base fixed. A correction starts from the reviewed prior ending SHA/baseline and authorizes only the narrow repair.

When automated return review is desired, correction-required includes the current reviewer thread marker. Codex copies it into the next evidence/blocker.

## Independence

Independence requires fresh remote inspection, not a different GitHub username. The implementing Codex session cannot accept its own work.

A review transport waking the ChatGPT conversation does not weaken independence because the reviewer still re-fetches and verifies the remote case itself.
