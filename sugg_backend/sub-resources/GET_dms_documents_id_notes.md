# GET /dms/documents/:id/notes

**Auth:** `Authorization: Bearer <token>`
**Permission:** `dms:read`

## Description

List all notes for a document, ordered by `created_at` descending (newest first).

## Path Parameters

| Param | Type | Description     |
|-------|------|-----------------|
| id    | UUID | The document ID |

## Response `200 OK`

```json
[
  {
    "id": "uuid",
    "document_id": "uuid",
    "user_id": "uuid",
    "note": "Check the amounts on page 2.",
    "created_at": "2024-01-15T10:00:00Z",
    "updated_at": "2024-01-15T10:00:00Z"
  }
]
```
