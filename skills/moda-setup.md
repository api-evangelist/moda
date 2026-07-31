---
name: moda-setup
description: Provider-published Claude Code skill that detects your project stack and auto-integrates Moda's LLM observability SDK (moda-ai) into TypeScript and Python apps.
api: https://docs.moda.dev/ingestion/moda-sdk
method: searched
source: https://docs.moda.dev/ingestion/claude-code-skill
operations: [moda.init, moda.flush]
---

# Moda Setup (provider-published)

Moda ships a Claude Code skill named `moda-setup` that automatically wires the
`moda-ai` observability SDK into an existing project. This file references the
provider's own skill; install and run it from Moda rather than reproducing it.

## Install

Per-project (recommended):

```bash
mkdir -p .claude/skills
cp -r path/to/moda-setup/ .claude/skills/moda-setup/
```

Or add the Moda CLI agent skill:

```bash
npx skills add ModaLabs/moda-cli
```

## What it does

- Detects the framework (Next.js, Express, FastAPI, Flask, LangChain, CrewAI) and
  LLM provider (OpenAI, Anthropic, and others).
- Installs the SDK: `pip install moda-ai` (Python) or `npm install moda-ai` (Node).
- Places `moda.init("YOUR_MODA_API_KEY")` correctly for the detected framework,
  chooses a `conversation_id` strategy, and configures a `moda.flush()` cleanup hook.
- Sets up Vercel AI SDK telemetry where applicable.

## Activation

Ask Claude Code to "integrate Moda observability", or invoke `/moda-setup`.

Source: https://docs.moda.dev/ingestion/claude-code-skill
