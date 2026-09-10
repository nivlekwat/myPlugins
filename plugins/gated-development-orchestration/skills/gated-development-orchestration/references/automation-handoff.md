# Automation Handoff Reference

This is the shared automation contract for ChatGPT orchestration/review, local Codex implementation, the transport-only implementation runner, and the review-only return workflow.

## Routing by first-line marker

| First-line marker | Destination | Meaning |
|---|---|---|
| `<!-- gated-development:activation:v1 -->` | Codex | Execute authorized checkpoint |
| `<!-- gated-development:review:v2 status=correction-required -->` | Codex | Execute authorized correction |
| `<!-- gated-development:codex-evidence:v2 -->` | ChatGPT review transport | Request independent review |
| `<!-- gated-development:blocker:v2 -->` | ChatGPT blocker transport when configured | Request blocker triage |
| `<!-- gated-development:review:v2 status=pass -->` | Ledger only | Accepted checkpoint |
| `<!-- gated-development:review:v2 status=verification-blocked -->` | Ledger only | Review could not complete |
| state/amendment markers | Ledger/control only | Do not independently launch Codex or ChatGPT review |

Marker versions, skill version, checkpoint identity, and work-order version are separate concepts.

## Thread routing marker

For automated return review, use exactly one:

```text
<!-- gated-development:chatgpt-thread:v1 id=<UUID> -->
```

This marker is transport metadata only.

### Orchestrator

Put it in each executable activation/correction that should return to a ChatGPT conversation.

### Codex

If the triggering work order contains the marker:

- copy the exact marker unchanged into evidence or blocker;
- do not invent, infer, normalize, replace, or select a different thread ID;
- do not invoke the ChatGPT bridge directly.

### Review transport

The review workflow may extract the marker from evidence and set the local bridge's thread target. It should pass a compact review request containing repository, issue, and exact evidence URL. ChatGPT then fetches and independently verifies the case.

The review transport must not decide PASS/correction/verification-blocked itself.

## Implementation transport

The AquaTwin implementation runner is deliberately dumb.

It may validate:

- correct repository
- correct configured account/sender
- issue-comment-created event
- issue/comment IDs
- supported executable first-line marker
- nonempty instruction
- local launch prerequisites
- duplicate delivery of the exact same GitHub comment

It should not enforce case-specific rules such as:

- source-of-truth field names
- branch existence/creation policy
- starting SHA
- upstream/remote state
- worktree cleanliness
- gate/body hashes
- work-order sequencing
- allowed paths
- verification sufficiency
- checkpoint lifecycle validity

Those belong to Codex following the case and skill.

## Detached execution

GitHub Actions ends after startup acknowledgment. Codex continues independently in its own visible PowerShell console.

Durable local run records may include:

- `request.json`
- `prompt.txt`
- `codex-events.jsonl`
- `codex-stderr.log`
- `progress.log`
- `final.txt`
- `started.json`
- `completed.json`
- `error.json`

A launch acknowledgment is not implementation completion or PASS.

## Visible console

Future executions may display readable live progress while preserving raw logs. Console visibility is presentation, not authorization or isolation policy.

Closing the execution console may interrupt its attached Codex process. A read-only viewer may be closed safely when it is explicitly implemented as view-only.

## Runtime default

The automated AquaTwin launcher applies:

```text
model_reasoning_effort=max
```

unless the executable case explicitly overrides reasoning effort.

This is a runtime default, not a scope or acceptance rule. The runner does not decide whether a lower/higher effort is appropriate.

## Review-only return workflow

The review workflow should have an allowlist, not interpret arbitrary comments.

Minimum production path:

```text
codex-evidence
    -> extract exactly one chatgpt-thread marker
    -> call local ChatGPT bridge for that thread
    -> send repository + issue + exact evidence URL
    -> exit
```

Do not copy the entire evidence report into the transport message unless necessary. The reviewer should fetch it independently from GitHub.

Codex never calls the review bridge. GitHub evidence is the boundary between implementation and independent review.

## Correction loop

```text
ChatGPT activation
  -> Codex
  -> evidence + same thread marker
  -> review workflow
  -> ChatGPT independent review
      -> PASS (ledger only)
      OR
      -> correction-required + current thread marker
          -> Codex
          -> new evidence + same marker
          -> review workflow
```

This avoids generic `Target:` fields. Message type plus routing marker is sufficient.

## Publication and retry

Evidence/blocker publication must be confirmed from the GitHub tool/API result. On ambiguous publication, check before retrying. Retry publication only; do not rerun implementation merely to repair reporting.

If no thread marker exists, publication still succeeds as a GitHub record; automatic ChatGPT return may simply be unavailable.

## Security and secrets

Keep local bridge mechanics local:

- local script path
- named pipe path
- browser/session connection details
- authentication material

Only the ChatGPT thread UUID needs to travel in GitHub comments for routing.

Never publish cookies, tokens, credential files, or secrets.

## Boundaries

The thread marker is not cryptographic authentication. The review workflow still validates the GitHub event/sender it trusts. A shared GitHub identity does not prove ChatGPT vs Codex authorship; role behavior comes from the workflow contract and independent verification.
