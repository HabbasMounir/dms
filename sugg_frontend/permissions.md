# Route: /dms/permissions (Roles, Users, Access Profiles, Storage Spaces)

## Overview

This covers 3 sub-pages under the "Roles" nav item:
- **Utilisateurs** (Users) — view-only, shows user cards with permission summary
- **Rôles** (Roles) — role cards with editable permission toggles, member avatars
- **Profils DMS** (Access Profiles) — full CRUD with color picker, permission toggles, member stacks

Also covers `/dms/storage` → `/dms/places` (Storage Spaces management).

---

## 1. LIST Users

**GET** `/companies/:companyId/dms/users`

### Response: `200 OK`

```typescript
Array<{ id: string; name: string; email: string }>
```

### Notes
- Read-only in the UI (no create/update/delete for users here)
- Users are managed at the system/auth level

---

## 2. LIST Roles

**GET** `/companies/:companyId/dms/roles`

### Response: `200 OK`

```typescript
Array<{
  id: string;
  name: string;
  permissions: string[];   // e.g. ["read", "write", "admin"]
}>
```

### Notes
- UI shows permission toggles but currently read-only for role permissions themselves
- Member avatars shown but member management may be system-level

---

## 3. LIST Access Profiles

**GET** `/companies/:companyId/dms/access-profiles`

### Response: `200 OK`

```typescript
DmsAccessProfile[]

interface DmsAccessProfile {
  id: string;
  name: string;
  color: string;           // hex color for UI badge
  description?: string;
  permissions: ("read" | "get" | "update" | "delete" | "send")[];
}
```

### 4. CREATE Access Profile

**POST** `/companies/:companyId/dms/access-profiles`

### Request Body

```typescript
{
  name: string;
  color: string;
  description?: string;
  permissions: DmsPermLevel[];
}
```

### Response: `201 Created`

```typescript
DmsAccessProfile
```

### 5. UPDATE Access Profile

**PATCH** `/companies/:companyId/dms/access-profiles/:id`

### Request Body

```typescript
Partial<{
  name: string;
  color: string;
  description: string;
  permissions: DmsPermLevel[];
}>
```

### Response: `200 OK`

```typescript
DmsAccessProfile
```

### 6. DELETE Access Profile

**DELETE** `/companies/:companyId/dms/access-profiles/:id`

### Response: `204 No Content`

---

## 7. LIST Archive Places (for storage page)

**GET** `/companies/:companyId/dms/archive-places`

(Already documented in `main.md` — used here too)

### 8. CREATE Archive Place

**POST** `/companies/:companyId/dms/archive-places`

### Request Body

```typescript
{
  type: "physical" | "digital" | "cloud";
  label: string;
  location_detail: string;
}
```

### Response: `201 Created`

```typescript
ArchivePlace
```

### 9. UPDATE Archive Place

**PATCH** `/companies/:companyId/dms/archive-places/:id`

### Request Body

```typescript
Partial<{ type: ArchivePlaceType; label: string; location_detail: string }>
```

### Response: `200 OK`

```typescript
ArchivePlace
```

### 10. DELETE Archive Place

**DELETE** `/companies/:companyId/dms/archive-places/:id`

### Response: `204 No Content`

---

## 11. LIST Storage Spaces

**GET** `/companies/:companyId/dms/storage-spaces`

### Response: `200 OK`

```typescript
StorageSpace[]

interface StorageSpace {
  id: string;
  name: string;
  icon: string;
  color: string;
  tag?: string;
  rooms: StorageRoom[];        // nested hierarchy
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

### 12. REPLACE Storage Spaces

**PUT** `/companies/:companyId/dms/storage-spaces`

### Request Body

```typescript
StorageSpace[]   // full replacement of the collection
```

### Response: `200 OK`

(or `204 No Content`)

---

### Consumed by

| Store Action | Endpoint |
|---|---|
| `loadUsersAction` → `usersAtom` | GET `/users` |
| `loadRolesAction` → `rolesAtom` | GET `/roles` |
| `loadDmsAccessProfilesAction` → `dmsAccessProfilesAtom` | GET `/access-profiles` |
| `createDmsAccessProfileAction` | POST `/access-profiles` |
| `updateDmsAccessProfileAction` | PATCH `/access-profiles/:id` |
| `deleteDmsAccessProfileAction` | DELETE `/access-profiles/:id` |
| `loadArchivePlacesAction` → `archivePlacesAtom` | GET `/archive-places` |
| `createArchivePlaceAction` | POST `/archive-places` |
| `deleteArchivePlaceAction` | DELETE `/archive-places/:id` |
| `loadStoragePlacesAction` → `storagePlacesAtom` | GET `/storage-spaces` |
| `saveStorageSpacesAction` | PUT `/storage-spaces` |

### Pages
- **Page**: `pages/permissions/index.tsx` — 3 pill tabs (Utilisateurs, Rôles, Profils DMS)
- **Page**: `pages/places/index.tsx` — 3 type sections (Physical, Digital, Cloud) with CRUD
- **Note**: `pages/storage/index.tsx` re-exports `pages/places/index.tsx`
