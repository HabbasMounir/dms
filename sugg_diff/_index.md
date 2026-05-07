# DMS API — Frontend vs Backend Gap Analysis

> Generated: Saturday, April 25, 2026
>
> Compares `sugg_frontend/` (9 page specs) against `sugg_backend/` (23 route specs) to identify gaps, mismatches, and improvement opportunities.

## Coverage Overview

| Frontend Page | File | Backend Routes Covered | Missing Backend Routes | Status |
|---|---|---|---|---|
| **Documents List & Viewer** | `main.md` | `GET /dms/documents`, `GET /dms/documents/:id`, `GET /dms/documents/:id/text`, `PATCH /dms/documents/:id`, `DELETE /dms/documents/:id`, `GET /dms/documents/:id/download`, `GET /dms/tags`, `GET /dms/clients` | `GET /dms/documents/:id/preview`, `GET /dms/users`, `GET /dms/roles`, `GET /dms/access-profiles`, `GET /dms/archive-places` | ⚠️ Partial |
| **Upload** | `upload.md` | `POST /dms/documents` (multipart) | — | ✅ Full |
| **Scan (Camera)** | `scan.md` | `POST /dms/documents` (multipart) | `POST /dms/documents/:id/ocr` | ⚠️ Partial |
| **Archive** | `archive.md` | `GET /dms/documents?archived=true`, `PATCH /dms/documents/:id` | — | ✅ Full |
| **Correspondents (Clients)** | `correspondents.md` | `GET /dms/clients`, `POST /dms/clients`, `PATCH /dms/clients/:id`, `DELETE /dms/clients/:id` | — | ✅ Full |
| **Tags Management** | `tags.md` | `GET /dms/tags` | `POST /dms/tags`, `DELETE /dms/tags/:name` (model mismatch) | ⚠️ Partial |
| **Permissions (Users/Roles/Profiles)** | `permissions.md` | — | `GET /dms/users`, `GET /dms/roles`, CRUD `/dms/access-profiles` (4), CRUD `/dms/archive-places` (4), `GET /dms/storage-spaces`, `PUT /dms/storage-spaces` | ❌ Missing |
| **Custom Fields** | `fields.md` | — | CRUD `/dms/custom-fields` (4) | ❌ Missing |
| **Shared With Me** | `shared.md` | — | `GET /dms/shared` | ❌ Missing |

## Legend

| Icon | Meaning |
|------|---------|
| ✅ Full | All frontend-expected endpoints exist in backend spec |
| ⚠️ Partial | Some endpoints exist but some are missing or have model mismatches |
| ❌ Missing | No corresponding backend endpoints exist |

## Key Counts

| Metric | Count |
|--------|-------|
| Backend routes documented | 23 |
| Frontend-expected endpoints not in backend | ~18 |
| Covered routes | ~6 |
| Model/data-shape mismatches | ~6 |
| Backend features frontend doesn't use | ~5 |

---

### See Also

- [Backend Missing Endpoints](gap-backend-missing.md) — endpoints frontend needs that backend spec lacks
- [Model Mismatches](gap-model-mismatches.md) — structural differences between TS interfaces and Rust models
- [Frontend-Unused Backend Features](gap-frontend-unused.md) — backend capabilities frontend could leverage
- [Suggestions for Backend](suggest-backend.md) — prioritized recommendations for the backend team
- [Suggestions for Frontend](suggest-frontend.md) — prioritized recommendations for the frontend team
