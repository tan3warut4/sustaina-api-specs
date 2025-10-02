# File Attachment Management

## Overview
Position templates allow multiple file attachments. The API now supports a soft-delete lifecycle so edits can safely remove files while leaving a recovery window.

## Endpoints
- `POST /api/v1/position-templates/{positionId}/files/presign` – request upload URLs for new files.
- `POST /api/v1/position-templates/{positionId}/files/confirm` – finalize newly uploaded files.
- `DELETE /api/v1/position-templates/{positionId}/files/{fileId}` – **soft delete** a previously attached file. The response exposes when the file was marked deleted and, if applicable, when it will be purged permanently.

## Soft Delete Behaviour
1. The file remains in the database with `softDeleted = true` and is filtered from standard `GET` responses.
2. Storage cleanup is deferred until `deleteRetentionExpiresAt`; the backend can restore the file or purge it after that timestamp.
3. Subsequent delete requests return a 409 conflict if the attachment is already marked deleted.

## Client Flow
- When editing a position, compare the original `files[]` list with the current selection.
- For each removed file, call the DELETE endpoint and update the UI once the API confirms `softDeleted = true`.
- Newly added files still go through the presign + confirm sequence before submitting the overall position update.
- Optional: expose `deletedAt` and `deleteRetentionExpiresAt` in admin tooling to audit file removals.
