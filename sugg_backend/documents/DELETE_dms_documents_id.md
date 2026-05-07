# DELETE /dms/documents/:id

**Auth:** `Authorization: Bearer <token>`
**Permission:** `dms:delete`

## Description

Soft-delete a document. Sets `is_deleted = TRUE`. The file stays in MinIO.

## Path Parameters

| Param | Type | Description     |
|-------|------|-----------------|
| id    | UUID | The document ID |

## Response `204 No Content`

No response body.

## Notes

- Soft-delete only — record remains in the database with `is_deleted = TRUE`.
- Physical file in MinIO is **not** deleted.
- Subsequent GET requests will return `404` for this document.

## Errors

- `404 Not Found` — document not found or already soft-deleted
