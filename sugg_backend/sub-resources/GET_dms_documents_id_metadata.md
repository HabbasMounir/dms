# GET /dms/documents/:id/metadata

**Auth:** `Authorization: Bearer <token>`
**Permission:** `dms:read`

## Description

List all key-value metadata pairs for a document. Ordered alphabetically by key.

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
    "key": "po_ref",
    "value": "PO-123"
  }
]
```
