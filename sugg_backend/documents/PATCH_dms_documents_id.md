# PATCH /dms/documents/:id

**Auth:** `Authorization: Bearer <token>`
**Permission:** `dms:update`

## Description

Update document fields. All fields are optional — only provided fields change. If `tag_ids` is provided, it **replaces** all existing tags.

## Path Parameters

| Param | Type | Description     |
|-------|------|-----------------|
| id    | UUID | The document ID |

## Request Body

```json
{
  "title": "Updated title",              // optional, max 500 chars
  "doc_type": "contract",                // optional, max 100 chars
  "client_id": "uuid",                   // optional, UUID
  "thumb_b64": "data:image/png;base64,..", // optional
  "custom_fields": { "ref": "PO-456" },  // optional, replaces entire JSONB value
  "tag_ids": ["uuid", "uuid"]            // optional, replaces ALL tags
}
```

## Response `200 OK` — Updated DocumentResponse

```json
{
  "id": "uuid",
  "title": "Updated title",
  "doc_type": "contract",
  "client_id": "uuid",
  "client_name": "Acme Corp",
  "uploaded_by": "uuid",
  "thumb_b64": null,
  "file_key": "company-uuid/2024/01/uuid-invoice.pdf",
  "mime_type": "application/pdf",
  "size_bytes": 204800,
  "custom_fields": { "ref": "PO-456" },
  "tags": [
    { "id": "uuid", "name": "urgent", "color": "#f87171" }
  ],
  "created_at": "2024-01-15T10:00:00Z",
  "updated_at": "2024-01-16T10:00:00Z"
}
```

## Notes

- Providing `tag_ids` **replaces** all existing tags — old ones are removed.
- `tag_ids` must reference existing `dms_tags` for this company.
- `updated_at` is automatically set to `NOW()`.

## Errors

- `404 Not Found` — document not found or soft-deleted
- `400 Bad Request` — invalid tag_ids or validation failure
