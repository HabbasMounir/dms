# Model / Data Shape Mismatches

> Structural differences between frontend TypeScript interfaces (`sugg_frontend/`) and backend Rust models (`sugg_backend/`).

---

## 1. Tags: `string[]` vs Structured Objects

**Severity: 🔴 High — impacts filtering, deletion, and display**

| Aspect | Frontend (`sugg_frontend/`) | Backend (`sugg_backend/`) |
|--------|-----------------------------|---------------------------|
| Shape | `string[]` — flat array of tag names | `{ id: uuid, name: string, color: string }` |
| Deletion key | By tag name: `DELETE /tags/:name` | By UUID: `DELETE /tags/:tag_id` |
| Color | Not present | Hex color, defaults to `#6ea8fe` |
| Auto-created | Free-form strings on documents | Must exist in `dms_tags` table; attached via junction table |

**Impact:**
- Frontend tag filter dropdown expects `string[]`, backend returns objects with `id`/`name`/`color`
- Deleting a tag by name won't work with UUID-based backend routes
- Frontend can't display tag colors (backend has them but frontend ignores them)

**Suggestion:** Frontend should update to `Tag[]` where `Tag = { id: string; name: string; color: string }`. Backend could also expose a lightweight `GET /tags?flat=true` returning `string[]` for backward compatibility.

---

## 2. Clients: Simplified vs Rich Model

**Severity: 🟡 Medium — missing fields are optional but useful**

| Aspect | Frontend (`correspondents.md`) | Backend (`sugg_backend/clients/`) |
|--------|-------------------------------|-----------------------------------|
| Fields | `id`, `name`, `ref`, `city` | `id`, `name`, `email?`, `phone?`, `address?`, `notes?`, `is_deleted` |
| Client ref in doc | `client: string` (just name string) | Should reference client by ID |
| Deletion | Simple delete | Soft-delete (`is_deleted = TRUE`) |
| Create body | `{ name, ref, city }` (all required) | `{ name }` required; `email?`, `phone?`, `address?`, `notes?` optional |

**Impact:**
- Frontend form only collects 3 fields; backend accepts 7 — extra fields won't be visible in UI
- Frontend's `client` field on `DmsDocument` is a string; backend references client ID
- Frontend doesn't handle soft-delete semantics (filtering out deleted clients)

---

## 3. Document Root Shape: Flattened vs Sub-Resource Pattern

**Severity: 🟡 Medium — affects data fetching strategy and interface size**

**Frontend `DmsDocument` includes at root level:**
- `tags: string[]`
- `sharedUsers: string[]`
- `sharedRoles: string[]`
- `notes?: Array<{...}>`
- `permissions?: DocPermEntry[]`
- `thumbs: { blur, thumb_sm, thumb_md, thumb_lg, thumb_xl, original }`
- `meta: { ref, currency, amount, vat, archivePlaces, extras? }`
- `client: string` (plain string)
- `uploadedBy?: string`
- `uploadedAt?: string`
- `company?: string`
- `mimeType?: string`
- `archived?: boolean`

**Backend organizes data across:**
| Resource | Endpoint |
|----------|----------|
| Main doc fields | `GET /dms/documents/:id` |
| Tags | `GET /dms/tags` (global) + junction table per doc |
| Text/OCR | `GET /dms/documents/:id/text` |
| Metadata | `GET /dms/documents/:id/metadata` |
| Notes | `GET /dms/documents/:id/notes` |
| Access | `GET /dms/documents/:id/access` |
| Full bundle | `GET /dms/documents/:id/full` (parallel sub-queries) |

**Impact:**
- Frontend expects most data in a single response; backend requires multiple calls (or the `/full` endpoint)
- Frontend has `sharedUsers`/`sharedRoles` at root; backend access grants are per-document grants with `user_id`/`role_id`
- Frontend `client` field is a string; backend references are by client ID (needs join or separate fetch)
- Frontend `thumbs` needs size key alignment (frontend uses lowercase `thumb_sm` etc.)

---

## 4. Thumbnail Size Keys

**Severity: 🟢 Low — naming convention only**

| Frontend Key | Backend Description |
|--------------|---------------------|
| `blur` | 40×52 blurred placeholder |
| `thumb_sm` | 150×200 |
| `thumb_md` | 400×520 |
| `thumb_lg` | 800×1040 |
| `thumb_xl` | 1200×1560 |
| `original` | Full resolution |

Backend spec (from `POST /dms/documents`) mentions "generate thumbnails (blur, sm, md, lg, xl, original)" which matches — **no mismatch here**, but backend should ensure the exact key naming matches what the frontend expects.

---

## 5. Archive: Boolean Field vs Date Heuristic

**Severity: 🟡 Medium — affects archive filter correctness**

| Aspect | Frontend Expectation | Current Mock (frontend notes) |
|--------|----------------------|-------------------------------|
| Archive filter | `archived=true` query param | Hardcoded `date < 2025-01-01` |
| Archive toggle | `PATCH /documents/:id { archived: true }` | Same endpoint |
| Model field | `archived: boolean` on document | Not yet implemented |

**Status:** Frontend spec and backend spec agree on using an `archived` boolean field. The issue is the frontend's *current mock implementation* uses a date heuristic. Backend should implement a proper boolean column. **No mismatch in the spec itself.**

---

## 6. Pagination Shape

**Severity: 🟢 Low — verify field naming**

| Aspect | Frontend | Backend |
|--------|----------|---------|
| Shape | `{ data, page, limit, total }` | Not explicitly specified in backend docs |
| Field for items | `data` | TBD — `data` or `items` or `results` |

**Suggestion:** Backend should match frontend's expected `PaginatedResponse<T>` shape exactly.

---

## 7. `client` Field on Documents: String vs ID

**Severity: 🟡 Medium — affects join queries and display**

- **Frontend:** `client: string` — appears to be the client **name** for display
- **Backend:** Documents reference clients via `client_id` (foreign key to `dms_clients`)
- **Gap:** The backend needs to return both `client_id` (for operations) and `client_name` (for display). A single `client: string` field can't serve both purposes.

**Suggestion:** Frontend should accept `client: { id: string; name: string }` or separate `clientId` and `clientName` fields. The backend should include the client name in document list/detail responses via a JOIN.

---

## 8. Permission / Access Model: Document-Level vs Profile-Level

**Severity: 🟡 Medium — conceptual mismatch**

| Aspect | Frontend | Backend |
|--------|----------|---------|
| Per-document access | `permissions?: DocPermEntry[]` on DmsDocument | 3 dedicated endpoints for access grants |
| Share popover | Uses `usersAtom`, `rolesAtom`, `dmsAccessProfilesAtom` | No users/roles/access-profiles endpoints exist |
| Shared listing | `GET /dms/shared` | Not implemented |

**Impact:**
- Frontend's `DmsDocument.permissions` shape needs to align with backend's access grant response
- Share popover UI needs to be wired to POST/PATCH per-document access, not just display profiles
- `GET /dms/shared` filtering logic must match how the backend stores access grants

---

## Summary Table

| # | Mismatch | Risk | Root Cause |
|---|----------|------|------------|
| 1 | Tags: `string[]` vs `{id, name, color}` | 🔴 High | Frontend simplified tag model |
| 2 | Client fields: 3 fields vs 7+ fields | 🟡 Medium | Frontend form not extended |
| 3 | Document: flattened vs sub-resources | 🟡 Medium | Different architectural approach |
| 4 | Thumbnail key naming | 🟢 Low | Easy to align |
| 5 | Archive boolean vs date heuristic | 🟡 Medium | Mock not yet replaced |
| 6 | Pagination shape | 🟢 Low | Needs explicit alignment |
| 7 | Client ref: string vs ID reference | 🟡 Medium | Display vs relational design |
| 8 | Permission model: flat vs 3-tier | 🟡 Medium | Different abstraction levels |
