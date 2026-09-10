# Gated Development Orchestration 1.3.0

One shared instruction package for ChatGPT Chat / Pro orchestration and local Codex implementation. This is a replacement skill bundle, not a launcher installation, not an activated checkpoint, and not a separate Codex/ChatGPT workflow fork.

## Package layout

```text
gated-development-orchestration/
  SKILL.md
  README.md
  references/
    authority-and-lifecycle.md
    gate-issue-templates.md
    evidence-and-review.md
    automation-handoff.md
    model-selection.md
```

`SKILL.md` selects the task role and loads the applicable references. The same package belongs on both sides. Do not independently edit a "Codex version" and a "ChatGPT version".

## What changed from 1.2.0

The original source was inspected at AquaTwin commit `ad68da84a90e065b156d15f5bcb219eedc2190e0`.

- Explicit ChatGPT Chat / Pro reviewer versus local Codex implementer paths, without a second skill identity.
- Authorized activation/correction is the handoff. No extra dispatch, preauthorization record or manual Codex start after activation.
- Exact triggering comment/version/hash binding, not unbounded "latest comment" execution.
- Launch, execution, publication and acceptance are distinct facts.
- Terminal evidence, blocker and publication-failure responses return truthful report locations; a blocked gate is not described as complete.
- Existing local GitHub reporting, trigger/run correlation and bounded publication-only retry guidance.
- Analysis-only corrections use `none`/`N/A` and preserve the repository baseline instead of requiring a commit/push.
- Existing marker versions and automation-parsed field names retained; formatting requirements made explicit.
- Model preferences separated from configured runtime facts; no model, permission or authentication settings are changed.
- Local skill provenance, stale-copy checks and current launcher limitations documented.

The original source-of-truth, frozen-body, original review-base, remote-evidence, no-scope-expansion, mandatory authorized push and independent-review rules remain controlling.

## Local Codex deployment

Install the whole directory, including references, into the **actual worktree that will execute the checkpoint**:

```text
<checkpoint-worktree>/.agents/skills/gated-development-orchestration/
```

For a checkpoint using the main local AquaTwin worktree, that path is:

```text
C:\AquaTwin\AquaTwin\.agents\skills\gated-development-orchestration\
```

Prepare and commit the skill update through the normal authorized repository workflow **before freezing the next checkpoint's starting SHA**. Do not overwrite tracked files mid-checkpoint and then ignore the dirty worktree or silently change its base. Updating only dev does not update an already-checked-out feature worktree.

Verify the loaded `SKILL.md` path and metadata version `1.3.0`, and that the five reference files are present. Explicitly select the intended skill/source when duplicate installed names exist. Do not assume a user-level copy overrides or merges with a repository copy. Restart the client if its skill list has not refreshed.

The existing Actions workflow fetches the two PowerShell helpers, not this folder. This download does not change that behavior or any local client installation.

## ChatGPT Chat / Pro deployment

Use the same bundle in the client's available skill installation/update surface. Availability and accepted upload forms vary by account and surface; having a Pro conversation is not itself proof that native skill upload is enabled.

The accompanying all-in-one Markdown file is a generated reading/Project-reference copy of `SKILL.md` plus all five references. It is not a separately maintained reviewer skill. Where native skill installation is unavailable, attach that reading copy to the intended AquaTwinChat Project/conversation and explicitly ask ChatGPT to use it as the reviewer/orchestrator reference.

Do not upload only the main SKILL.md as a stand-alone reference while omitting its required reference files. Do not treat uploading a file as proof that an installed skill was replaced. Verify the version/source read in the next task. Updating the ChatGPT copy does not update local Codex, and the reverse is also true.

Suggested role selection for ChatGPT:

> Use Gated Development Orchestration 1.3.0 as the orchestrator and independent reviewer. Read current GitHub source and work-order records. Posting activation or a correction work order is executable; do not publish one without the applicable authority. Do not implement or delegate this review to Work/Codex.

The existing Codex launcher already selects the implementer role through its exact execution prompt. No second manually typed role prompt is required for each launch.

## Validation and rollout status

This release is an instruction/template update only. Its accompanying validation report covers UTF-8/frontmatter, package structure, internal links, marker compatibility and example manifests for implementation, documentation and analysis-only activation/corrections against the inspected parser shape. These are static checks, not proof of a live model following every instruction or of a complete production checkpoint run.

No new executable scripts, YAML workflow, service, queue worker, model override, GitHub credential, automatic ChatGPT return trigger or blanket checkpoint-advance authorization is included.

Before first use, deploy the intended version to both sides, verify the actual loaded worktree copy, and use one authorized bounded real checkpoint. Actions success remains only a startup acknowledgment. Inspect the resulting evidence and remote diff independently.

## Source provenance

Repository: `Aquanuity/AquaTwin`
Reviewed revision: `ad68da84a90e065b156d15f5bcb219eedc2190e0`

Original skill blobs read from GitHub:

| File | Git blob SHA |
|---|---|
| SKILL.md | `fdf2e95e2615cb66457143286f44af7b7782a7f0` |
| references/authority-and-lifecycle.md | `e093c9f426c6a46be95efac983469b64ccd40217` |
| references/gate-issue-templates.md | `99815108355fc86b7fd0e1256273b58e1ea88e8c` |
| references/evidence-and-review.md | `3d722f16f02c005f22422210ef4fe59f25322050` |
| references/model-selection.md | `6f758901568bf193b9bd453e9860392973c3b2d3` |

Launcher files inspected:
- `.github/scripts/Start-Checkpoint.ps1`, blob `5d7e8d81bfa38d3574f6c0063ac8278b014d2495`.
- `.github/scripts/Invoke-Checkpoint.ps1`, blob `d020e63c58a7d1cd8c6f1e6ad7fab3599195aa6d`.
- `.github/workflows/chatgpt-codex-dispatch.yml`, blob `47abbfeef62d5d23ea98ed20afb56f2fdfb1cd32`.

Canonical source locations and public loading documentation:
- https://github.com/Aquanuity/AquaTwin/tree/ad68da84a90e065b156d15f5bcb219eedc2190e0/.agents/skills/gated-development-orchestration
- https://github.com/Aquanuity/AquaTwin/tree/ad68da84a90e065b156d15f5bcb219eedc2190e0/.github/scripts
- https://developers.openai.com/codex/skills
- https://help.openai.com/en/articles/20001066-skills-in-chatgpt

Repository source content was read through the connected GitHub tools. The release was authored as a downloadable replacement, not committed or installed. Refresh the launcher comparison if those implementation files change.
