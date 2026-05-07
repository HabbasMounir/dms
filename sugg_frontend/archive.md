# Route: /dms/archive (Archived Documents)

## 1. LIST Archived Documents

**GET** `/companies/:companyId/dms/documents`

### Query Parameters

| Param | Type | Required | Description |
|-------|------|----------|-------------|
| `archived` | `boolean` | **Yes** | `true` — filter only archived docs |
| `page` | `number` | No | |
| `limit` | `number` | No | |
| `q` / `search` | `string` | No | Search within archived docs |

### Response: `200 OK`

```typescript
PaginatedResponse<DmsDocument>   // ocrText excluded
```

### Notes
- Same endpoint as main documents list but with `archived=true`
- Current mock: hardcoded filter `date < 2025-01-01`
- **Backend should use a proper `archived` boolean field**, not date-based heuristics

---

## 2. ARCHIVE a Document

**PATCH** `/companies/:companyId/dms/documents/:id`

### Request Body

```typescript
{ archived: true }
```

### Response: `200 OK`

```typescript
DmsDocument  // updated, without ocrText
```

### Consumed by
- **Store**: `archiveDocAction` → `DmsService.updateDocument(id, { archived: true })`
- **Action**: also clears `selectedDocAtom` if the archived doc was being viewed
- **Page**: `pages/archive/index.tsx` — archived list with search, year groups

### Archive Page Features
- Groups documents by year
- Shows count per year group
- Search bar to filter archived docs
- Empty state when no archived documents
