# POST /dms/documents/:id/access

**Auth:** `Authorization: Bearer <token>`
**Permission:** `dms:manage_access`

## Description

Grant access to a document for a user or role. At least one of `user_id` or `role_id` is required.

## Path Parameters

| Param | Type | Description     |
|-------|------|-----------------|
| id    | UUID | The document ID |

## Request Body

```json
{
  "user_id": "uuid",        // optional (required if role_id is null)
  "role_id": null,          // optional (required if user_id is null)
  "permission": "read"      // required — "read" | "write" | "admin"
}
```

## Response `201 Created`

```json
{
  "id": "uuid",
  "document_id": "uuid",
  "user_id": "uuid",
  "role_id": null,
  "permission": "read",
  "granted_by": "uuid",
  "granted_at": "2024-01-15T10:00:00Z"
}
```

## Validation

- At least one of `user_id` or `role_id` must be provided.
- `permission`: required, max 20 chars, must be `read`, `write`, or `admin`.

## Errors

- `400 Bad Request` — both `user_id` and `role_id` are null
