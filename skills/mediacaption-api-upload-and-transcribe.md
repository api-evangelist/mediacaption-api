---
name: upload-and-transcribe
description: Transcribe a local media file via multipart upload, with credit reconciliation, cancel and resume.
api: Media Caption Public API
operations: [createUpload, createUploadPartUrl, completeUpload, getUpload, cancelUpload, resumeUpload, getTranscription]
generated: '2026-09-03'
method: generated
source: openapi/mediacaption-api-openapi.yaml + https://www.mediacaption.io/docs/changelog
---

# Upload a local file for AI transcription

1. Start with `createUpload` (POST /v1/uploads) — credits are reserved from the estimated duration (AI transcription is 1 credit/minute; files up to 3 GB).
2. Request presigned part URLs with `createUploadPartUrl` (POST /v1/uploads/{id}/parts), upload the parts, then call `completeUpload` (POST /v1/uploads/{id}/complete). Incomplete multipart uploads are aborted after one day.
3. Poll `getUpload` (GET /v1/uploads/{id}) for `status`, `stage` and `progress`; after server-side duration measurement the difference against the reservation is charged or refunded.
4. To abort, call `cancelUpload` (DELETE /v1/uploads/{id}) before processing — reserved credits are refunded. If processing stalls on `insufficient_credits`, add credits and call `resumeUpload` (POST /v1/uploads/{id}/resume).
5. When complete, follow `transcriptionId`/`transcriptionUrl` to `getTranscription` and store the content before its 3-day expiry; completed source objects are deleted from Media Caption S3 after 30 days.
