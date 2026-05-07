# Route: /dms/fields (Custom Fields Configuration)

## Current State

Custom fields are currently **entirely client-side only** — stored in `MOCK_CUSTOM_FIELDS` constant, no service layer or API calls. Unlike other modules, there's no `DmsService` method for custom fields.

**New backend endpoints are needed** for persistence.

## Proposed Endpoints

### 1. LIST Custom Fields

**GET** `/companies/:companyId/dms/custom-fields`

### Response: `200 OK`

```typescript
CustomField[]

interface CustomField {
  id: string;
  name: string;
  type: "text" | "number" | "date" | "boolean" | "select" | "textarea";
  required: boolean;
  options?: string[];   // for "select" type
}
```

---

### 2. CREATE Custom Field

**POST** `/companies/:companyId/dms/custom-fields`

### Request Body

```typescript
{
  name: string;
  type: "text" | "number" | "date" | "boolean" | "select" | "textarea";
  required: boolean;
  options?: string[];   // required if type === "select"
}
```

### Response: `201 Created`

```typescript
CustomField
```

---

### 3. UPDATE Custom Field

**PATCH** `/companies/:companyId/dms/custom-fields/:id`

### Request Body

```typescript
Partial<{
  name: string;
  type: FieldType;
  required: boolean;
  options?: string[];
}>
```

### Response: `200 OK`

```typescript
CustomField
```

---

### 4. DELETE Custom Field

**DELETE** `/companies/:companyId/dms/custom-fields/:id`

### Response: `204 No Content`

---

### Consumed by
- **Store**: `loadCustomFieldsAction`, `createCustomFieldAction`, `updateCustomFieldAction`, `deleteCustomFieldAction`
- **Store atom**: `customFieldsAtom`
- **Page**: `pages/fields/index.tsx`

### Fields Page Features
- 3 tabs: **System** (read-only server fields), **Required** (always-on: title, type, client, date, tags, storage), **Optional** (user-managed custom fields)
- Custom field CRUD with 6 field types
- Toggle required on existing custom fields
- Field ordering (future enhancement)

### How Custom Fields Are Used
- Stored on each document's `meta.extras` as `Record<string, string>`
- Key = custom field name, value = field value
- Displayed in viewer/upload Forms as extra input fields
- Current mock has 4 predefined custom fields: `Numéro de marché`, `Fournisseur`, `Date d'échéance`, `Service concerné`
