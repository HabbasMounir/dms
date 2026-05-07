# POST /dms/documents/:id/notes

**Auth:** `Authorization: Bearer <token>`
**Permission:** `dms:update`

## Description

Add a note to a document. The note is attributed to the authenticated user (`ctx.user_id`).

## Path Parameters

| Param | Type | Description     |
|-------|------|-----------------|
| id    | UUID | The document ID |

## Request Body

```json
{
  "note": "Check the amounts on page 2."  // required
}
```

## Response `201 Created`

```json
{
  "id": "uuid",
  "document_id": "uuid",
  "user_id": "uuid",
  "note": "Check the amounts on page 2.",
  "created_at": "2024-01-15T10:00:00Z",
  "updated_at": "2024-01-15T10:00:00Z"
}
```

## Validation

- `note`: required, minimum 1 character
