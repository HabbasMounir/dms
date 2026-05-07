# DELETE /dms/documents/:id/access/:grant_id

**Auth:** `Authorization: Bearer <token>`
**Permission:** `dms:manage_access`

## Description

Revoke an access grant from a document.

## Path Parameters

| Param    | Type | Description          |
|----------|------|----------------------|
| id       | UUID | The document ID      |
| grant_id | UUID | The access grant ID to revoke |

## Response `204 No Content`

No response body.
