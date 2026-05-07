# POST /dms/documents

**Auth:** `Authorization: Bearer <token>`
**Permission:** `dms:create`

## Description

Upload a document. Request must be `multipart/form-data`. Accepts PDF, HTML, or any file type. File is stored in MinIO at `{company_id}/{yyyy}/{mm}/{uuid}-{sanitized_filename}`.

## Request — Multipart Form Fields

| Field          | Type   | Required | Description                              |
|----------------|--------|----------|------------------------------------------|
| title          | string | **yes**  | Document title                           |
| doc_type       | string | **yes**  | e.g. "invoice", "contract"              |
| file           | File   | **yes**  | Any file type (PDF, HTML, etc.)         |
| client_id      | string | optional | UUID of a `dms_client`                  |
| tag_ids        | string | optional | JSON array of tag UUIDs: `["uuid","uuid"]` |
| thumb_b64      | string | optional | Base64 10×10 preview image              |
| custom_fields  | string | optional | JSON object: `{"ref":"PO-123"}`         |

### Example cURL

```bash
curl -X POST http://localhost:3000/api/v1/companies/{company_id}/dms/documents \
  -H "Authorization: Bearer <token>" \
  -F "title=Invoice #2024-01" \
  -F "doc_type=invoice" \
  -F "tag_ids=[\"tag-uuid\"]" \
  -F "client_id=client-uuid" \
  -F "custom_fields={\"ref\":\"PO-123\"}" \
  -F "file=@invoice.pdf"
```

## Response `201 Created`

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
  "updated_at": "2024-01-15T10:00:00Z"
}
```

## Validation

- `title`: required
- `doc_type`: required
- `file`: required
- `tag_ids` (if provided): must reference existing `dms_tags` for this company
- `client_id` (if provided): must reference an existing, non-deleted `dms_client`
