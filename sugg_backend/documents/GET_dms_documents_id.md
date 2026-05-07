# GET /dms/documents/:id

**Auth:** `Authorization: Bearer <token>`
**Permission:** `dms:read`

## Description

Get main document data + tags + client name. Does **not** include texts, notes, metadata, or access — use `/dms/documents/:id/full` for everything.

## Path Parameters

| Param | Type | Description     |
|-------|------|-----------------|
| id    | UUID | The document ID |

## Response `200 OK`

```json
{
  "id": "uuid",
  "title": "Invoice #2024-01",
  "doc_type": "invoice",
  "client_id": "uuid",
  "client_name": "Acme Corp",
  "uploaded_by": "uuid",
  "thumb_b64": null,
  "file_key": "company-uuid/2024/01/uuid-invoice.pdf",
  "mime_type": "application/pdf",
  "size_bytes": 204800,
  "custom_fields": {},
  "tags": [
    { "id": "uuid", "name": "urgent", "color": "#f87171" }
  ],
  "created_at": "2024-01-15T10:00:00Z",
  "updated_at": "2024-01-15T10:00:00Z
}
```

## Errors

- `404 Not Found` — document not found or soft-deleted
