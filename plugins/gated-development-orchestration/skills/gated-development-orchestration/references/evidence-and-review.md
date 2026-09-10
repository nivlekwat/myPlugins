# Evidence and Independent Review Reference

Use when Codex reports, ChatGPT independently reviews, a blocker is triaged, or a correction is issued.

## Evidence is not acceptance

Actions success, CLI exit `0`, Codex summaries, and evidence URLs are not PASS. The reviewer independently fetches remote evidence.

## Codex evidence comment

When the triggering work order contains a ChatGPT thread marker, reproduce it exactly on the second line.

```markdown
<!-- gated-development:codex-evidence:v2 -->
<!-- gated-development:chatgpt-thread:v1 id=<copied exact UUID> -->
## Codex gate evidence

Status: Evidence posted. **Not PASS.**

### Work order
- Gate: `<id>`
- Gate type: `implementation | documentation | analysis-only`
- Work-order version: `<executed version>`
- Activation comment: `<actual URL>`
- Source-of-truth commit: `<SHA or case-specific equivalent>`
- Original gate starting SHA: `<original SHA>`
- Review diff base: `<same original SHA>`
- Correction review comment: `<URL or N/A>`
- Required execution starting SHA: `<initial/correction SHA>`

### Execution correlation
- Triggering comment: `<exact URL>`
- Actions run: `<actual run URL or N/A — manual>`
- Automation request ID: `<supplied ID or N/A — manual>`
- Skill package version used: `<actual>`
- Skill source used: `<actual>`
- Runtime model/reasoning observed: `<actual or not exposed>`

### Repository state
- Starting branch/SHA/worktree:
- Ending SHA:
- Expected remote branch:
- Push attempted/result:
- Remote availability:

### Changed files
- ...

### Implemented or analyzed scope
- ...

### Acceptance evidence by criterion
| Criterion | Evidence | Codex assessment |
|---|---|---|
| `AC-1` | ... | SATISFIED / NOT SATISFIED / NOT TESTED |

### Verification
| Command or inspection | Outcome | Notes |
|---|---|---|
| `...` | PASS / FAIL / NOT RUN | ... |

### Unresolved evidence or blockers
- ...

### Explicit confirmations
- No out-of-scope implementation: `YES | NO`
- No next-gate work: `YES | NO`
- Required verification complete: `YES | NO`

### Submission outcome
`COMPLETE EVIDENCE | PARTIAL EVIDENCE WITH BLOCKERS`

Awaiting independent review. Not PASS.
```

If no thread marker exists in the trigger, omit the routing line rather than inventing one.

## Blocker comment

```markdown
<!-- gated-development:blocker:v2 -->
<!-- gated-development:chatgpt-thread:v1 id=<copied exact UUID when present> -->
## Gate blocked before completion

- Gate: `<id>`
- Work-order version: `<version>`
- Triggering comment: `<actual URL>`
- Actions run: `<actual run URL or N/A — manual>`
- Automation request ID: `<request ID or N/A — manual>`
- Skill version/source: `<actual>`

### Blocker
- ...

### Work already performed
- ...

### Required decision or resume condition
- ...

Execution is blocked. The gate remains Not PASS.
```

## Independent review

A routed review request is only a pointer. Independently fetch:

1. exact evidence comment;
2. gate issue/body;
3. activation or correction;
4. pinned source-of-truth;
5. ending commit and expected remote branch;
6. original-base-to-ending range;
7. correction delta where applicable;
8. required verification evidence;
9. each immutable acceptance criterion.

Do not trust the evidence report's PASS-like statements without remote confirmation.

## Review outcomes

### PASS

```markdown
<!-- gated-development:review:v2 status=pass -->
## Independent gate review — PASS

- Gate: `<id>`
- Work-order version reviewed: `<n>`
- Activation comment: `<URL>`
- Reviewed evidence comment: `<URL>`
- Original review diff base: `<B>`
- Accepted ending commit: `<E or N/A for analysis-only>`
- Remote branch inspected: `<R or N/A>`
- Reviewer skill version/source: `<actual>`

### Acceptance criteria
| Criterion | Result | Evidence |
|---|---|---|
| `AC-1` | PASS | ... |

This checkpoint is accepted. A successor executes only through its own authorized activation.
```

PASS does not need a thread marker because it does not route implementation or review.

### Correction required

A correction-required comment is executable and should include the current reviewer thread marker when automated return review is desired.

```markdown
<!-- gated-development:review:v2 status=correction-required -->
<!-- gated-development:chatgpt-thread:v1 id=<current reviewer thread UUID> -->
## Independent gate review — NOT PASS / correction required

### Correction manifest
- Gate: `<id>`
- Gate type: `implementation | documentation | analysis-only`
- Correction work-order version: `<prior highest + 1>`
- Activation comment: `<URL>`
- Reviewed evidence comment: `<URL>`
- Required branch: `<same branch>`
- Expected remote branch: `<remote>/<branch>`
- Required correction starting SHA: `<reviewed ending SHA/baseline>`
- Prior reviewed ending SHA: `<same>`
- Original gate starting SHA: `<B>`
- Review diff base: `<B>`
- Source-of-truth document: `<path>`
- Source-of-truth commit: `<SHA>`
- Push policy: `agent | none`

### Blocking findings
1. ...

### Authorized correction
- ...

### Required verification
1. ...

This finalized correction is the execution authorization and trigger. No additional dispatch is required.
```

### Verification blocked

```markdown
<!-- gated-development:review:v2 status=verification-blocked -->
## Independent gate review — VERIFICATION BLOCKED

- Gate: `<id>`
- Work-order version under review: `<n>`
- Submitted evidence comment: `<URL or unavailable>`
- Last independently verified SHA/baseline: `<value>`

### Unavailable or unverifiable evidence
- ...

### Resume condition
- ...

No PASS or implementation FAIL is issued while required evidence cannot be independently inspected.
```

## Review routing boundary

The review workflow transports the request to ChatGPT. It does not perform the review. The reviewer must use fresh GitHub/remote evidence and may issue PASS, correction-required, or verification-blocked according to the case.
