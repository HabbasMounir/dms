# DELETE /dms/tags/:tag_id

**Auth:** `Authorization: Bearer <token>`
**Permission:** `dms:delete`

## Description

Delete a tag. Also removes it from all documents that reference it (cascade via `dms_document_tags`).

## Path Parameters

| Param   | Type   | Description |
|---------|--------|-------------|
| tag_id  | UUID   | The tag ID to delete |

## Response `204 No Content`

No response body.

## Notes

- Cascade removal from `dms_document_tags` junction table.
- Only tags belonging to the current company are affected (scoped by `company_id`).
