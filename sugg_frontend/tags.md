# Route: /dms/tags (Tags Management)

## 1. LIST Tags

**GET** `/companies/:companyId/dms/tags`

### Response: `200 OK`

```typescript
string[]   // distinct array of tag strings across all documents
```

### Notes
- Returns flat array of tag strings (e.g. `["urgent", "fournisseur", "2024", "facture"]`)
- Used for filter dropdowns AND the tags management page

---

## 2. (Optional) Tags CRUD

Currently the service only supports listing tags. The tags page has **add/delete** UI but these are done in-memory only (no backend endpoints). If you want server-side tag management, consider:

**POST** `/companies/:companyId/dms/tags`
```typescript
Request:  { name: string }
Response: 201 { name: string }
```

**DELETE** `/companies/:companyId/dms/tags/:name`
```typescript
Response: 204 No Content
```

### Current Behavior
- Tags page allows adding new tags and deleting with confirmation
- Doc count per tag is shown
- Currently all operations are client-side only (no real persistence)
- Tags are stored as `string[]` on each document

### Consumed by
- **Store**: `loadTagsAction` → `tagsAtom`
- **Page**: `pages/tags/index.tsx`
- **Component features**: inline add, search/filter, list with doc counts, delete with confirmation

### Alternative Approach
If tags are purely derived from document metadata, the client can extract distinct tags from the documents list without a dedicated endpoint. However, a dedicated endpoint is cleaner and allows for:
- Autocomplete suggestions when adding tags
- Tag usage statistics
- Future tag metadata (color, description)
