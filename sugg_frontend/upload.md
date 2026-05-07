# Route: /dms/upload (Document Upload)

## 1. CREATE / Upload Document

**POST** `/companies/:companyId/dms/documents`

### Request: `multipart/form-data`

Unlike other endpoints which use JSON, upload is **multipart** because it includes a binary file.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `file` | `File` (binary) | **Yes** | The document file (PDF, image, Word, Excel, etc.) |
| `title` | `string` | **Yes** | Document title |
| `type` | `DocType` | **Yes** | One of: `invoice`, `contract`, `bank`, `order`, `report`, `receipt`, `tax`, `legal` |
| `client` | `string` | **Yes** | Client ID |
| `tags` | `string[]` | **Yes** | Array of tag strings |
| `ref` | `string` | No | Reference number |
| `currency` | `string` | No | Currency code (e.g. `DZD`, `EUR`) |
| `amount` | `string` | No | Document amount |
| `date` | `string` | **Yes** | Document date (`YYYY-MM-DD`) |
| `archivePlaces` | `ArchivePlace[]` (JSON string) | No | Selected archive places |
| `extras` | `Record<string, string>` (JSON string) | No | Custom field values |

> **Note:** `archivePlaces` and `extras` are complex objects — encode them as JSON strings in the multipart form.

### Response: `201 Created`

```typescript
DmsDocument  // WITHOUT ocrText — contains generated thumbs, meta, etc.
```

### Processing Pipeline

The upload endpoint should:
1. Receive the file
2. Generate thumbnails (blur, sm, md, lg, xl, original) ← **backend should auto-generate these**
3. Run OCR extraction (async, returned in `GET /:id/text`)
4. Detect MIME type and page count
5. Store and return the document

### Upload Progress States (client-side)

The store tracks progress via `uploadProgressAtom` — these stages map to what the backend can report:

| Stage | Description |
|-------|-------------|
| `reading` | Client reading file (10%) |
| `uploading` | Sending multipart to server (35-65%) |
| `processing` | Server processing/OCR (85%) |
| `done` | Complete (100%) |
| `error` | Failed |

### Consumed by
- **Store**: `addDocAction` → builds `UploadPayload`, calls `DmsService.uploadDocument()`
- **Page**: `pages/upload/index.tsx`
- **Component**: `pages/upload/index.tsx` — two-stage flow (dropzone → form)

### Error Cases
- `400 Bad Request` — missing required fields or invalid file type
- `413 Payload Too Large` — file exceeds size limit
- `415 Unsupported Media Type` — file format not supported
