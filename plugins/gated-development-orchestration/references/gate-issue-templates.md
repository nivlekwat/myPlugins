# Gate and Issue Templates

Use for new work. Preserve older issues as history; never rewrite frozen records merely to match current formatting. Implementation/documentation use `Push policy: agent`; analysis-only uses `none`, including corrections.

**Automated activation/correction comments are executable.** Drafts, examples and planning belong in non-executable discussion. Posting a complete authorized activation is the handoff; no second dispatch is needed. Use the exact first-line markers and manifest field spelling shown here, each required field once. Replace placeholder alternatives with one actual value. The automation-handoff reference documents parser constraints.

## Parent feature issue

```markdown
<!-- gated-development:parent:v2 -->
# [AREA] Feature title

## Objective
<Concise product or engineering outcome.>

## Source of truth
- Document: `<path>`
- Approved document commit: `<full lowercase SHA>`
- Working branch: `<branch>`

## Controlling principles
- <Architecture, ownership, behavior and safety rules>

## Non-goals
- ...

## Gate plan
| Gate | Scope | Issue | State | Accepted commit or evidence |
|---|---|---|---|---|
| CP1 | ... | #... | PASS / READY / BLOCKED | ... |

## Dependencies
- ...

## Deferred work
- ...

## Close condition
Close only when every required gate is accepted and final live verification is complete.
```

## Gate issue

Use immutable acceptance IDs rather than mutable acceptance checkboxes.

```markdown
<!-- gated-development:gate:v2 -->
# [AREA][GATE] Gate title

## Gate manifest
- Parent issue: `#<number or none>`
- Gate ID: `<feature>-CP<n>`
- Gate type: `implementation | documentation | analysis-only`
- Work-order version: `1`
- Initial status: `READY`
- Required branch: `<branch>`
- Expected remote branch: `<remote>/<branch>`
- Original gate starting SHA: `<full lowercase SHA>`
- Review diff base: `<same original starting SHA>`
- Source-of-truth document: `<path>`
- Source-of-truth commit: `<full lowercase SHA>`
- Push policy: `agent | none`
- Required commit message: `<message or N/A for analysis-only>`
- Durable evidence destination: `<this issue; or an authorized document path for a write gate>`

## Objective
<Exactly one independently reviewable outcome.>

## Current behavior to preserve
- ...

## Authorized work
- ...

## Authorized production paths
- ...

## Authorized tests and documents
- ...

## Prohibited work
- ...

## Acceptance criteria
- `AC-1` — ...
- `AC-2` — ...

## Required verification
1. `<exact command or inspection>`
2. `<exact command or inspection>`

## Stop conditions
Stop and report without expanding scope when:
- branch, execution starting SHA, worktree, upstream or live remote state differs;
- the body or exact triggering-comment hash does not match;
- the gate is closed, cancelled, superseded, accepted, stale or edited;
- the pinned source of truth conflicts with this issue;
- an unauthorized path, feature, ownership decision or architecture expansion is required;
- preserved behavior cannot be maintained;
- required verification or normal push cannot complete as authorized.
Do not merge, rebase, pull, reset, clean, force-push or switch branches merely to make execution succeed.

## Required Codex evidence
Use `gated-development:codex-evidence:v2`, including trigger/run correlation for an automated execution. Use `gated-development:blocker:v2` for a blocked attempt. Return the actual posted report URL; do not claim publication without a successful tool result.

## Completion rule
Evidence is **Not PASS**. Implementation/documentation: verify, commit, normal-push the authorized branch, establish remote containment, post evidence and stop. Analysis-only: do not write repository files, commit or push; post analysis evidence and stop. Never begin the next gate or invoke a return bridge from this execution.
```

For implementation/documentation, specify `agent`, authorized paths and the required commit message. For analysis-only, use `none`, commit message `N/A`, production paths `N/A`, and explicit baseline/no-write evidence. The current AquaTwin automation also requires a matching local/upstream/live-remote branch even for analysis-only; see automation-handoff.md.

## Activation comment

Finalize the body and compute its exact GitHub-returned UTF-8 SHA-256 before posting. Publish only after human authorization for this gate; use already-given authorization instead of requesting it twice.

```markdown
<!-- gated-development:activation:v1 -->
## Gate activated

- Gate: `<id>`
- Work-order version: `1`
- Gate issue: `<canonical issue URL>`
- Gate body hash algorithm: `sha256`
- Gate body hash: `<64-character lowercase digest>`
- Gate type: `implementation | documentation | analysis-only`
- Source-of-truth document: `<path>`
- Source-of-truth commit: `<full lowercase SHA>`
- Required branch: `<branch>`
- Expected remote branch: `<remote>/<branch>`
- Original gate starting SHA: `<full lowercase SHA>`
- Review diff base: `<same original starting SHA>`
- Push policy: `agent | none`
- Activated by: `<human or authorized orchestrator>`

The issue body is frozen at this hash. This activation authorizes execution of this checkpoint and triggers the configured launcher. No separate dispatch or start confirmation is required. Evidence, review, correction and state changes belong in marked comments.
```

A deliberately approved pre-execution body replacement requires reconciliation of any prior attempt, an increased work-order version, a new hash and a new activation. Preserve the previous record; do not simply edit/replay it.

## Scope amendment

Only for approved clarification or narrowing, not architecture/path expansion or changing correction start.

```markdown
<!-- gated-development:amendment:v2 -->
## Approved gate amendment

- Gate: `<id>`
- Applies to work-order version: `<n>`
- Approved by: `<human or reviewer>`
- Architecture document: `<path>`
- Architecture commit: `<SHA or unchanged>`
- Original gate starting SHA: `<original SHA>`
- Review diff base: `<same original SHA>`

### Amendment
- ...

### Unchanged boundaries
- ...

### Prohibited expansion
- ...

This amendment clarifies or narrows the active gate. Material expansion requires an authorized source-of-truth update and usually a new gate. It is not an independent launch trigger.
```

A newer controlling amendment can invalidate a queued execution. Do not resolve that by editing the queued request or silently treating the amendment as a new work order. Reconcile the current instruction as described in automation-handoff.md.

## Scope blocker

Use instead of unauthorized expansion. Fill unavailable facts with an explicit reason, not invented values.

```markdown
<!-- gated-development:blocker:v2 -->
## Gate blocked before completion

- Gate: `<id>`
- Work-order version: `<executed version>`
- Required branch: `<branch>`
- Required execution starting SHA: `<SHA>`
- Actual branch and SHA: `<branch @ SHA, or unavailable with reason>`
- Worktree state: `<clean or details, or unavailable>`
- Triggering comment: `<actual activation/correction URL>`
- Actions run: `<actual run URL or N/A — manual>`
- Automation request ID: `<supplied ID or N/A — manual>`
- Skill package version used: `<actually loaded version>`
- Skill source used: `<actual file path or pinned source reference>`

### Blocker classification
`blocking external defect | architecture or replanning issue | repository-state mismatch | body-hash mismatch | push-failure | verification-failure | publication-failure | other`

### Blocker
- ...

### Evidence and work already performed
- <Commands, actual state and partial changes; do not imply no edits if edits occurred>

### Required decision or resume condition
- ...

### Scope compliance
- <Any unauthorized changes: none, or exact deviation disclosed>

Execution is blocked. The gate remains Not PASS.
```

If GitHub is unavailable, do not claim to have posted this template. Preserve it in a permitted local output location and return the publication-blocked outcome.

## Verification-blocked state

A state/access hold, not an implementation verdict or execution trigger.

```markdown
<!-- gated-development:state:v1 status=verification-blocked -->
## Gate state — VERIFICATION BLOCKED

- Gate: `<id>`
- Work-order version: `<n>`
- Evidence unavailable or unverifiable: `<what>`
- Last remotely verified SHA or baseline: `<SHA or N/A>`
- Resume condition: `<specific evidence or access needed>`

No PASS or implementation FAIL is issued while required evidence cannot be independently inspected.
```

## Superseded

```markdown
<!-- gated-development:state:v1 status=superseded -->
## Gate state — SUPERSEDED

- Gate: `<id>`
- Superseded by: `<new issue, document commit or gate>`
- Last accepted commit or baseline: `<SHA>`
- Reason: `<material architecture, scope, ownership or sequencing change>`

Do not continue implementation under this gate. Reconcile any already-running detached process separately; this comment does not claim to terminate it automatically.
```

## Cancelled

```markdown
<!-- gated-development:state:v1 status=cancelled -->
## Gate state — CANCELLED

- Gate: `<id>`
- Cancelled by: `<human or product owner>`
- Last accepted commit or baseline: `<SHA>`
- Reason: `<reason>`

No implementation from this gate is accepted unless separately reviewed and recorded. Do not continue under this authority. This state record does not itself prove that a detached process has stopped.
```

## Ready state

Use for a complete gate when execution is deliberately deferred.

```markdown
<!-- gated-development:state:v1 status=ready -->
## Gate state — READY

- Gate: `<id>`
- Work-order version: `<n>`
- Source-of-truth commit: `<SHA>`
- Original gate starting SHA: `<SHA>`

The work order is complete. Codex must not begin until a matching authorized activation is posted. No execution is requested by this READY record.
```

## History rules

Preserve old activation, evidence, review, correction and state comments. Link correction to reviewed evidence/activation and new evidence to its exact trigger. Do not convert a PASS comment into another state by editing it. Do not update issue-body checkboxes as a substitute for criterion-by-ID review. Keep parent tables as summaries.

After an ambiguous publication response, read before retrying. Do not post a second activation or correction simply because the first has not produced evidence yet.
