# codeitall — Rust API behavioral oracle

Version-pinned behavioral oracle for Rust package APIs, built for AI coding
agents. Five deterministic tools backed by a Postgres substrate of
**66,549,370 `pattern_records`** and **107 successful probe runs** across
**7 task families** (datetime, duration, base64, JSON strict-vs-permissive,
JWT verify, AEAD encrypt/decrypt, constant-time compare).

**Hosted, free, no signup:** `https://api.codeitall.dev`

> This repository is codeitall's **public surface** — the API reference,
> OpenAPI spec, the "For AI Agents" page, and the per-family pages. The
> ingest/indexer and server source are **not redistributed**; you consume
> codeitall by querying the hosted API, not by running it yourself.

## What it answers

The differentiator from a docs RAG: the substrate captures **runtime
behavior** (what a function actually does on a canonical input set, observed
empirically), not just documentation (what it claims to do). It targets the
documentation-vs-runtime gap — yank status, RustSec advisories, deprecation
renames, and cross-implementation behavior tables.

## Tools

| Tool | Use it for |
|---|---|
| `answer_api_question` | Open-ended Rust API questions; routes deterministically (rule-based, no LLM) and falls back to bge-m3 cosine retrieval. |
| `signature_search` | You know the signature shape (`fn(&str) -> Result<Vec<u8>, _>`); get ranked candidate functions + crate, downloads, yank status. |
| `behavior_lookup` | A specific `(crate, fn_name)`; get the probe-observation table verbatim — each row `(input, outcome=ok\|err\|panic, value_or_error)`. |
| `compare_implementations` | A task category (e.g. "base64 crates"); get the side-by-side cross-implementation behavior table. |
| `find_modern_equivalent` | A suspected-deprecated call; get the current canonical alternative + yank/advisory rows. |

## Determinism contract

**No generative LLM sits between a request and its response.** Every
response is SQL + deterministic templating; the only model in the hot path
is **bge-m3**, a deterministic text→vector embedder used for the cosine
fallback (same input → same vector → same hits). This is a regression-pinned
invariant.

## Quickstart

```bash
# Mint a free anonymous key (no email, no signup):
curl -sX POST https://api.codeitall.dev/v1/keys \
  -H 'content-type: application/json' -d '{}'

# Ask a question (HTTP REST):
curl -sX POST https://api.codeitall.dev/v1/answer_api_question \
  -H 'content-type: application/json' \
  -d '{"question":"how do I parse a duration string in rust"}'
```

GET aliases exist for every tool (browse-only clients); `signature_search`
also accepts a `shape_b64` (base64url) parameter so URL re-encoding can't
mangle type-signature characters.

## Access modes

- **MCP** — SSE/HTTP at `https://api.codeitall.dev/mcp` (Bearer key), or stdio
  for self-hosters.
- **HTTP REST** — `POST`/`GET /v1/<tool>`.
- **OpenAPI 3.1** — [`openapi.json`](./openapi.json) (live: `https://api.codeitall.dev/openapi.json`).

## Docs in this repo

- [`for-ai-agents.html`](./for-ai-agents.html) — positioning, tool list, key snippet (live: `https://api.codeitall.dev/for-ai-agents.html`).
- [`api-reference.html`](./api-reference.html) / [`api-reference.md`](./api-reference.md) — generated tool reference + scope/coverage.
- [`agents/`](./agents) — one page per probe family.

## Cost & limits

Free. Per-IP key issuance cap (10/day) and per-key quota (10,000/day). No
paid tier, no email capture, no signup.
