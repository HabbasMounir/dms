# Route: /dms/main (Documents List) & /dms/main/view/:id (Document Viewer)

## 1. LIST Documents

**GET** `/companies/:companyId/dms/documents`

### Query Parameters

| Param | Type | Required | Description |
|-------|------|----------|-------------|
| `page` | `number` | No | Page number (default: 1) |
| `limit` | `number` | No | Items per page (default: 50) |
| `q` / `search` | `string` | No | Full-text search — matches title, OCR text, ref, tags |
| `type` | `DocType` | No | Filter by document type (`invoice`, `contract`, `bank`, `order`, `report`, `receipt`, `tax`, `legal`) |
| `clientId` | `string` | No | Filter by client ID |
| `tags[]` | `string[]` | No | Filter by tags (OR logic — doc tag matches any) |
| `archived` | `boolean` | No | Filter archived status (default: only active) |
| `sortBy` | `string` | No | Sort field (e.g. `date`, `title`) |
| `sortOrder` | `"asc" \| "desc"` | No | Sort direction |

### Response: `200 OK`

```typescript
interface PaginatedResponse<DmsDocument> {
  data: DmsDocument[];   // ocrText EXCLUDED from list for performance
  page: number;
  limit: number;
  total: number;
}
```

`DmsDocument` shape (without `ocrText`):

```typescript
interface DmsDocument {
  id: string;
  client: string;
  title: string;
  type: "invoice" | "contract" | "bank" | "order" | "report" | "receipt" | "tax" | "legal";
  date: string;           // "YYYY-MM-DD"
  dateCreated?: string;   // "YYYY-MM-DD"
  size: string;           // e.g. "1.2 MB"
  pages: number;
  tags: string[];
  sharedUsers: string[];
  sharedRoles: string[];
  thumbs: {
    blur: string;         // 40x52 blurred placeholder
    thumb_sm: string;     // 150x200
    thumb_md: string;     // 400x520
    thumb_lg: string;     // 800x1040
    thumb_xl: string;     // 1200x1560
    original: string;     // full resolution
  };
  meta: {
    ref: string;
    currency: string;
    amount: string;
    vat: string;
    archivePlaces: ArchivePlace[];
    extras?: Record<string, string>;   // custom field values
  };
  notes?: Array<{ id: string; text: string; author: string; date: string }>;
  permissions?: DocPermEntry[];
  uploadedBy?: string;
  uploadedAt?: string;    // ISO timestamp
  mimeType?: string;
  company?: string;
  archived?: boolean;
}
```

### Consumed by
- **Store**: `loadDocsAction` → `docsAtom`
- **Page**: `pages/main/index.tsx`
- **Filter state**: `dmsFiltersAtom` (search, clientId, tags, types, groupBy, viewMode)

---

## 2. GET Single Document

**GET** `/companies/:companyId/dms/documents/:id`

### Response: `200 OK`

```typescript
DmsDocument  // WITHOUT ocrText — same as list shape
```

### Error
- `404 Not Found` — document ID doesn't exist

### Consumed by
- **Store**: no action directly (page uses `selectedDocAtom`)
- **Page**: `pages/main/view/[id].tsx` — reads `:id` param, looks up in store, falls back to loading

---

## 3. GET Document OCR Text

**GET** `/companies/:companyId/dms/documents/:id/text`

### Response: `200 OK`

```typescript
interface { ocrText: string }
```

### Notes
- OCR text is **excluded** from list and single-doc responses for performance
- Loaded on demand when user opens OCR tab
- Can be large (multi-page OCR content)

### Consumed by
- **Page**: `pages/main/components/DocumentViewer.tsx` — OCR tab
- **Page**: `pages/main/view.tsx` — OCR tab

---

## 4. UPDATE Document

**PATCH** `/companies/:companyId/dms/documents/:id`

### Request Body

```typescript
Partial<DmsDocument>
// Typical patch fields: title, client, type, date, tags, meta (ref, currency, amount, archivePlaces, extras), notes, permissions, archived
```

### Response: `200 OK`

```typescript
DmsDocument  // updated, WITHOUT ocrText
```

### Consumed by
- **Store**: `updateDocAction` (title editing, metadata changes, archive toggle)
- **Store**: `archiveDocAction` (sets `{ archived: true }`)
- **Pages**: viewer, list inline edits

---

## 5. DELETE Document

**DELETE** `/companies/:companyId/dms/documents/:id`

### Response: `204 No Content`

### Consumed by
- **Store**: `deleteDocAction`
- **Pages**: main list (delete button), viewer (delete button)

---

## 6. GET Document Preview URL

**GET** `/companies/:companyId/dms/documents/:id/preview?size=sm|md|lg|xl`

### Query Parameters

| Param | Type | Required | Description |
|-------|------|----------|-------------|
| `size` | `"sm" \| "md" \| "lg" \| "xl"` | Yes | Preview resolution |

### Response: `200 OK`

```typescript
interface { url: string }   // signed URL or data URL
```

### Consumed by
- **Page**: viewer — `Thumb` component, preview panel

---

## 7. DOWNLOAD Document

**GET** `/companies/:companyId/dms/documents/:id/download`

### Response: `200 OK`

Binary stream with `Content-Disposition: attachment` header.

### Consumed by
- **Page**: viewer — Download button

---

## 8. GET Tags (for filter dropdown)

**GET** `/companies/:companyId/dms/tags`

### Response: `200 OK`

```typescript
string[]   // distinct tags across all documents
```

### Consumed by
- **Store**: `loadTagsAction` → `tagsAtom`
- **Page**: main list — tag filter dropdown

---

## 9. GET Clients (for filter dropdown)

**GET** `/companies/:companyId/dms/clients`

### Response: `200 OK`

```typescript
DmsClient[]   // clients with at least 1 document
```

### Consumed by
- **Store**: `loadClientsAction` → `clientsAtom`
- **Page**: main list — client filter dropdown

---

## 10. GET Users (for share popover)

**GET** `/companies/:companyId/dms/users`

### Response: `200 OK`

```typescript
Array<{ id: string; name: string; email: string }>
```

### Consumed by
- **Store**: `loadUsersAction` → `usersAtom`
- **Component**: `SharePopover.tsx` — user selection

---

## 11. GET Roles (for share popover)

**GET** `/companies/:companyId/dms/roles`

### Response: `200 OK`

```typescript
Array<{ id: string; name: string; permissions: string[] }>
```

### Consumed by
- **Store**: `loadRolesAction` → `rolesAtom`
- **Component**: `SharePopover.tsx` — role selection

---

## 12. GET Access Profiles (for permission display)

**GET** `/companies/:companyId/dms/access-profiles`

### Response: `200 OK`

```typescript
DmsAccessProfile[]
```

### Consumed by
- **Store**: `loadDmsAccessProfilesAction` → `dmsAccessProfilesAtom`
- **Pages**: viewer — permission tabs

---

## 13. GET Archive Places (for document meta)

**GET** `/companies/:companyId/dms/archive-places`

### Response: `200 OK`

```typescript
ArchivePlace[]
```

### Consumed by
- **Store**: `loadArchivePlacesAction` → `archivePlacesAtom`
- **Pages**: viewer, upload — archive place selector
