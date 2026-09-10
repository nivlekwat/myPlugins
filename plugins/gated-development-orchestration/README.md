# Gated Development Orchestration Plugin 1.3.0

This plugin wraps the existing **Gated Development Orchestration 1.3.0** skill without changing its workflow contract.

## Included

- `.codex-plugin/plugin.json` — plugin manifest
- `.app.json` — references the GitHub app used by the orchestration/review workflow
- `skills/gated-development-orchestration/` — the original uploaded skill bundle, unchanged

## Compatibility choices

- No MCP server is declared, so the plugin is not made Desktop-only merely by packaging.
- GitHub is referenced as an app instead of embedding a local MCP bridge.
- The skill content and its five references are preserved verbatim from the uploaded 1.3.0 bundle.

The GitHub app remains subject to the user's/workspace's normal connection and permission controls.
