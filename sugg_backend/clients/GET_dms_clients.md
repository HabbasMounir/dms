# GET /dms/clients

**Auth:** `Authorization: Bearer <token>`
**Permission:** `dms:read`

## Description

List all active (non-deleted) clients/correspondents for the company. Sorted by name ascending. Does not include soft-deleted clients.

## Response `200 OK`

```json
[
  {
    "id": "uuid",
    "name": "Acme Corp",
    "email": "a@acme.com",
    "phone": "+213...",
    "created_at": "2024-01-15T10:00:00Z"
  }
]
```

## Notes

- Only returns a subset of fields — excludes `address`, `notes`, `is_deleted`, `updated_at`.
- Clients referenced by documents keep their `client_id` even after soft-delete (SET NULL on hard delete).
