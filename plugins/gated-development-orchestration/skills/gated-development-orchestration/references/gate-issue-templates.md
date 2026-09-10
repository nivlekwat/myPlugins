# Gate and Issue Templates

Use for new work. Preserve older frozen records as history.

## Parent feature issue

No ChatGPT thread ID is required in the parent issue body.

```markdown
<!-- gated-development:parent:v2 -->
# [AREA] Feature title

## Objective
...

## Source of truth
- Document: `<path>`
- Approved document commit: `<SHA>`
- Working branch: `<branch>`

## Gate plan
| Gate | Scope | Issue | State | Accepted commit or evidence |
|---|---|---|---|---|
| CP1 | ... | #... | READY | ... |
```

## Gate issue

The frozen gate body defines the case. Routing metadata normally stays out of the body.

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
- Original gate starting SHA: `<SHA>`
- Review diff base: `<same SHA>`
- Source-of-truth document: `<path>`
- Source-of-truth commit: `<SHA or case-specific bootstrap value>`
- Push policy: `agent | none`
- Required commit message: `<message or N/A>`
- Durable evidence destination: `<issue or authorized path>`

## Objective
...

## Authorized work
...

## Prohibited work
...

## Acceptance criteria
- `AC-1` — ...

## Required verification
1. ...

## Stop conditions
- ...
```

## Activation comment

When automated return review is desired, include exactly one thread marker immediately after the executable marker.

```markdown
<!-- gated-development:activation:v1 -->
<!-- gated-development:chatgpt-thread:v1 id=<current ChatGPT thread UUID> -->
## Gate activated

- Gate: `<id>`
- Work-order version: `1`
- Gate issue: `<canonical issue URL>`
- Gate body hash algorithm: `sha256`
- Gate body hash: `<digest>`
- Gate type: `implementation | documentation | analysis-only`
- Source-of-truth document: `<path or case-specific controlling source>`
- Source-of-truth commit: `<SHA or case-specific bootstrap value>`
- Required branch: `<branch>`
- Expected remote branch: `<remote>/<branch>`
- Original gate starting SHA: `<SHA>`
- Review diff base: `<same SHA>`
- Push policy: `agent | none`
- Activated by: `<human or authorized orchestrator>`

This activation authorizes execution and triggers the configured Codex launcher. No separate dispatch is required.
```

If automatic ChatGPT return review is intentionally not desired, omit the thread marker. Never invent one.

## Correction-required comment

```markdown
<!-- gated-development:review:v2 status=correction-required -->
<!-- gated-development:chatgpt-thread:v1 id=<current reviewer thread UUID> -->
## Independent gate review — NOT PASS / correction required

<complete bounded correction work order>
```

## Codex evidence

```markdown
<!-- gated-development:codex-evidence:v2 -->
<!-- gated-development:chatgpt-thread:v1 id=<copied exactly from trigger when present> -->
## Codex gate evidence
...
```

## Codex blocker

```markdown
<!-- gated-development:blocker:v2 -->
<!-- gated-development:chatgpt-thread:v1 id=<copied exactly from trigger when present> -->
## Gate blocked before completion
...
```

## PASS

```markdown
<!-- gated-development:review:v2 status=pass -->
## Independent gate review — PASS
...
```

## Verification blocked

```markdown
<!-- gated-development:review:v2 status=verification-blocked -->
## Independent gate review — VERIFICATION BLOCKED
...
```

## Routing rules

- Thread routing belongs to executable activation/correction comments, not necessarily the issue body.
- Codex copies the thread marker unchanged into evidence/blocker.
- PASS/state comments do not need the thread marker.
- Do not add a generic `Target: Codex` or `Target: ChatGPT` field. First-line marker plus thread marker is the routing contract.
- Preserve old comments; do not edit history to retrofit routing metadata.
