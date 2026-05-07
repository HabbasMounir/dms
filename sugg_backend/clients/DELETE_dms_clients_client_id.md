# DELETE /dms/clients/:client_id

**Auth:** `Authorization: Bearer <token>`
**Permission:** `dms:delete`

## Description

Soft-delete a client. Sets `is_deleted = TRUE`. Existing documents keep their `client_id` reference.

## Path Parameters

| Param     | Type | Description   |
|-----------|------|---------------|
| client_id | UUID | The client ID |

## Response `204 No Content`

No response body.

## Notes

- Soft-delete only — data remains in the database with `is_deleted = TRUE`.
- Documents referencing this client are **not** affected (SET NULL on hard delete, but this is soft).
