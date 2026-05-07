# POST /dms/tags

**Auth:** `Authorization: Bearer <token>`
**Permission:** `dms:update`

## Description

Create (or update color of) an approved tag. Idempotent — if a tag with the same `name` already exists for this company, its `color` is updated instead.

## Request Body

```json
{
  "name": "urgent",    // required, max 100 chars
  "color": "#f87171"  // optional hex color, defaults to "#6ea8fe"
}
```

## Response `201 Created`

```json
{
  "id": "uuid",
  "company_id": "uuid",
  "name": "urgent",
  "color": "#f87171",
  "created_at": "2024-01-15T10:00:00Z"
}
```

## Validation

- `name`: required, 1–100 characters
- `color`: optional, must be a valid hex color (not strictly validated by backend, passed as-is)
