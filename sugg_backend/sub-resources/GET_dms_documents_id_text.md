# GET /dms/documents/:id/text

**Auth:** `Authorization: Bearer <token>`
**Permission:** `dms:read`

## Description

List all OCR text rows for a document, ordered by page number ascending, then `extracted_at` ascending.

## Path Parameters

| Param | Type | Description     |
|-------|------|-----------------|
| id    | UUID | The document ID |

## Response `200 OK`

```json
[
  {
    "id": "uuid",
    "document_id": "uuid",
    "lang": "en",
    "ocr_text": "Extracted OCR text content...",
    "page_num": 1,
    "extracted_at": "2024-01-15T10:00:00Z"
  }
]
```
