# Route: /dms/shared (Shared Documents)

## 1. LIST Shared Documents

**GET** `/companies/:companyId/dms/shared`

### Query Parameters

| Param | Type | Required | Description |
|-------|------|----------|-------------|
| `page` | `number` | No | Page number |
| `limit` | `number` | No | Items per page |

### Response: `200 OK`

```typescript
PaginatedResponse<DmsDocument>   // Documents where current user is in sharedUsers
```

Returns documents where `sharedUsers` includes the current user's ID, but the current user is NOT the uploader.

### Notes
- "Shared with me" view — shows docs others have shared with current user
- Current mock behavior: filters `docs.filter(d => d.sharedUsers.includes("u1"))` where `u1` is hardcoded mock user
- **Backend should derive this from the authenticated user's ID**, not a query param

### Consumed by
- **Store**: no dedicated action (page currently reads directly from `docsAtom`)
- **Page**: `pages/shared/index.tsx` — grid view with doc card, shared-by avatar, date

### Current Mock Limitation
The shared page reads from `docsAtom` (all documents) and filters client-side. The `DmsService.listSharedWithMe()` exists but is not connected to the page. Suggestion: connect via a `loadSharedDocsAction` in the store.
