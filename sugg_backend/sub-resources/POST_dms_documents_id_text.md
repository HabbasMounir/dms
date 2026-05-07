# POST /dms/documents/:id/text

**Auth:** `Authorization: Bearer <token>`
**Permission:** `dms:update`

## Description

Add an OCR text row to a document.

## Path Parameters

| Param | Type | Description     |
|-------|------|-----------------|
| id    | UUID | The document ID |

## Request Body

```json
{
  "ocr_text": "Invoice content...",  // required
  "lang": "en",                       // optional
  "page_num": 1                       // optional
}
```

## Response `201 Created`

```json
{
  "id": "uuid",
  "document_id": "uuid",
  "lang": "en",
  "ocr_text": "Invoice content...",
  "page_num": 1,
  "extracted_at": "2024-01-15T10:00:00Z"
}
```

## Validation

- `ocr_text`: required, minimum 1 character
