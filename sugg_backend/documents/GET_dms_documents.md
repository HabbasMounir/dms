# GET /dms/documents

**Auth:** `Authorization: Bearer <token>`
**Permission:** `dms:read`

## Description

List documents for the company. Returns main fields only — no texts, metadata, notes, or access. Tags are batch-joined in a single query.

## Query Parameters

| Param     | Type       | Default | Description                              |
|-----------|------------|---------|------------------------------------------|
| range     | string     | `"6m"`  | Preset: `6m`, `1y`, `all`               |
| from      | ISO8601    | —       | Overrides `range` — start date           |
| to        | ISO8601    | —       | Overrides `range` — end date             |
| doc_type  | string     | —       | Filter by document type (exact match)    |
| tag_id    | UUID       | —       | Filter by tag                            |
| client_id | UUID       | —       | Filter by client                         |
| search    | string     | —       | ILIKE search on title                    |
| limit     | integer    | `100`   | Max 500                                  |
| offset    | integer    | `0`     | Pagination offset                        |

## Response `200 OK`

```json
[
  {
    "id": "uuid",
    "title": "Invoice #2024-01",
    "doc_type": "invoice",
    "client_id": "uuid",
    "client_name": "Acme Corp",
    "uploaded_by": "uuid",
    "thumb_b64": null,
    "mime_type": "application/pdf",
    "size_bytes": 204800,
    "tags": [
      { "id": "uuid", "name": "urgent", "color": "#f87171" }
    ],
    "created_at": "2024-01-15T10:00:00Z"
  }
]
```

## Notes

- `range` and `from`/`to` are mutually exclusive. If `from` or `to` is provided, `range` is ignored.
- Tags are batch-fetched in one query for all returned documents — N+1 avoided.
- Soft-deleted documents (`is_deleted = TRUE`) are excluded.
