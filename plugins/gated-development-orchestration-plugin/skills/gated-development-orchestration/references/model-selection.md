# Model Selection and Runtime Reference

Model advice, runtime configuration, and permission to execute are different things. This reference does not select a model, change configuration, create agents or route a ChatGPT Chat review into another product.

## Current execution settings take precedence over old advice

For the existing automated AquaTwin path, retain the human's configured local Codex model/reasoning/authentication unless a current explicit instruction or controlling gate requires another setting. The inspected launcher invokes `codex exec` without a model override. Do not invent a flag or silently edit configuration to enforce an old model-name recommendation.

A recommendation is not proof of the model actually used. Report actual runtime settings only when exposed by a reliable, non-secret runtime source. Otherwise write `not exposed`; do not infer them from the skill text, task duration or subscription name.

If the active work order makes a model/runtime setting a mandatory requirement and it cannot be met or verified, report that mismatch before implementation rather than silently substituting. A historical advisory preference alone must not override the human's currently selected runtime.

## Recorded preference, not a CLI specification

The prior skill recorded a preference to recommend GPT-5.6 Sol modes for Codex (`high`, `xhigh`, `max`, `ultra`) and a Pro-capable ChatGPT Chat reviewer, with Extra High for minor follow-ups. Preserve that preference when asked for advice unless the human asks to reconsider it or specifies a different current choice.

These names are recorded user-facing preferences, not guaranteed valid model IDs, CLI flags, or portable reasoning values. Verify available settings before translating them into commands. In particular, a label `ultra` does not itself create or authorize multiple agents.

## Effort and escalation rubric

| Work shape | Recorded preference | Reason and escalation |
|---|---|---|
| Narrow routine implementation, isolated tests or documentation | `high` | Clear owner, limited paths, ordinary tests; escalate when behavior tracing is nontrivial |
| Bounded bridge, preservation-heavy work or correction | `xhigh` | Default advisory choice for careful source tracing and precise diff control; escalate for coupled ownership/architecture |
| Coupled architecture, migration or historical-integrity work | `max` | Keep the tightly coupled problem in one agent's view; task length alone is not a reason |
| Safely separable workstreams with explicit ownership/merge boundaries | `ultra` only where an actual authorized runtime supports it | Parallelism is a separate choice, not merely a higher scalar reasoning level |

Use the table as an effort rubric when the current selected runtime has different naming. Do not claim a mapping is supported without checking it.

Parallel work is appropriate only when explicitly allowed by the gate and runtime, with non-overlapping edit ownership and one accountable report. Do not spawn extra agents merely to follow this historical preference, bypass a worktree lock, or implement a later checkpoint. Coupled files or competing architecture decisions favor a single implementer.

## Recommendation format

Give one recommendation with a reason, not a menu of equally weighted options. For example:

> Recommended effort: careful single-agent execution, matching your recorded xhigh preference. This gate is bounded but requires behavior-preservation tracing. Keep the configured runtime unless you explicitly select another supported setting.

When a current model is explicitly selected, use its actual name without relabeling it as a historical preference. Do not repeatedly ask the human to choose a model already configured for the run.

## ChatGPT Chat / Pro review

Use the human-selected ChatGPT Chat / Pro conversation for orchestration and independent review in this workflow. Do not silently delegate review to ChatGPT Work, a Codex task, an API call, or the implementing session. Keep product/surface selection separate from model advice and do not infer quota/billing behavior from a skill.

A stronger model never replaces fresh remote inspection. A separate adversarial review session is optional when warranted; the implementer still must not accept its own work.
