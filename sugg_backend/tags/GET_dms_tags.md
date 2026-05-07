# GET /dms/tags

**Auth:** `Authorization: Bearer <token>`
**Permission:** `dms:read`

## Description

List all approved tags for the company. Returns the managed tag list sorted by name ascending.

## Response `200 OK`

```json
[
  {
    "id": "uuid",
    "company_id": "uuid",
    "name": "urgent",
    "color": "#f87171",
    "created_at": "2024-01-15T10:00:00Z"
  },
  {
    "id": "uuid",
    "company_id": "uuid",
    "name": "q1",
    "color": "#6ea8fe",
    "created_at": "2024-01-15T10:00:00Z"
  }
]
```

## Notes

- Tags are scoped per company.
- Only tags in this list can be attached to documents.
