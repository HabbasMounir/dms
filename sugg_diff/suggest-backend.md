# Suggestions for Backend

> Prioritized recommendations to align the backend spec (`sugg_backend/`) with frontend needs (`sugg_frontend/`).

---

## Phase 1: Critical (🔴) — Must-Have for Core Frontend Pages

### 1. Add Users & Roles Endpoints

Two read-only endpoints needed urgently — referenced by both `main.md` (share popover) and `permissions.md` (permissions page).

```rust
// GET /api/v1/companies/:company_id/dms/users
// Permission: dms:read
// Response: Vec<UserSummary>
struct UserSummary {
    id: String,
    name: String,
    email: String,
}

// GET /api/v1/companies/:company_id/dms/roles
// Permission: dms:read
// Response: Vec<RoleSummary>
struct RoleSummary {
    id: String,
    name: String,
    permissions: Vec<String>,
}
```

**Implementation notes:**
- Users likely come from the system/auth service — proxy or join
- Roles are likely already stored in a `roles` table scoped to company
- Keep read-only for now (frontend doesn't need user/role mutation)

### 2. Add Archive Places CRUD (4 endpoints)

```rust
// GET /api/v1/companies/:company_id/dms/archive-places
// POST /api/v1/companies/:company_id/dms/archive-places
// PATCH /api/v1/companies/:company_id/dms/archive-places/:id
// DELETE /api/v1/companies/:company_id/dms/archive-places/:id
struct ArchivePlace {
    id: String,
    r#type: ArchivePlaceType,  // "physical" | "digital" | "cloud"
    label: String,
    location_detail: String,
}
```

**Consider:** Should this reuse the existing `dms_document_metadata` table or have its own `dms_archive_places` table? A separate table is cleaner for CRUD operations and independent lifecycle.

### 3. Add Access Profiles CRUD (4 endpoints)

```rust
// GET /api/v1/companies/:company_id/dms/access-profiles
// POST /api/v1/companies/:company_id/dms/access-profiles
// PATCH /api/v1/companies/:company_id/dms/access-profiles/:id
// DELETE /api/v1/companies/:company_id/dms/access-profiles/:id
struct DmsAccessProfile {
    id: String,
    name: String,
    color: String,        // hex color
    description: Option<String>,
    permissions: Vec<String>,  // "read" | "get" | "update" | "delete" | "send"
}
```

### 4. Add Shared Documents Endpoint

```rust
// GET /api/v1/companies/:company_id/dms/shared
// Permission: dms:read
// Auth: Derive user from JWT — NOT a query param
// Query: page, limit
// Response: PaginatedResponse<DmsDocumentSummary>
```

**Filtering logic:** Return documents where the authenticated user appears in an access grant (via `dms_document_access` table) but is NOT the `uploaded_by` user.

### 5. Add Get Document Preview Endpoint

```rust
// GET /api/v1/companies/:company_id/dms/documents/:id/preview
// Query: size = "sm" | "md" | "lg" | "xl"
// Response: { url: String }
// Returns a signed MinIO URL for thumbnail images
```

---

## Phase 2: Important (🟡) — Enhances Feature Completeness

### 6. Add Storage Spaces Endpoints

```rust
// GET /api/v1/companies/:company_id/dms/storage-spaces
// PUT /api/v1/companies/:company_id/dms/storage-spaces

struct StorageSpace {
    id: String,
    name: String,
    icon: String,
    color: String,
    tag: Option<String>,
    rooms: Vec<StorageRoom>,
}
// StorageRoom contains nested StorageFolder
```

**Architecture note:** This is a hierarchical structure (space → room → folder). Consider if this needs its own table(s) or can be stored as a JSONB column for simplicity.

### 7. Add Custom Fields CRUD (4 endpoints)

```rust
// GET /api/v1/companies/:company_id/dms/custom-fields
// POST /api/v1/companies/:company_id/dms/custom-fields
// PATCH /api/v1/companies/:company_id/dms/custom-fields/:id
// DELETE /api/v1/companies/:company_id/dms/custom-fields/:id

struct CustomField {
    id: String,
    name: String,
    r#type: FieldType,  // "text" | "number" | "date" | "boolean" | "select" | "textarea"
    required: bool,
    options: Option<Vec<String>>,  // for "select" type
}
```

**Suggestion:** Store custom field definitions in a `dms_custom_fields` table. Values go in `dms_document_metadata.extras` (JSONB) as `Record<String, String>`.

### 8. Add Optional OCR Endpoint

```rust
// POST /api/v1/companies/:company_id/dms/documents/:id/ocr
// Response: { ocr_text: String, status: "processing" | "done" }
```

**Notes:** Low priority — client-side OCR preprocessing via OpenCV WASM already handles the scan pipeline. This would be for server-side re-processing or initial OCR on uploaded files.

---

## Phase 3: Data Alignment (🟡) — Fixes Mismatches

### 9. Align Tag Model with Frontend

Either:
- **(Recommended) Frontend adopts structured tags**: Keep `{id, name, color}` and update frontend to use it
- **Or add a flat endpoint**: `GET /tags?flat=true` returning `string[]` for backward compat

Add an optional `color` field to tag responses (already in backend spec, just needs frontend to use it).

### 10. Enrich Document Responses

Consider adding these to `DmsDocumentSummary` (list response) to match frontend expectations:

| Field | Source | Why |
|-------|--------|-----|
| `client_name` | JOIN with `dms_clients` | Frontend displays client as string |
| `uploaded_by` | From document record | Frontend shows uploader |
| `uploaded_at` | From document record | Frontend shows upload date |
| `archived` | Boolean column | Frontend filters by this |

### 11. Standardize Pagination Response Shape

Ensure all paginated endpoints return the exact shape frontend expects:

```json
{
  "data": [...],
  "page": 1,
  "limit": 50,
  "total": 142
}
```

### 12. Add Client Extra Fields to Response

Even if frontend doesn't collect them yet, include `email`, `phone`, `address`, `notes` in client responses so the frontend can display them in the future without a backend change.

---

## Summary Priority Matrix

| Priority | Endpoints | Count |
|----------|-----------|-------|
| 🔴 Phase 1 | Users, Roles, Archive Places CRUD, Access Profiles CRUD, Shared, Preview | ~13 |
| 🟡 Phase 2 | Storage Spaces, Custom Fields CRUD, OCR | ~7 |
| 🟡 Phase 3 | Data alignment (tags, client enrich, pagination shape) | ~5 changes |
