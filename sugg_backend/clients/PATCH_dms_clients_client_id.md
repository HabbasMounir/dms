# PATCH /dms/clients/:client_id

**Auth:** `Authorization: Bearer <token>`
**Permission:** `dms:update`

## Description

Update a client. All fields are optional — only provided fields are updated.

## Path Parameters

| Param     | Type | Description   |
|-----------|------|---------------|
| client_id | UUID | The client ID |

## Request Body

```json
{
  "name": "Acme Corp Updated",  // optional, max 300 chars
  "email": "new@acme.com",     // optional
  "phone": "+213 ...",         // optional
  "address": "New address",    // optional
  "notes": "Updated notes"     // optional
}
```

## Response `200 OK` — Full client object

```json
{
  "id": "uuid",
  "company_id": "uuid",
  "name": "Acme Corp Updated",
  "email": "new@acme.com",
  "phone": "+213 ...",
  "address": "New address",
  "notes": "Updated notes",
  "is_deleted": false,
  "created_at": "2024-01-15T10:00:00Z",
  "updated_at": "2024-01-16T10:00:00Z"
}
```

## Errors

- `404 Not Found` — client not found or soft-deleted
