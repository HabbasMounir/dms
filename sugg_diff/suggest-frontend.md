# Suggestions for Frontend

> Prioritized recommendations to align the frontend spec (`sugg_frontend/`) with the backend API (`sugg_backend/`).

---

## Phase 1: Critical (🔴) — Must-Fix for Backend Compatibility

### 1. Update Tag Model from `string[]` to Structured Objects

**Current:** `tags: string[]` — flat array of name strings

**Needed:** Match backend's structured tag model:

```typescript
// Current (won't work with backend):
interface DmsDocument {
  tags: string[];
}

// Proposed:
interface Tag {
  id: string;       // UUID — stable identifier
  name: string;     // display name
  color: string;    // hex color, e.g. "#6ea8fe"
}

interface DmsDocument {
  tags: Tag[];      // full objects, not just names
}
```

**Changes required:**
- Tag filter dropdown: render colored badges from `tag.color`
- Tag management page: use `id` for deletion, not name
- Document display: show colored tag pills
- `tagsAtom`: store `Tag[]` instead of `string[]`
- Upload form: send `tag_ids: string[]` instead of `tags: string[]`

### 2. Enrich Client Interface

**Current:** `{ id: string; name: string; ref: string; city: string }`

**Needed:** Match backend's richer model:

```typescript
interface DmsClient {
  id: string;
  name: string;
  ref?: string;         // make optional — backend doesn't require it
  city?: string;        // make optional
  email?: string;
  phone?: string;
  address?: string;     // backend includes this (sugg_backend/clients/POST_dms_clients.md)
  notes?: string;
}
```

**Changes required:**
- Add email, phone, address, notes fields to client form
- Make ref and city optional in the form (backend only requires `name`)

### 3. Normalize Document Interface: Flattened → Sub-Resource Aware

**Current:** `DmsDocument` is monolithic with all data at root level.

**Recommended:** Align with backend's sub-resource pattern:

```typescript
interface DmsDocument {
  id: string;
  title: string;
  type: DocType;
  date: string;
  client_id: string;            // use ID, not name string
  client_name?: string;         // include name for display (from JOIN)
  size: string;
  pages: number;
  tags: Tag[];
  archived: boolean;
  uploaded_by?: string;
  uploaded_at?: string;
  meta: {
    ref: string;
    currency: string;
    amount: string;
    vat: string;
    archive_places: ArchivePlace[];
    extras?: Record<string, string>;
  };
  thumbs: ThumbnailSet;
  mime_type?: string;
  // Sub-resources are fetched SEPARATELY:
  // text  → GET /:id/text        (lazy-loaded when OCR tab opened)
  // notes → GET /:id/notes       (lazy-loaded when notes section expanded)
  // metadata → GET /:id/metadata (lazy-loaded)
  // access → GET /:id/access     (lazy-loaded when share popover opened)
}
```

**Changes required:**
- Remove `sharedUsers`, `sharedRoles`, `notes`, `permissions` from root `DmsDocument`
- Add `client_id` + `client_name` fields to replace single `client: string`
- Create separate store atoms for each sub-resource type
- Use the `/full` endpoint for detail view when all data is needed at once

---

## Phase 2: Important (🟡) — Feature Completeness

### 4. Wire Share Popover to Per-Document Access Endpoints

**Current:** Share popover reads `usersAtom`, `rolesAtom`, `dmsAccessProfilesAtom` but doesn't call per-document access endpoints.

**Needed:** Connect to backend's 3 access endpoints:

```typescript
// Store actions to add:
loadDocumentAccess(documentId: string): Promise<AccessGrant[]>
grantDocumentAccess(documentId: string, grant: { user_id?: string; role_id?: string; permission: "read" | "write" | "admin" }): Promise<void>
revokeDocumentAccess(documentId: string, grantId: string): Promise<void>
```

**Flow:**
1. Open share popover → `GET /dms/documents/:id/access` → display current grants
2. Add user/role → `POST /dms/documents/:id/access` → refresh list
3. Remove grant → `DELETE /dms/documents/:id/access/:grant_id` → refresh list

### 5. Connect Shared Page to Backend

**Current:** Shared page reads from `docsAtom` (all documents) and filters client-side with hardcoded user ID `"u1"`.

**Needed:**

```typescript
// Add to store:
loadSharedDocsAction: (params: { page?: number; limit?: number }) => Promise<void>
sharedDocsAtom: Atom<PaginatedResponse<DmsDocument>>
```

Replace the client-side filter with a call to `GET /dms/documents/shared`. Remove the hardcoded `"u1"` — backend derives user from JWT.

### 6. Use `GET /dms/documents/:id/full` for Detail View

**Current:** Detail view makes multiple sequential calls for different data slices.

**Optimization:** Use `GET /dms/documents/:id/full` on the document detail page load. This one endpoint returns everything (main fields + tags + client + texts + metadata + notes + access) with parallel sub-queries on the backend side.

```typescript
// Add to store:
loadFullDocumentAction(documentId: string): Promise<DmsDocumentFull>

interface DmsDocumentFull extends DmsDocument {
  text_rows: OcrTextRow[];
  notes: Note[];
  metadata: MetadataEntry[];
  access_grants: AccessGrant[];
}
```

### 7. Add Original Thumbnail Size

**Current:** Frontend spec includes `original` in thumbnails but only documents sizes `sm`, `md`, `lg`, `xl` for the preview endpoint.

**Fix:** Ensure the `original` size is also covered by the preview endpoint or treat it as a special case (full-res download rather than thumbnail).

---

## Phase 3: Polish (🟢) — Edge Cases & UX

### 8. Handle Expiring Download URLs Gracefully

Backend presigned URLs expire after 15 minutes.

```typescript
// Frontend: regenerate if expired
const downloadDocument = async (docId: string) => {
  const { url } = await DmsService.getDownloadUrl(docId);
  // If user needs more time, regenerate:
  // const { url: newUrl } = await DmsService.getDownloadUrl(docId);
  window.open(url, '_blank');
};
```

**Consider:** Add an "expired" state or "regenerate link" button if URL fails.

### 9. Handle Soft-Deleted Clients

Backend uses soft-delete for clients. Documents can reference clients that have been marked as deleted.

```typescript
// In client list/filter:
loadClientsAction: (includeDeleted?: boolean) => void

// In document display:
// Show "(deleted)" badge next to client name if client is soft-deleted
```

### 10. Add Error States for All API Calls

Backend can return 404, 413, 415, and permission errors. Ensure the frontend spec covers:

| Error | Where | Handling |
|-------|-------|----------|
| `404 Not Found` | Document detail, client detail | Show "not found" state, redirect |
| `413 Payload Too Large` | Upload | Show size limit message |
| `415 Unsupported Media Type` | Upload | Show format requirements |
| `403 Forbidden` | Any endpoint with insufficient permission | Show "access denied" message |

---

## Summary Priority Matrix

| Priority | Change | Effort |
|----------|--------|--------|
| 🔴 Phase 1 | Update tag model to structured `Tag[]` | Medium |
| 🔴 Phase 1 | Enrich client interface (email, phone, etc.) | Low |
| 🔴 Phase 1 | Normalize DmsDocument (sub-resources, client_id) | Medium |
| 🟡 Phase 2 | Wire share popover to per-doc access endpoints | Medium |
| 🟡 Phase 2 | Connect shared page to backend | Low |
| 🟡 Phase 2 | Use `/full` endpoint for detail views | Low |
| 🟡 Phase 2 | Add original thumbnail size to preview | Low |
| 🟢 Phase 3 | Handle expiring download URLs | Low |
| 🟢 Phase 3 | Handle soft-deleted clients | Low |
| 🟢 Phase 3 | Add error states for all API calls | Low |
