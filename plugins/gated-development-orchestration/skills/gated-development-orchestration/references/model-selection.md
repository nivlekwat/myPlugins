# Model Selection and Runtime Reference

Model advice, runtime configuration, and permission to execute are different things.

## AquaTwin automated Codex default

The automated AquaTwin launcher defaults Codex reasoning effort to:

```text
model_reasoning_effort=max
```

unless the executable activation/correction explicitly supplies a supported reasoning-effort override.

The launcher default is mechanical runtime configuration. It does not decide scope, architecture, verification, repository operations, or acceptance.

When a case explicitly overrides reasoning effort, the case value takes precedence for that execution.

## Reporting runtime

Report actual runtime model/reasoning only when exposed by a reliable runtime source or by the launcher record. Do not infer it from task duration or skill prose.

A launcher-recorded `reasoning_effort=max` is evidence of the requested runtime setting, not evidence that the model reasoned correctly.

## Human/model preference

When the human explicitly selects a model or reasoning level, use that current instruction instead of historical advice.

Do not turn model recommendations into product authority. A stronger model does not replace remote inspection or acceptance criteria.

## ChatGPT independent review

Use the human-selected ChatGPT Chat / Pro conversation identified by the routing marker for independent review when the return workflow is configured.

Do not silently delegate the review back to the implementing Codex session, ChatGPT Work, or another unapproved surface.
