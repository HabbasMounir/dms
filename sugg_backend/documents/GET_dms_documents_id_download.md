# GET /dms/documents/:id/download

**Auth:** `Authorization: Bearer <token>`
**Permission:** `dms:read`

## Description

Get a presigned MinIO download URL valid for 15 minutes. Open the URL directly in the browser or use it to programmatically download the file.

## Path Parameters

| Param | Type | Description     |
|-------|------|-----------------|
| id    | UUID | The document ID |

## Response `200 OK`

```json
{
  "url": "http://minio:9000/dms/company-uuid/2024/01/uuid-invoice.pdf?X-Amz-Algorithm=...&X-Amz-...",
  "expires_in_seconds": 900
}
```

## Notes

- URL expires after 900 seconds (15 minutes).
- The URL must be generated fresh — no caching/storing the URL beyond its lifetime.
