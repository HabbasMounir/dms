# POST /dms/documents/:id/metadata

**Auth:** `Authorization: Bearer <token>`
**Permission:** `dms:update`

## Description

Upsert a metadata key-value pair. If the key already exists for this document, the value is updated. If it doesn't exist, it's inserted.

## Path Parameters

| Param | Type | Description     |
|-------|------|-----------------|
| id    | UUID | The document ID |

## Request Body

```json
{
  "key": "po_ref",    // required, max 200 chars
  "value": "PO-123"  // optional (can be null)
}
```

## Response `200 OK`

```json
{
  "id": "uuid",
  "document_id": "uuid",
  "key": "po_ref",
  "value": "PO-123"
}
```
