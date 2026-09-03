---
name: fetch-youtube-transcript
description: Fetch a public YouTube transcript synchronously and handle billing, retention and rate limits correctly.
api: Media Caption Public API
operations: [createTranscript, getTranscription, getBalance]
generated: '2026-09-03'
method: generated
source: openapi/mediacaption-api-openapi.yaml + https://www.mediacaption.io/docs/quickstart
---

# Fetch a public YouTube transcript

1. Optionally check credit headroom with `getBalance` (GET /v1/balance). A single transcript freezes 1 credit and charges it only on success; `402 insufficient_credits` is returned before any processing.
2. Call `createTranscript` (POST /v1/transcripts) with `{ "url": "<YouTube URL>", "language": "en" }` and `Authorization: Bearer mc_live_...`.
3. Read `transcript[]` segments (`startSec`, `durationSec`, `text`) from the response and note `expiresAt` — content is retrievable via `getTranscription` (GET /v1/transcriptions/{id}) only for 3 days. Store what you need longer.
4. On `429 single_transcript_rate_limited` (limit: 10/min), wait `Retry-After` seconds and retry. Never auto-retry a timed-out POST: there is no idempotency key, and a duplicate request spends additional credits.
5. Source failures (`transcript_unavailable`, `video_unavailable`, `geo_restricted`, `youtube_blocked`, `youtube_rate_limited`) release the frozen credit; report them rather than retrying immediately.
