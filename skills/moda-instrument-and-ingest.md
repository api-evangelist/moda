---
name: moda-instrument-and-ingest
description: Instrument an LLM/agent app with the Moda SDK (or the Direct HTTP API) and send conversation events to Moda for analytics, threading, and failure detection.
api: https://docs.moda.dev/ingestion/direct-api
method: generated
source: https://docs.moda.dev/quickstart
operations: [moda.init, moda.flush, POST /v1/ingest]
---

# Instrument an LLM app and ingest conversation events

Send production agent/chat traffic to Moda so it can thread conversations, discover
intents, and detect behavioral failures. Grounded in the documented SDK quickstart
and the Direct Ingestion API.

## Auth

- Get an API key (prefix `moda_sk_`) from https://moda.dev/settings.
- SDK: pass it to `moda.init(...)`.
- Direct API: send `Authorization: Bearer <MODA_API_KEY>`.

## Path A — SDK (recommended)

Python:

```python
import moda
from openai import OpenAI

moda.init("YOUR_MODA_API_KEY")
moda.conversation_id = "session_123"   # thread key

client = OpenAI()
client.chat.completions.create(
    model="gpt-4o",
    messages=[{"role": "user", "content": "Hello!"}],
)
moda.flush()                           # flush before exit
```

TypeScript: `import { Moda } from 'moda-ai'`, then `await Moda.init(...)`,
`Moda.conversationId = 'session_123'`, and `await Moda.flush()`.

## Path B — Direct HTTP API

`POST https://moda-ingest.modas.workers.dev/v1/ingest` with a Bearer key:

```json
{
  "environment": "production",
  "events": [
    { "conversation_id": "conv-123", "role": "user", "message": "What is the capital of France?" }
  ]
}
```

Each event requires `conversation_id`, `role` (user|assistant|system), and
`message` (or `content`). Optional: `timestamp`, `trace_id`, `user_id`,
`input_tokens`, `output_tokens`, `model`, `provider`, `prompt_id`.

## Rules

- Group turns with a stable `conversation_id`.
- Success returns `{ success: true, count, requestId }`.
- On error, honor `retryable`: back off and retry `503`; do not retry `400/401/413`.
- Batch multiple turns in one `events[]` array; keep the payload under the `413` limit.

## Verify

Run `moda overview` (CLI) or check the dashboard for the first cluster.
