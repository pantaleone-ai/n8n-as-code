---
sidebar_position: 7
title: OpenClaw Plugin
description: Install the n8n-as-code OpenClaw plugin, bootstrap the workspace, and use OpenClaw with the same n8nac workflow model as the CLI and Claude plugin.
---

# OpenClaw Plugin

The `@n8n-as-code/openclaw-plugin` package gives OpenClaw native access to the same `n8nac` workflow model used across the rest of the project.

It is the right entry point when you want OpenClaw to:

- bootstrap an n8n workspace for you
- carry the generated `AGENTS.md` context into prompts automatically
- run workflow operations through the shared `n8nac` CLI and tool surface

## What It Adds

Once installed, the plugin gives OpenClaw:

- an `n8nac` tool for setup, workflow sync, validation, and skills access
- an `openclaw n8nac:setup` wizard for host, API key, and project selection
- automatic prompt grounding from the generated `AGENTS.md`
- an OpenClaw-native workspace rooted at `~/.openclaw/n8nac/`

## Install

Install the published plugin package:

```bash
openclaw plugins install @n8n-as-code/openclaw-plugin
```

Then run the setup wizard:

```bash
openclaw n8nac:setup
```

When setup completes, restart the gateway so the plugin and generated AI context are active:

```bash
openclaw gateway restart
```

## Setup Flow

The setup wizard walks through the same core steps as the CLI:

1. Save the n8n host and API key through `n8nac init-auth`.
2. Select the active n8n project.
3. Generate `AGENTS.md` with `n8nac update-ai`.
4. Point OpenClaw at the initialized workspace in `~/.openclaw/n8nac/`.

After that, you can ask for workflow work in plain language, for example:

- `Create an n8n workflow that sends a Slack message when a GitHub issue is opened`
- `Pull workflow 42 and add retry handling before the HTTP Request node`
- `What operations does the Google Sheets node support?`

## Workspace Layout

The plugin keeps its working files under:

```text
~/.openclaw/n8nac/
  n8nac-config.json
  AGENTS.md
  workflows/
```

- `n8nac-config.json` stores the selected project binding
- `AGENTS.md` contains the generated workflow instructions and schema-first guardrails
- `workflows/` holds the local `.workflow.ts` files you pull and edit

## Commands

### OpenClaw Commands

| Command | Description |
|---|---|
| `openclaw n8nac:setup` | Interactive setup wizard |
| `openclaw n8nac:status` | Check workspace and connection state |
| `openclaw gateway restart` | Reload the plugin after setup or local changes |

### Underlying n8nac Flow

The plugin still uses the shared CLI model underneath:

```bash
npx --yes n8nac list
npx --yes n8nac pull <workflow-id>
npx --yes n8nac push <file>
npx --yes n8nac update-ai
```

That keeps OpenClaw aligned with the CLI, VS Code extension, and Claude plugin instead of inventing a separate sync path.

## Local Development

If you are iterating on the plugin from this monorepo, link the local source tree instead of installing from npm:

```bash
openclaw plugins install --link /home/etienne/repos/n8n-as-code/plugins/openclaw/n8n-as-code
```

Then restart the gateway after code changes:

```bash
openclaw gateway restart
```

Useful checks while developing:

```bash
openclaw plugins info n8nac
openclaw n8nac:status
tail -f ~/.openclaw/logs/openclaw-$(date +%Y-%m-%d).log | grep n8nac
```

To reset the OpenClaw workspace and start over:

```bash
rm -rf ~/.openclaw/n8nac
openclaw n8nac:setup
```

## Related Guides

- [Getting Started](/docs/getting-started)
- [Claude Plugin](/docs/usage/claude-skill)
- [CLI Guide](/docs/usage/cli)
- [Skills Guide](/docs/usage/skills)