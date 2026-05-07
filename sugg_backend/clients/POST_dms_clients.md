# POST /dms/clients

**Auth:** `Authorization: Bearer <token>`
**Permission:** `dms:create`

## Description

Create a new client/correspondent that documents can reference by `client_id`.

## Request Body

```json
{
  "name": "Acme Corp",       // required, max 300 chars
  "email": "a@acme.com",    // optional
  "phone": "+213 ...",      // optional
  "address": "16 rue ...",  // optional
  "notes": "VIP client"     // optional
}
```

## Response `201 Created` — Full client object

```json
{
  "id": "uuid",
  "company_id": "uuid",
  "name": "Acme Corp",
  "email": "a@acme.com",
  "phone": "+213 ...",
  "address": "16 rue ...",
  "notes": "VIP client",
  "is_deleted": false,
  "created_at": "2024-01-15T10:00:00Z",
  "updated_at": "2024-01-15T10:00:00Z"
}
```

## Validation

- `name`: required, 1–300 characters
