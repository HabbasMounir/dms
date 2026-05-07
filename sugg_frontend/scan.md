# Route: /dms/scan (Camera Scanner)

## Overview

The scan page captures documents via camera, processes them with OpenCV WASM (auto-detect corners, perspective warp, crop), then saves the document. The final save is the same endpoint as upload.

## 1. SAVE Scanned Document

**POST** `/companies/:companyId/dms/documents`

### Request: `multipart/form-data`

Same as upload — the scan produces a processed image (PNG/JPEG blob) from canvas, then sends it as a file.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `file` | `File` (binary) | **Yes** | Processed image (PNG/JPEG) after crop & transform |
| `title` | `string` | **Yes** | Document title |
| `type` | `DocType` | **Yes** | `invoice`, `contract`, etc. |
| `client` | `string` | **Yes** | Client ID |
| `tags` | `string[]` | **Yes** | Tags |
| `ref` | `string` | No | Reference number |
| `currency` | `string` | No | Currency |
| `amount` | `string` | No | Amount |
| `date` | `string` | **Yes** | `YYYY-MM-DD` |
| `archivePlaces` | JSON string | No | Selected archive places |
| `extras` | JSON string | No | Custom field values |

### Response: `201 Created`

```typescript
DmsDocument  // WITHOUT ocrText
```

---

## 2. (Optional) OCR Endpoint for Scans

If OCR should run server-side after scan:

**POST** `/companies/:companyId/dms/documents/:id/ocr`

### Request Body

```typescript
interface { /* empty or config */ }
```

### Response: `200 OK`

```typescript
interface { ocrText: string; status: "processing" | "done" }
```

### Notes
- Current mock returns static fixture text
- The scan page currently handles all image processing client-side (OpenCV WASM for corner detection, canvas for warp/crop)
- Backend should NOT expect any client-side processing — just receive the final image

### Consumed by
- **Page**: `pages/scan/index.tsx` — 3-step wizard (Capture → Crop/Edit → Fields)
- **Component**: `FieldsStep.tsx` — form with save → `onSave` callback
- **Store**: `addDocAction` (same as upload)

### Scan Wizard Flow
```
Step 1: Capture   → snapVideo() → canvas blob
Step 2: Crop      → geometry.ts (homography, warp, auto-detect corners)
Step 3: Fields    → metadata form → POST document
```
