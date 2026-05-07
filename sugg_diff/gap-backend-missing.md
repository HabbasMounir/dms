# Backend Missing Endpoints

> Endpoints that the frontend spec (`sugg_frontend/`) expects but the backend spec (`sugg_backend/`) does **not** currently document.

---

## 1. Users & Roles (Read-Only)

### GET `/dms/users`

**Source:** `sugg_frontend/main.md` (#10), `sugg_frontend/permissions.md` (#1)

Used by the share popover and permissions page to list users for selection.

```typescript
// Response: 200 OK
Array<{ id: string; name: string; email: string }>
```

| Aspect | Detail |
|--------|--------|
| Permission | `dms:read` (or system-level) |
| Notes | Frontend treats this as read-only. Users managed at system/auth level. |
| Priority | **High** — needed by two frontend pages |

### GET `/dms/roles`

**Source:** `sugg_frontend/main.md` (#11), `sugg_frontend/permissions.md` (#2)

Used by share popover and permissions page for role-based access.

```typescript
// Response: 200 OK
Array<{ id: string; name: string; permissions: string[] }>
```

| Aspect | Detail |
|--------|--------|
| Permission | `dms:read` |
| Notes | Frontend shows permission toggles but treats as read-only for now. |
| Priority | **High** |

---

## 2. Access Profiles (CRUD)

### GET/POST/PATCH/DELETE `/dms/access-profiles`

**Source:** `sugg_frontend/permissions.md` (#3–6)

Full CRUD for named access profiles with color, description, and permission levels.

```typescript
interface DmsAccessProfile {
  id: string;
  name: string;
  color: string;           // hex color for UI badge
  description?: string;
  permissions: ("read" | "get" | "update" | "delete" | "send")[];
}
```

| Method | Path | Permission | Returns |
|--------|------|------------|---------|
| `GET` | `/dms/access-profiles` | `dms:read` | `DmsAccessProfile[]` |
| `POST` | `/dms/access-profiles` | `dms:manage_access` | `201 Created` → `DmsAccessProfile` |
| `PATCH` | `/dms/access-profiles/:id` | `dms:manage_access` | `200 OK` → `DmsAccessProfile` |
| `DELETE` | `/dms/access-profiles/:id` | `dms:manage_access` | `204 No Content` |

**Priority:** **High**

---

## 3. Archive Places (CRUD)

### GET/POST/PATCH/DELETE `/dms/archive-places`

**Source:** `sugg_frontend/main.md` (#13), `sugg_frontend/permissions.md` (#7–10)

Used by document metadata (main page) and the storage/places management page.

```typescript
interface ArchivePlace {
  id: string;
  type: "physical" | "digital" | "cloud";
  label: string;
  location_detail: string;
}
```

| Method | Path | Permission | Returns |
|--------|------|------------|---------|
| `GET` | `/dms/archive-places` | `dms:read` | `ArchivePlace[]` |
| `POST` | `/dms/archive-places` | `dms:update` | `201 Created` → `ArchivePlace` |
| `PATCH` | `/dms/archive-places/:id` | `dms:update` | `200 OK` → `ArchivePlace` |
| `DELETE` | `/dms/archive-places/:id` | `dms:delete` | `204 No Content` |

**Priority:** **High** — referenced by two frontend pages

---

## 4. Storage Spaces (List + Replace)

### GET `/dms/storage-spaces`

**Source:** `sugg_frontend/permissions.md` (#11)

Returns a nested hierarchy of storage spaces → rooms → folders.

```typescript
interface StorageSpace {
  id: string;
  name: string;
  icon: string;
  color: string;
  tag?: string;
  rooms: StorageRoom[];
}

interface StorageRoom {
  id: string;
  name: string;
  icon: string;
  color: string;
  tag?: string;
  folders: StorageFolder[];
}

interface StorageFolder {
  id: string;
  name: string;
  icon: string;
  color: string;
  tag?: string;
}
```

| Method | Path | Permission | Returns |
|--------|------|------------|---------|
| `GET` | `/dms/storage-spaces` | `dms:read` | `StorageSpace[]` |

### PUT `/dms/storage-spaces`

**Source:** `sugg_frontend/permissions.md` (#12)

Full replacement of the storage spaces collection.

| Method | Path | Permission | Returns |
|--------|------|------------|---------|
| `PUT` | `/dms/storage-spaces` | `dms:update` | `200 OK` |

**Priority:** **Medium**

---

## 5. Custom Fields (CRUD)

### GET/POST/PATCH/DELETE `/dms/custom-fields`

**Source:** `sugg_frontend/fields.md`

Entirely client-side mock currently. Backend persistence is needed.

```typescript
interface CustomField {
  id: string;
  name: string;
  type: "text" | "number" | "date" | "boolean" | "select" | "textarea";
  required: boolean;
  options?: string[];   // for "select" type
}
```

| Method | Path | Permission | Returns |
|--------|------|------------|---------|
| `GET` | `/dms/custom-fields` | `dms:read` | `CustomField[]` |
| `POST` | `/dms/custom-fields` | `dms:update` | `201 Created` → `CustomField` |
| `PATCH` | `/dms/custom-fields/:id` | `dms:update` | `200 OK` → `CustomField` |
| `DELETE` | `/dms/custom-fields/:id` | `dms:delete` | `204 No Content` |

Custom field values are stored per-document in `meta.extras` as `Record<string, string>`.

**Priority:** **Medium** (currently mocked client-side)

---

## 6. Shared Documents

### GET `/dms/shared`

**Source:** `sugg_frontend/shared.md`

Returns documents shared with the current user where they are NOT the uploader.

```typescript
// Response: 200 OK
PaginatedResponse<DmsDocument>
```

| Query Param | Type | Required | Description |
|-------------|------|----------|-------------|
| `page` | `number` | No | Page number |
| `limit` | `number` | No | Items per page |

| Aspect | Detail |
|--------|--------|
| Permission | `dms:read` |
| Auth note | Backend should derive user from JWT, not a query param |
| Priority | **High** — dedicated page with no backend |

---

## 7. Document Preview (Signed Thumbnail URL)

### GET `/dms/documents/:id/preview`

**Source:** `sugg_frontend/main.md` (#6)

Returns a signed URL for document thumbnails at various sizes.

```typescript
// Response: 200 OK
{ url: string }
```

| Query Param | Type | Required | Description |
|-------------|------|----------|-------------|
| `size` | `"sm" \| "md" \| "lg" \| "xl"` | **Yes** | Preview resolution |

| Aspect | Detail |
|--------|--------|
| Permission | `dms:read` |
| Notes | Currently frontend likely uses direct MinIO URLs. This endpoint would provide signed, expiring URLs with size-specific thumbnails. |
| Priority | **Medium** — frontend may work with direct URLs for now |

---

## 8. OCR Trigger (Optional)

### POST `/dms/documents/:id/ocr`

**Source:** `sugg_frontend/scan.md` (#2)

Triggers server-side OCR processing on an uploaded document.

```typescript
// Request body: {} or configuration
// Response: 200 OK
{ ocrText: string; status: "processing" | "done" }
```

| Aspect | Detail |
|--------|--------|
| Permission | `dms:update` |
| Notes | Frontend already has client-side OCR preprocessing via OpenCV WASM. This is for server-side OCR re-processing. The existing `POST /dms/documents/:id/text` can already store OCR results. |
| Priority | **Low** — optional enhancement |

---

## Summary Table

| # | Endpoint | Frontend Source | Priority |
|---|----------|-----------------|----------|
| 1 | `GET /dms/users` | `main.md`, `permissions.md` | 🔴 High |
| 2 | `GET /dms/roles` | `main.md`, `permissions.md` | 🔴 High |
| 3 | CRUD `/dms/access-profiles` (4) | `permissions.md` | 🔴 High |
| 4 | CRUD `/dms/archive-places` (4) | `main.md`, `permissions.md` | 🔴 High |
| 5 | `GET /dms/shared` | `shared.md` | 🔴 High |
| 6 | `GET/PUT /dms/storage-spaces` (2) | `permissions.md` | 🟡 Medium |
| 7 | CRUD `/dms/custom-fields` (4) | `fields.md` | 🟡 Medium |
| 8 | `GET /dms/documents/:id/preview` | `main.md` | 🟡 Medium |
| 9 | `POST /dms/documents/:id/ocr` | `scan.md` | 🟢 Low |

**Total missing endpoints: ~18** (counting each HTTP method separately)
