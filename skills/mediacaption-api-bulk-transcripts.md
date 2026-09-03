---
name: bulk-transcripts
description: Run a bulk YouTube transcript job with polling or webhooks, reconciling per-item credits.
api: Media Caption Public API
operations: [createBulkTranscriptJob, getJob, getTranscription]
generated: '2026-09-03'
method: generated
source: openapi/mediacaption-api-openapi.yaml + https://www.mediacaption.io/docs/webhooks
---

# Bulk public transcripts

1. Batch up to 100 videos into `createBulkTranscriptJob` (POST /v1/transcripts/bulk); give each item an `externalId` so results map back to your records. Acceptance freezes 1 credit per item; you may run at most 5 concurrent bulk jobs.
2. Either poll `getJob` (GET /v1/jobs/{id}) — paginate items with `limit`/`cursor` and follow `nextCursor` — or receive the final job-level webhook (`job.completed`, `job.completed_with_errors`, `job.failed`). Verify the `MediaCaption-Signature` (HMAC-SHA256 of `timestamp + "." + rawBody`) and dedupe on `MediaCaption-Event-Id`.
3. For each completed item, fetch content via `getTranscription` (GET /v1/transcriptions/{transcriptionId}) before its 3-day `expiresAt`. Failed items release their frozen credits (`creditsRefunded`); only completed items are charged.
4. There is no job-cancel operation in v1 — completed items cannot be reversed, so size jobs deliberately.
