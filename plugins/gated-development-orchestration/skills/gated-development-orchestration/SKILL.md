---
name: gated-development-orchestration
description: Coordinate checkpoint development through pinned source documents, frozen GitHub work orders, local Codex execution, and independent ChatGPT review. Use for gate creation, activation, implementation, evidence review, and bounded corrections. Choose the current role before acting; reading or reviewing this skill does not authorize a checkpoint or a GitHub write.
compatibility: Requires access to the complete skill references and relevant GitHub sources. Implementation requires an authorized local git worktree and its configured tools. ChatGPT Chat orchestration does not require access to the user's local filesystem.
metadata:
  version: "1.3.0"
  workflow: "github-codex-gated-development"
---

# Gated Development Orchestration

## Purpose and role selection

Use one shared workflow contract, not separately maintained Codex and ChatGPT variants.

**ChatGPT Chat / Pro normally acts as orchestrator and independent reviewer. Local Codex normally acts as implementer.** These are task roles, not privileges inferred from a model name, subscription, or GitHub username. An explicitly assigned read-only Codex review is possible, but a checkpoint implementation run must never promote itself to its own independent reviewer.

Select the role from the current human request and authorized execution context. A launcher-delivered activation/correction selects **implementer**. A request to review evidence selects **reviewer**. A request to discuss, review, or revise this skill does not activate product work. When the role is unresolved, remain read-only rather than guessing write authority.

| Role | Owns | Must not do |
|---|---|---|
| Human / product owner | Intent, scope, architecture decisions, activation authorization, cancellation and overrides | Nothing in this skill delegates final product authority away from the human |
| Orchestrator / reviewer | Source tracing, source-of-truth documents, issue topology, authorized activation, fresh remote review, correction orders, acceptance records | Treat Codex summaries or Actions success as proof; publish executable draft markers; silently replace ChatGPT Chat review with Work, Codex execution, or an API review |
| Codex implementer | Exact work-order preflight, bounded implementation or analysis, verification, authorized commit/push, evidence or blocker publication | Self-approve, close the gate, activate the next gate, emit an executable reviewer marker, expand scope, or run a return bridge without a separately authorized integration |
| Launcher / GitHub Actions | Validate and deliver an instruction; acknowledge process startup | Decide product scope, accept implementation, or substitute another work order |

GitHub is the durable coordination record. A shared GitHub identity is not proof of which role authored a comment. Skill instructions govern behavior; they are not an authentication or process-isolation boundary.

## References to load

Read only the references needed for the current phase, but do not skip a required contract:

- [Authority and lifecycle](references/authority-and-lifecycle.md): authority, freezing, exact hashing, gate types, ancestry invariants, state and discovered work. Required for activation, execution and review.
- [Gate and issue templates](references/gate-issue-templates.md): parent, gate, activation, amendment, blocker and state templates. Required before authoring those records.
- [Evidence and review](references/evidence-and-review.md): implementation evidence, review range, correction orders, PASS and verification blockers. Required for evidence publication or review.
- [Automation handoff](references/automation-handoff.md): executable markers, exact-comment binding, noninteractive reporting, duplicate/interruption handling and AquaTwin launcher compatibility. Required before automated activation, correction or execution.
- [Model selection](references/model-selection.md): recorded preferences versus actual runtime settings. Required when recommending or checking a model choice; it never grants scope or changes runtime configuration by itself.

Record the skill version and source actually read when reporting an automated run or its review. Do not claim a local, repository or installed copy was updated merely because a different copy changed.

## Core invariants

1. The pinned source-of-truth document defines feature meaning and architecture. The frozen gate body and matching activation authorize only the current slice.
2. Keep checkpoints as checkpoints. Corrections stay in the same gate while its objective, architecture and maximum path boundary remain valid.
3. A valid authorized activation is also the execution trigger. A valid correction work order is also the correction trigger. No separate dispatch, preauthorization record, or additional "start CP" message is required.
4. PASS accepts the current checkpoint. It does not itself authorize implementation of an unactivated checkpoint. Once the next checkpoint has its authorized activation, no additional manual handoff to Codex is needed. Use already-given explicit authorization; do not ask for it again or invent blanket future authority.
5. Codex implements and reports; the reviewer independently inspects remote evidence. Codex never issues its own independent PASS.
6. Preserve the original gate starting SHA and review diff base through every correction. Only the execution starting SHA changes for a correction.
7. Implementation/documentation: verify -> commit -> normal push to the authorized branch -> establish remote containment -> post evidence -> stop. Push policy is `agent`.
8. Analysis-only: no repository writes, commit or push, including in correction cycles. Push policy is `none`; accepted commit is `N/A`, with explicit baseline and durable evidence.
9. Do not merge, rebase, pull, reset, clean, force-push, switch worktrees or repair repository mismatches merely to get a run or push to succeed. A separately authorized repository operation must not be inferred from checkpoint activation.
10. Discovered cleanup, defects and future work are not automatically authorized. Required expansion is a blocker, not permission.
11. A launch acknowledgment, CLI exit code, posted URL, or evidence report is not acceptance. Distinguish delivery, execution, publication and review.
12. Preserve historical comments. Never fabricate SHAs, hashes, issue numbers, tool outcomes, credentials, model settings or verification results.

## Instruction precedence

After platform and safety instructions:

1. Current explicit human instruction.
2. Applicable repository-wide agent, contribution and safety instructions.
3. Source-of-truth document at its pinned commit.
4. Frozen gate body and matching activation.
5. Applicable approved amendment or versioned correction within those boundaries.
6. Repository source, tests, build output and runtime evidence.
7. Other comments, attachments and summaries.

A correction or amendment may clarify or narrow the controlling contract, not contradict or broaden it. If sources conflict, stop and identify the conflict. Reading an issue to locate a pinned document does not elevate the issue above that document. An automated invocation is bound to its exact trigger; newer controlling records may invalidate it, not silently retarget it.

## Orchestrator path: define and activate

### 1. Establish intent and source truth

Inspect the actual repository source and existing architecture documents. Separate current behavior from desired behavior. Define ownership, dependency direction, UI/API/host/trust boundaries, non-goals, verification and independently reviewable checkpoints. Ask only questions that materially affect correctness and have not already been answered.

For a strict bridge or refactor, preserve this rule:

> Existing behavior is the acceptance oracle. Discovered defects are documented or deferred, not repaired unless the gate explicitly authorizes that product change.

Write the agreed contract to a durable source-of-truth document and commit/push it before activating dependent implementation. Do not create work for Codex while material product or architecture decisions remain unresolved.

### 2. Choose issue topology and prepare the gate

Use one issue for one independently accepted outcome. Use one parent plus child checkpoint issues for a multi-gate feature. Normally create only the next executable child; preserve accepted history and link parent/child both ways. Follow an explicit request to pre-create the complete issue set without activating it.

Use the exact templates. Include gate type, work-order version, required branch/upstream, original starting SHA/review base, source-of-truth path/commit, push policy, commit message where applicable, maximum allowed paths, prohibited work, immutable acceptance IDs, verification and stop conditions.

### 3. Activate as the final execution-authorizing write

First establish that the human authorized this checkpoint and that its prerequisites are ready. Before a write, state the repository, issue/branch/path target and intended change. This announcement is not another approval request.

Fetch the final exact issue body, calculate its SHA-256, and post the complete activation as a **new comment**. Treat the body and activation as frozen. Posting the executable marker is a real handoff, not a draft preview. Use an ordinary discussion or non-executable READY state when work should not start.

Do not immediately append a controlling state/amendment just to acknowledge dispatch: it can make the queued activation stale. Necessary cancellation or supersession must still be recorded. The launcher handles delivery; the orchestrator does not also start a duplicate Codex session.

## Implementer path: execute one exact work order

### 1. Read and revalidate

Read applicable `AGENTS.md` and repository instructions, this skill and required references. Use the issue/activation only far enough to locate the pinned source of truth; read that document at the exact commit, then interpret the frozen gate.

For an automated run, identify the repository, gate issue, exact triggering comment URL, operation, work-order version, required branch/start, body/trigger hashes and launch correlation from the supplied context. Re-fetch the exact issue and comment using configured GitHub tools, verify both hashes, and verify the referenced activation and applicable comment history. Never select "latest work" instead of the supplied instruction.

Stop without editing when the body/trigger is edited or mismatched; the gate is closed, cancelled, superseded or already accepted; a later controlling instruction invalidates the run; or scope/architecture is unresolved. Manual execution must establish an equally specific current work order and must not compete with an automated run already executing it.

Before editing, verify the actual worktree, exact branch, execution starting SHA, clean state, upstream, ahead/behind and relevant live remote state. Inspect the required source/tests and the original-base ancestry. Launcher checks do not replace these checks. Do not edit launcher request files, claims, locks or scripts to bypass a rejection.

### 2. Implement or analyze, then verify

Perform only the authorized gate or correction. Maximum allowed paths are limits, not a to-do list. Add only authorized tests and run the exact verification. Preserve native behavior for bridge/refactor work. Review the complete original-base-to-ending diff for drift and the correction-only delta when applicable.

For analysis-only, keep the repository unchanged and place new findings in the permitted issue evidence destination. Linking an existing report is allowed; creating or editing a repository report requires a documentation gate.

### 3. Commit/push where applicable and publish

For implementation/documentation, perform the required commit and normal authorized push only after successful required verification. Establish that the ending SHA is remotely inspectable on the declared branch before publishing normal final evidence. A failed verification/push gets truthful partial evidence or a blocker, not a success claim; do not repair unrelated failures or synchronize the repository without authorization.

Post one terminal evidence **or** blocker for this execution using the existing markers. Include the exact trigger, launch run, executed work-order version and skill version/source. Use existing local GitHub authentication; do not create new credentials, expose secret files, or depend on an expired Actions token.

Confirm publication from the tool response and preferably read it back. Check for an existing matching report before retrying an ambiguous publication. A report-only retry must not repeat the implementation. If GitHub publication is unavailable, preserve the report in the authorized local run/output location where available and explicitly say no comment was posted.

Return the report URL and a truthful final state, then stop. Do not invoke the ChatGPT return bridge or another orchestration process under this skill. That integration remains separate.

## Reviewer path: inspect, correct or accept

Load the evidence-and-review contract and perform fresh remote inspection. Correlate the submitted report to the exact gate, activation/correction and ending SHA. Confirm the frozen body hash, pinned source, remote containment, original-base ancestry, full gate range, every commit/path, architecture, preserved behavior, required tests and each acceptance ID. Inspect the correction delta separately without replacing the original review base.

Use implementation FAIL / correction-required only for independently established in-scope defects or unmet required checks. Missing remote source, inaccessible evidence or uncertain ancestry means `VERIFICATION_BLOCKED`, not an invented code defect. An analysis-only review still verifies baseline identity, evidence and the no-write boundary; a commit/diff criterion being `N/A` is not permission to ignore unauthorized writes.

For an executable correction, finalize all findings and fields before posting its existing correction-required marker as a **new comment**. Increment the work-order version beyond prior issued versions; link activation, reviewed evidence and the prior correction; set the exact reviewed ending SHA as correction start. Preserve the original gate base. Do not create a correction merely to solve an access or publication problem. A complete authorized correction directly triggers execution, without another dispatch or manual Codex handoff.

For PASS, record the accepted original-base-to-ending range and remote ending commit, or accepted analysis evidence and baseline. Evaluate every acceptance ID. Update the parent summary, preserve deferred discoveries and close the gate only when allowed. Prepare/activate the next checkpoint according to the approved sequence and human activation authority; never let Codex choose or self-activate it. Do not close the parent until all required gates and final live verification are accepted.

## Required terminal outcomes

For an actual orchestration/review task, report one primary state with evidence: `DRAFT`, `READY`, `BLOCKED`, `VERIFICATION BLOCKED`, `NOT PASS — correction required`, `PASS — accepted commit <actual SHA>`, or `PASS — analysis evidence <actual reference>`. A skill-file review is not itself a product-gate verdict.

For a completed Codex evidence submission:

```text
Evidence posted. Awaiting independent review. Not PASS.
Report: <actual posted evidence comment URL>
```

For a blocked Codex execution:

```text
Blocked: <concise reason>. Not PASS.
Report: <actual posted blocker comment URL>
```

For unavailable publication:

```text
Report publication blocked. No GitHub comment was posted. Not PASS.
Local report: <actual saved path, or explicitly unavailable>
```

Never say "gate complete" when blocked. Never fabricate a URL to satisfy the launcher. Do not include the trigger URL as if it were the result URL.
