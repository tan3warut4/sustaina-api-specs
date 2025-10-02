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

## Client Flow for Edit
1. Fetch the current position with `GET /api/v1/position-templates/{id}` to hydrate the form and capture the existing `files[]` snapshot.
2. Retrieve date constraints with:
   - `GET /api/v1/position-templates/{id}/children/allowed-terms`
   - `GET /api/v1/position-templates/parents/{parentId}/allowed-terms` when a parent exists.
3. Let the user adjust metadata and attachments:
   - **Keep**: leave existing files untouched; they remain linked automatically.
   - **Add**: run the presign → upload → confirm sequence for new files before submitting the final PUT.
   - **Remove**: call `DELETE /api/v1/position-templates/{id}/files/{fileId}` for each file the user removes. The response returns soft-delete metadata so the UI can refresh its list.
4. Submit the position changes via `PUT /api/v1/position-templates/{id}` (include `forceTypeChange=true` only when the user confirmed a disruptive type change).
5. Optional: surface `deletedAt` / `deleteRetentionExpiresAt` in audit or restore tooling to respect retention policies.
