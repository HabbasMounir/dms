# Backend Features the Frontend Doesn't Leverage

> Backend capabilities documented in `sugg_backend/` that the frontend spec (`sugg_frontend/`) doesn't currently use or mention.

---

## 1. `GET /dms/documents/:id/full` — Bulk Document Fetch

**Backend:** Returns ALL document data in a single call: main fields + tags + client + texts + metadata + notes + access. Sub-queries fire in parallel for efficiency.

**Frontend Current Approach:**
- Fetches `GET /dms/documents/:id` for main fields
- Then lazy-loads `GET /dms/documents/:id/text` when user opens OCR tab
- Other sub-resources fetched individually as needed

**Missed Opportunity:**
- The detail view (`pages/main/view/[id].tsx`) could use `/full` for an immediate, complete document load — eliminating multiple round trips.
- Especially valuable for the first load where most sub-resources (notes, metadata, access) will likely be viewed.
- Saves 4+ individual API calls per document view.

**Suggestion:** Use `/full` for the primary document detail page load. Keep lazy-loading for the documents list (where only basic fields are needed).

---

## 2. Per-Document Access Control Endpoints

**Backend:** 3 dedicated endpoints for document-level access management:

| Method | Path | Purpose |
|--------|------|---------|
| `GET` | `/dms/documents/:id/access` | List access grants |
| `POST` | `/dms/documents/:id/access` | Grant access to user/role |
| `DELETE` | `/dms/documents/:id/access/:grant_id` | Revoke access |

**Frontend Current Approach:**
- `DmsDocument` has a `permissions?: DocPermEntry[]` field but no explicit wiring to these endpoints
- Share popover (`SharePopover.tsx`) uses `loadUsersAction`, `loadRolesAction`, `loadDmsAccessProfilesAction` — but doesn't call per-document access endpoints
- The `permissions.md` spec describes access profiles at the template/role level, not per-document grants

**Missed Opportunity:**
- The share popover should POST to `/dms/documents/:id/access` when sharing, and DELETE when removing access
- The viewer should fetch `GET /dms/documents/:id/access` to display current grants

**Suggestion:** Wire the share popover and document viewer to the per-document access endpoints.

---

## 3. Structured Sub-Resource Mutation Endpoints

**Backend:** Separate endpoints for manipulating document sub-resources:

| Endpoint | Purpose |
|----------|---------|
| `POST /dms/documents/:id/text` | Add OCR text rows |
| `POST /dms/documents/:id/metadata` | Upsert metadata key-value |
| `POST /dms/documents/:id/notes` | Add a note |

**Frontend Current Approach:**
- The `PATCH /dms/documents/:id` endpoint is used as a catch-all for updating document fields including notes and metadata
- Notes, metadata, and text are treated as part of the monolithic `DmsDocument` object

**Missed Opportunity:**
- Using dedicated sub-resource endpoints allows for:
  - **Audit trail**: Each note or metadata change is a separate record with timestamps and authors
  - **Concurrent edits**: Multiple users can add notes/text without race conditions on the parent document
  - **Partial updates**: No need to send the entire document payload for a single note addition
  - **History**: Backend can track when each text row, note, or metadata entry was added

**Suggestion:** Frontend should use the dedicated sub-resource endpoints for text, metadata, and notes mutations, reserving `PATCH /documents/:id` for top-level field changes only (title, type, client, tags, archived, meta fields).

---

## 4. Structured Tag Model (Color, ID)

**Backend:** Tags are structured objects with `id`, `name`, and `color` stored in `dms_tags` table, linked via `dms_document_tags` junction table.

**Frontend Current Approach:** Tags are treated as `string[]` — simple name strings with no color or stable ID.

**Missed Opportunity:**
- **Tag colors** could be displayed as colored badges in the document list and filter dropdown — improving visual scanability
- **Stable IDs** allow tags to be renamed without breaking document associations (rekeys are fragile)
- **Tag usage counts** could be displayed on the tags management page

**Suggestion:** Update the frontend tag model to `Tag[]` where `Tag = { id: string; name: string; color: string }`. Display colors as badges/circles. Use IDs for deletion and association.

---

## 5. Soft-Delete Semantics for Clients

**Backend:** Clients use soft-delete (`is_deleted = TRUE`). Existing document references are preserved.

**Frontend Current Approach:** No mention of soft-delete or handling deleted clients.

**Missed Opportunity:**
- Frontend could display "(deleted)" or similar indicator for documents referencing a soft-deleted client
- The correspondents management page could show a "show deleted" toggle
- Prevents confusion when a referenced client has been deleted but documents still reference it

**Suggestion:** Add handling for soft-deleted clients in the client filter dropdown and document display.

---

## 6. Presigned Download URLs with Expiry

**Backend:** `GET /dms/documents/:id/download` returns a presigned MinIO download URL with 15-minute expiry (900 seconds).

**Frontend Current Approach:** No specific handling of expiring URLs — the download button just triggers the download.

**Potential Issue:**
- If a user opens the download link in a new tab but doesn't download immediately, the URL may expire after 15 minutes
- The frontend needs to be aware of the ephemeral nature of these URLs

**Suggestion:** Frontend should either:
- Use the download endpoint as a proxy (stream through the server, not just redirect)
- Or handle URL expiry gracefully (show "link expired, please try again" / regenerate)

---

## Summary

| # | Feature | Backend Endpoint | Value for Frontend | Effort |
|---|---------|-----------------|--------------------|--------|
| 1 | `/full` endpoint | `GET /dms/documents/:id/full` | Eliminates N+1 round trips on detail views | 🟢 Low |
| 2 | Per-doc access grants | 3 endpoints under `/:id/access` | Enables real share popover functionality | 🟡 Medium |
| 3 | Sub-resource mutations | `POST /:id/text`, `/:id/metadata`, `/:id/notes` | Audit trail, concurrent edits, cleaner code | 🟡 Medium |
| 4 | Structured tags | Structured `{id, name, color}` | Colored badges, stable associations | 🟡 Medium |
| 5 | Client soft-delete | `is_deleted` flag | Graceful handling of deleted references | 🟢 Low |
| 6 | Expiring download URLs | 15-min presigned URLs | Prevent broken downloads, show expiration UX | 🟢 Low |
