# Automation Handoff Reference

This is the shared behavior contract for ChatGPT Chat orchestration and local Codex implementation. It describes the AquaTwin launcher inspected at `ad68da84a90e065b156d15f5bcb219eedc2190e0`; it does not install or change that launcher.

## Ownership and executable comments

Activation is both the authorization record and the trigger. A correction work order is both the independent review decision and the trigger for that narrow correction. There is no separate dispatch comment, preauthorization policy object, or manual "start CP2" command after an authorized activation.

| First-line marker | Authoring role | Effect in the inspected launcher |
|---|---|---|
| `<!-- gated-development:activation:v1 -->` | Authorized orchestrator / human | Validate and launch this gate |
| `<!-- gated-development:review:v2 status=correction-required -->` | Independent reviewer | Validate and launch this correction |
| `<!-- gated-development:codex-evidence:v2 -->` | Implementer | Report only; does not launch another Codex |
| `<!-- gated-development:blocker:v2 -->` | Implementer | Blocker only; does not launch another Codex |
| `<!-- gated-development:review:v2 status=pass -->` | Independent reviewer | Acceptance only; does not activate the next gate |
| `<!-- gated-development:review:v2 status=verification-blocked -->` | Independent reviewer | Evidence/access hold; not a code-correction instruction |
| Amendment or state marker | Appropriate authorized role | Records state; not an independent execution trigger |

These format versions are independent: skill package `1.3.0`, activation format `v1`, review/evidence format `v2`, and a gate's numeric work-order version do not count the same thing. Advancing CP1 to CP2 or issuing another correction does not change the marker version. An existing marker or parsed field must not be renamed without a coordinated launcher change.

## Orchestrator rules

Draft non-executable discussion first; publish the activation/correction marker only when its complete work order is authorized and ready to execute. Do not put a test marker at the beginning of a real checkpoint comment. READY is not activation.

The inspected Action responds to newly created issue comments, not edits, PR discussion comments, or historical comments. It checks the repository and configured account. An edit does not repair a dispatched instruction and may invalidate it. Deliberately replace/supersede the instruction when appropriate; do not delete history.

An activation/correction posting timeout is an uncertain delivery, not proof of failure. Search/read the issue for the exact intended work order before posting again. Do not create another executable comment merely because no report has arrived yet. Do not start Codex manually in parallel after posting an automated activation.

Use existing human authorization for the checkpoint; do not request a duplicate approval. Preserve normal checkpoint boundaries. A PASS record alone does not supply an activation for an unactivated successor. Do not add a blanket advancement-authorization artifact. When the next activation is authorized and ready, post it and let the existing launcher perform the handoff.

## Exact input binding

The execution context identifies:

- repository and checkpoint issue;
- exact triggering comment URL/ID and recognized operation;
- numeric work-order version;
- selected worktree, branch and required execution start;
- frozen issue-body SHA-256 and triggering-comment SHA-256;
- launch run ID, request ID and local request path where supplied.

Treat the request as data, not executable shell text. Re-fetch the exact GitHub records with existing tools, compute SHA-256 over the exact UTF-8 strings, and verify every binding before editing. Do not trim or normalize text before hashing. Stop on an edited/missing trigger, hash conflict or stale/cancelled/superseded/accepted gate. A newer controlling record invalidates this run when applicable; it does not silently become its replacement task.

Read applicable amendments and the complete relevant history. A correction must reference the matching activation on the same gate, a reviewed evidence package and the prior reviewed ending SHA. Check increasing versions against the relevant history, not just against initial version 1.

Repository state, behavior, ownership and scope must be verified independently of launcher validation. A downloaded request is not an architecture review or proof that the currently selected skill is the expected version.

## What Actions success means

The architecture is:

```text
Authorized activation/correction -> Action -> detached one-shot Codex process
                                      |
                                      +-> startup acknowledgment -> Action ends
Detached Codex -> verification and required push -> evidence/blocker -> exit
ChatGPT Chat -> independent remote review
```

Startup acknowledgment means the launcher observed startup, not that all Codex preflight checks passed or that implementation began. CLI exit `0` means the process exited successfully, not that the checkpoint passed. A final URL is a report claim until independently retrieved. Only the independent review can accept the gate.

No continuously running queue worker is required. The one-shot wrapper may wait for its Codex process while Actions is already finished. The skill must not introduce another launcher, service, polling daemon or worker thread.

## Reporting and recovery

Codex prepares and posts its own evidence/blocker using its existing local GitHub tools/authentication. Keep report markers non-executable. Include correlation fields from the evidence template; use `N/A — manual` only for values truly absent in a manual execution. Report only actually observed runtime and skill provenance.

The final CLI response must contain the actual terminal report URL. Do not return only "done", the triggering comment URL, or a local path while claiming publication succeeded. A blocker is a legitimate completed agent turn, not successful implementation.

Attempt one terminal evidence or blocker per execution. On an ambiguous comment API response, check for the report by gate, work-order version and trigger/run identity before a bounded publication retry. Do not rerun implementation to repair reporting. When reporting is unavailable, say so explicitly and preserve recoverable output in the supplied run directory when allowed.

For a missing report or an interrupted process, inspect available run records and repository state before authorizing any retry. Do not delete claim/lock files, reset source, increment a version merely to defeat duplicate prevention, or silently resume an arbitrary session. Re-execution requires a reconciled, current work order. Recovery instructions are not a new dispatch layer.

The launcher may fail before Codex starts, and a terminated process cannot be expected to post its own blocker. A missing comment is not proof that no files changed. The inspected wrapper records launch/execution errors locally; automatic publication of all launcher failures is not implemented by these skill instructions.

## Existing parser compatibility

For automation, author manifest fields exactly as `- Field name: value`, with one occurrence per required field in that document/comment. Optional surrounding backticks on values are allowed. Do not bold labels, substitute tables, use short SHAs, or change field names. Use full 40-character lowercase commit SHAs and 64-character lowercase SHA-256 values.

Copy these fields consistently across gate body, activation and correction: `Required branch`, `Expected remote branch`, `Original gate starting SHA`, `Review diff base`, `Source-of-truth commit`, and the appropriate `Push policy`. `Gate ID` in the body equals `Gate` in activation/correction. Keep the source-of-truth document path consistent too, even where the current parser leaves that check to Codex.

An activation's `Work-order version` equals the frozen body's initial version. A correction uses `Correction work-order version` greater than prior issued work-order versions; do not rewrite the frozen body to reflect it. Its `Required correction starting SHA` equals `Prior reviewed ending SHA`. Both point to the submitted implementation ending SHA, or the unchanged baseline for an analysis-only correction.

Use the raw canonical GitHub comment URL as the `Activation comment` value, optionally inside backticks, not a Markdown label. The current parser validates that URL form. All older comments remain historical; do not rewrite a frozen gate simply to make it parse.

## AquaTwin deployment-specific constraints

The inspected entrypoint is `.github/workflows/chatgpt-codex-dispatch.yml`, using `.github/scripts/Start-Checkpoint.ps1` and `.github/scripts/Invoke-Checkpoint.ps1`.

It finds an already-registered worktree for the required branch through the configured project root. It requires a clean worktree, matching starting SHA and upstream, and a live remote branch tip equal to the start. This is stricter than merely finding the commit somewhere on the remote. It does not create/switch worktrees or synchronize branches. Prepare a missing worktree separately before activation; do not loosen the work order to conceal a mismatch.

It uses per-request claims and a worktree lock for automated executions. These do not prevent an unrelated manual editor from changing the same files; avoid concurrent manual implementation in that worktree. Existing claims are not a general-purpose durable restart queue.

The Action fetches its two helpers from its workflow commit. It does **not** fetch or synchronize the repository skill folder. The implementer must read the actual worktree's skill. Installing this package only on `dev` or only in ChatGPT does not update other worktrees.

Historical test issue #299 is restricted to the fixed connectivity-test route and is not a real checkpoint. Never replay its synthetic requests as implementation work. Do not copy test-only fields to real gates.

The return bridge into ChatGPT is not enabled by this contract. Do not invoke it or claim that an evidence comment automatically wakes ChatGPT. A later expressly authorized integration can consume evidence without changing who approves work.

Cancelling an already-finished Action or posting a gate cancellation is not a demonstrated process-stop mechanism for detached Codex. Treat the cancellation as authoritative when observed; use an explicit reconciled process-stop procedure where needed. Do not promise automatic interruption that has not been implemented/tested.

## Boundaries this skill cannot enforce

Role-specific instructions do not cryptographically distinguish ChatGPT from Codex when both publish as the same GitHub account. Detachment is not a security sandbox. Loaded skill versions, permissions, stop handling, duplicate prevention and local process lifetime require actual client/launcher support. Never claim that editing Markdown implemented those capabilities.
