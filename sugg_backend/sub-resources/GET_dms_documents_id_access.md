# GET /dms/documents/:id/access

**Auth:** `Authorization: Bearer <token>`
**Permission:** `dms:read`

## Description

List per-document access grants. Ordered by `granted_at` descending (newest first).

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
    "role_id": null,
    "permission": "read",
    "granted_by": "uuid",
    "granted_at": "2024-01-15T10:00:00Z"
  }
]
```

## Notes

- Either `user_id` or `role_id` will be present, never both `null`.
- `granted_by` is the user who granted the access.
