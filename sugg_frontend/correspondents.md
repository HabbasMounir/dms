# Route: /dms/correspondents (Clients / Correspondents CRUD)

Also aliased from `/dms/clients` → redirect to `/dms/correspondents`.

## 1. LIST Clients

**GET** `/companies/:companyId/dms/clients`

### Response: `200 OK`

```typescript
DmsClient[]

interface DmsClient {
  id: string;
  name: string;
  ref: string;
  city: string;
}
```

### Notes
- Returns ALL clients (not just those with documents)
- Used for filter dropdowns AND the correspondents management page

---

## 2. CREATE Client

**POST** `/companies/:companyId/dms/clients`

### Request Body

```typescript
{
  name: string;   // required
  ref: string;    // required — client reference code
  city: string;   // required
}
```

### Response: `201 Created`

```typescript
DmsClient
```

---

## 3. UPDATE Client

**PATCH** `/companies/:companyId/dms/clients/:id`

### Request Body

```typescript
Partial<{
  name: string;
  ref: string;
  city: string;
}>
```

### Response: `200 OK`

```typescript
DmsClient
```

---

## 4. DELETE Client

**DELETE** `/companies/:companyId/dms/clients/:id`

### Response: `204 No Content`

### Notes
- Should check if client has associated documents before deletion (or cascade)

---

### Consumed by
- **Store**: `loadClientsAction`, `createClientAction`, `updateClientAction`, `deleteClientAction`
- **Store atoms**: `clientsAtom`
- **Page**: `pages/correspondents/index.tsx`
- **Component features**: search bar, add/edit form (name/ref/city), inline delete with confirmation, doc count per client, stats row (total, cities, documents)

### DmsClient Type Reference

```typescript
interface DmsClient {
  id: string;
  name: string;
  ref: string;
  city: string;
}
```
