# Evidence and Independent Review Reference

Use when Codex reports, the reviewer inspects a gate, a push/verification/publication attempt is blocked, or a correction begins. Automated work also requires automation-handoff.md.

## Evidence is not acceptance

Distinguish delivery, process execution, report publication and independent acceptance. Actions success, CLI exit `0`, an agent's summary, and a URL are not PASS. Independently fetch the reported comment and relevant remote sources.

Evidence distinguishes commands run/not run, passing/failing checks, environmental/implementation failures, unchanged baseline/new failures, exercised/unexercised live behavior, remote/push-blocked local commits, and initial/correction work.

For implementation/documentation, the standard sequence is verify -> commit -> normal authorized push -> confirm remote containment -> evidence -> stop. Failed verification or push does not authorize source synchronization, history rewriting, unrelated fixes or fabricated completion. Analysis-only evidence and its corrections make no repository writes.

## Redaction

Do not publish credentials, tokens, passwords, private keys, secret-bearing certificates/connection strings, customer or regulated data, complete environment/configuration dumps, or unnecessary sensitive machine details. Redact values while preserving useful command shape and outcomes. Identify a required secret by name only, never its value.

A relevant skill path or worktree reference may be supplied when needed to explain a mismatch; avoid unnecessary user-profile detail. Report actual model settings only when available without reading/exposing secret configuration.

## Codex evidence comment

Use one terminal report per execution. This template supports both complete and partial evidence; select truthful values rather than asserting success automatically.

```markdown
<!-- gated-development:codex-evidence:v2 -->
## Codex gate evidence

Status: Evidence posted. **Not PASS.**

### Work order
- Gate: `<id>`
- Gate type: `implementation | documentation | analysis-only`
- Work-order version: `<executed version>`
- Activation comment: `<actual URL>`
- Gate body hash verified: `YES | NO`
- Source-of-truth commit: `<SHA>`
- Original gate starting SHA: `<original SHA>`
- Review diff base: `<same original SHA>`
- Correction review comment: `<actual URL or N/A>`
- Required execution starting SHA: `<initial or correction SHA>`

### Execution correlation
- Triggering comment: `<exact activation/correction URL>`
- Actions run: `<actual run URL or N/A — manual>`
- Automation request ID: `<supplied ID or N/A — manual>`
- Skill package version used: `<actually loaded version>`
- Skill source used: `<actual path or pinned source reference>`
- Runtime model/reasoning observed: `<actual observed values or not exposed>`

### Repository state
- Starting branch: `<branch>`
- Starting SHA: `<SHA>`
- Starting worktree: `<clean or details>`
- Upstream and ahead/behind: `<actual details>`
- Ending SHA: `<SHA or N/A for analysis-only; state unchanged baseline separately>`
- Ending worktree: `<clean or details>`
- Expected remote branch: `<remote>/<branch>`
- Push attempted: `YES | NO | N/A`
- Push result: `PASS | FAILED | NOT ATTEMPTED | N/A`
- Remote availability: `REMOTE | LOCAL ONLY DUE TO PUSH FAILURE | NOT ESTABLISHED | N/A`

### Changed files
- `<actual path>`
- `N/A — analysis-only` when no repository write occurred

### Implemented or analyzed scope
- ...

### Acceptance evidence by criterion
| Criterion | Evidence | Codex assessment |
|---|---|---|
| `AC-1` | ... | SATISFIED / NOT SATISFIED / NOT TESTED |

### Behavior and architecture preservation
- ...

### Verification
| Command or inspection | Outcome | Notes |
|---|---|---|
| `...` | PASS / FAIL / NOT RUN | ... |

### Baseline or environmental failures
- ...

### Live behavior not exercised
- ...

### Redactions
- `<category redacted, or none>`

### Unresolved evidence or blockers
- ...

### Explicit confirmations
- No out-of-scope implementation: `YES | NO`
- No next-gate work: `YES | NO`
- No unauthorized file changes: `YES | NO`
- Accepted earlier work untouched: `YES | NO`
- Required agent-push policy followed: `YES | NO | N/A`
- Analysis-only no-write/no-push policy followed: `YES | NO | N/A`
- Required verification complete: `YES | NO`

### Submission outcome
`COMPLETE EVIDENCE | PARTIAL EVIDENCE WITH BLOCKERS`

Awaiting independent review. Not PASS.
```

Ordinary final write-gate evidence follows a verified push. On push failure, a blocker or partial evidence may identify the actual local ending SHA and `LOCAL ONLY DUE TO PUSH FAILURE`; final acceptance remains verification-blocked until remote reviewability is restored deliberately. On failed checks, describe exact failures and work performed instead of filling every field with PASS.

For analysis-only, identify the inspected baseline and no-write evidence. Do not create a commit solely to satisfy a report template. Do not claim a byte-for-byte audit if only HEAD/branch/status/tracked-diff fingerprints were compared.

## Publication and final response

Publish using the implementer's configured GitHub tools. Confirm the API/tool result and read back when available. Include trigger/run correlation in the comment. The final CLI answer contains the actual result URL, not just the triggering comment or a claim of completion.

```text
Evidence posted. Awaiting independent review. Not PASS.
Report: <actual posted evidence URL>
```

For a blocker, use the blocker template and return:

```text
Blocked: <actual reason>. Not PASS.
Report: <actual posted blocker URL>
```

For a failed publication, preserve a local report in the authorized output location where available and return:

```text
Report publication blocked. No GitHub comment was posted. Not PASS.
Local report: <actual saved path, or unavailable>
```

Do not claim gate completion when blocked. Do not fabricate URLs. Before repeating a timed-out publication, check for an existing report matching the gate/version/trigger/run. Retry only publication, not implementation. A killed process may leave only local logs; the reviewer must not interpret silence as no work.

## Review range and ancestry

For a write gate, define `B` = original review base; `E` = submitted ending SHA; `R` = expected remote branch; `P` = prior reviewed ending SHA for a correction, otherwise N/A.

Establish all of the following from actual remote evidence:

1. `B` and `E` belong to the intended repository and `E` exists remotely.
2. `R` contains `E`.
3. `B` is an ancestor of `E` and their merge base is `B`.
4. Every commit in `B..E` belongs to the gate or an explicitly approved repository operation.
5. Inspect the full `B...E` diff after the ancestry check, not only the last commit.
6. Derive all changed paths from that full gate range.
7. For a correction, `P` is an ancestor of `E`; inspect `P...E` for the authorized repair only.
8. No unauthorized rebase, reset, force-push, unrelated merge or history rewrite changed the lineage.

Do not compare the entire feature branch against dev when earlier gates already exist. If ancestry or baseline cannot be established, stop at verification-blocked or obtain the required lineage decision rather than inventing a convenient range.

For analysis-only, no new commit/diff is required, but actual baseline, evidence, no-write compliance and substantive acceptance must still be established.

## Mechanical review gates

Judge these before style/elegance:

1. Activation/body/trigger integrity and correct current work-order version.
2. Remote evidence and expected branch containment, or justified no-write N/A for new commits.
3. Correct original-base ancestry, complete range, and correction delta.
4. Only authorized paths changed; no-write behavior actually preserved where required.
5. Scope limited to this checkpoint/correction.
6. Behavior matches preserved and expressly changed contracts.
7. Ownership and dependency direction match pinned architecture.
8. Required verification was truthful, sufficient and actually performed.
9. Every immutable acceptance ID has an evidence-backed outcome.
10. No next-checkpoint or speculative work began.
11. Evidence is useful without secrets or unwarranted claims.
12. Submitted report corresponds to the execution being reviewed; not an older result with the same branch name.

A polished implementation fails acceptance when a mandatory gate fails.

## Outcome selection

**PASS:** all mandatory checks/criteria pass.

**CORRECTION_REQUIRED / NOT PASS:** independently established implementation, behavior, scope, path or required-verification defect is repairable within the gate. The executable correction is a complete, bounded work order, not a placeholder review finding.

**VERIFICATION_BLOCKED:** required evidence cannot be obtained/trusted, such as a local-only ending SHA, inaccessible remote branch, body-hash mismatch, unknown ancestry, broken evidence link or unavailable required artifact. An access failure is not automatically a code defect. Where an actual implementation defect is independently established too, describe both facts without pretending the full review is complete.

**BLOCKED:** human, product, architecture, repository-state, push or dependency decision is needed before/outside merits review.

Do not issue a correction marker merely to repair delivery, missing access or report publication. When a required check actually was not performed, distinguish missing test execution from inability to retrieve evidence that it ran.

## Independent PASS — implementation/documentation

```markdown
<!-- gated-development:review:v2 status=pass -->
## Independent gate review — PASS

- Gate: `<id>`
- Gate type: `implementation | documentation`
- Work-order version reviewed: `<n>`
- Activation comment: `<actual URL>`
- Reviewed evidence comment: `<actual URL>`
- Triggering work-order comment: `<actual activation/correction URL>`
- Gate body hash: `PASS — <digest>`
- Source-of-truth commit: `<SHA>`
- Original review diff base: `<B>`
- Accepted diff range: `<B>...<E>`
- Accepted ending commit: `<E>`
- Remote branch inspected: `<R>`
- Prior reviewed ending SHA: `<P or N/A>`
- Correction delta inspected: `<P>...<E> or N/A`
- Reviewer skill version/source: `<actually read version/reference>`

### Mechanical gate results
- Activation integrity: PASS
- Remote evidence: PASS
- Ancestry and range: PASS
- Path boundary: PASS
- Scope: PASS
- Behavior: PASS
- Architecture: PASS
- Verification: PASS
- No future-gate leakage: PASS
- Evidence redaction and correlation: PASS

### Acceptance criteria
| Criterion | Result | Evidence |
|---|---|---|
| `AC-1` | PASS | ... |

### Notes and deferred findings
- ...

This checkpoint is accepted. A successor executes through its own authorized activation, not from this PASS marker alone. No extra manual Codex start is needed after that activation.
```

## Independent PASS — analysis-only

```markdown
<!-- gated-development:review:v2 status=pass -->
## Independent gate review — PASS

- Gate: `<id>`
- Gate type: `analysis-only`
- Work-order version reviewed: `<n>`
- Activation comment: `<actual URL>`
- Triggering work-order comment: `<actual activation/correction URL>`
- Gate body hash: `PASS — <digest>`
- Source-of-truth commit: `<SHA>`
- Repository baseline inspected: `<SHA>`
- Accepted commit: `N/A`
- Accepted evidence comment or report: `<actual reference>`
- New-commit remote containment: `N/A — no-write gate`
- New-commit ancestry and diff: `N/A — no-write gate`
- Baseline identity and evidence access: `PASS`
- No-write boundary: `PASS — <evidence>`
- Reviewer skill version/source: `<actually read version/reference>`

### Acceptance criteria
| Criterion | Result | Evidence |
|---|---|---|
| `AC-1` | PASS | ... |

### Notes
- ...

This analysis-only checkpoint is accepted. The next checkpoint still requires its own authorized activation; no additional manual Codex handoff follows a valid activation.
```

## Independent correction work order

This comment both records the independent failed review and authorizes/triggers its narrow correction. Finish the work order before posting its marker. Use a version greater than all previously issued gate work-order versions; keep the body frozen.

```markdown
<!-- gated-development:review:v2 status=correction-required -->
## Independent gate review — NOT PASS / correction required

### Correction manifest
- Gate: `<id>`
- Gate type: `implementation | documentation | analysis-only`
- Correction work-order version: `<prior highest issued version + 1>`
- Activation comment: `<canonical same-issue activation URL>`
- Reviewed evidence comment: `<actual URL>`
- Required branch: `<same authorized branch>`
- Expected remote branch: `<remote>/<branch>`
- Required correction starting SHA: `<reviewed ending SHA or unchanged analysis baseline>`
- Prior reviewed ending SHA: `<same correction starting SHA>`
- Original gate starting SHA: `<original SHA>`
- Review diff base: `<same original SHA>`
- Source-of-truth document: `<same pinned document path>`
- Source-of-truth commit: `<same pinned SHA>`
- Push policy: `agent | none`
- Required correction commit message: `<message or N/A for analysis-only>`
- Supersedes correction comment: `<actual prior correction URL or none>`

### Blocking findings
1. **Finding**
   - Acceptance criterion or gate:
   - Source evidence:
   - Why it blocks:

### Authorized correction
- ...

### Unchanged boundaries
- ...

### Prohibited expansion
- ...

### Required verification
1. ...

This finalized correction is the execution authorization and configured launch trigger; no additional dispatch is required. Do not begin the next gate. Start only at the required correction SHA and retain the original review base. For implementation/documentation: verify, commit, normal-push the authorized branch, establish remote containment, then post new evidence and stop. For analysis-only: preserve the repository baseline, make no repository writes/commit/push, post corrected analysis evidence and stop. Return the actual terminal report URL.
```

For implementation/documentation fill `Push policy: agent` and the required commit message. For analysis-only fill `Push policy: none`, commit message `N/A`, and both correction-start fields with the unchanged inspected baseline. A corrected analysis is a new evidence submission, not a reason to invent a commit.

The correction start changes execution only; original gate base and source pin remain fixed. Never restart numbering or change the base to bypass claims or hide earlier work.

## Independent verification-blocked review

```markdown
<!-- gated-development:review:v2 status=verification-blocked -->
## Independent gate review — VERIFICATION BLOCKED

- Gate: `<id>`
- Work-order version under review: `<n>`
- Activation comment: `<actual URL>`
- Submitted evidence comment: `<actual URL or unavailable>`
- Last independently verified SHA or baseline: `<SHA or N/A>`
- Submitted ending SHA or evidence: `<SHA/reference or N/A>`

### Unavailable or unverifiable evidence
- ...

### Inspection attempted
- ...

### Why no merits verdict is possible
- ...

### Resume condition
- <Specific remote commit, branch, body reactivation, access, artifact or evidence required>

No PASS or implementation FAIL is issued while required evidence cannot be independently inspected. This comment is not a code-correction launch instruction.
```

Preserve this record when evidence later becomes available; issue a new review instead of rewriting history.

## Provisional assessments and write-back

Label a partial assessment provisional. State exactly what was and was not independently inspected. End verification-blocked when evidence prevents a complete verdict; do not imply PASS.

After PASS, update the parent table with accepted commit/evidence, close the gate only when conventions/authority permit, and record deferred defects separately. Prepare the next gate from accepted source according to the approved plan. Activation, not a second manual "start" message, is its execution handoff. Never edit old review comments to make later history appear linear.
