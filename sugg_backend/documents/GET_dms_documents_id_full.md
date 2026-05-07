# GET /dms/documents/:id/full

**Auth:** `Authorization: Bearer <token>`
**Permission:** `dms:read`

## Description

All document data in one call — main fields + tags + client + texts + metadata + notes + access. All sub-queries fire in parallel.

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
  "created_at": "2024-01-15T10:00:00Z",
  "updated_at": "2024-01-15T10:00:00Z",
  "tags": [
    { "id": "uuid", "name": "urgent", "color": "#f87171" }
  ],
  "texts": [
    {
      "id": "uuid",
      "document_id": "uuid",
      "lang": "en",
      "ocr_text": "...",
      "page_num": 1,
      "extracted_at": "2024-01-15T10:00:00Z"
    }
  ],
  "metadata": [
    {
      "id": "uuid",
      "document_id": "uuid",
      "key": "ref",
      "value": "PO-123"
    }
  ],
  "notes": [
    {
      "id": "uuid",
      "document_id": "uuid",
      "user_id": "uuid",
      "note": "Check this.",
      "created_at": "2024-01-15T10:00:00Z",
      "updated_at": "2024-01-15T10:00:00Z"
    }
  ],
  "access": [
    {
      "id": "uuid",
      "document_id": "uuid",
      "user_id": "uuid",
      "role_id": null,
      "permission": "read",
      "granted_by": "uuid",
      "granted_at": "2024-01-15T10:00:00Z"
    }
  ]
}
```

## Notes

- Use this instead of making 5 separate calls for texts, metadata, notes, access, and main doc data.
- Sub-queries are all parallel.
