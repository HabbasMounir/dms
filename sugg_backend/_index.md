# DMS API Route Index

**Base Path:** `/api/v1/companies/:company_id/dms`
**Auth:** `Authorization: Bearer <token>`

---

## Tags (3 routes)

| Method   | Path                       | Permission      | File                                                                |
|----------|----------------------------|-----------------|---------------------------------------------------------------------|
| `GET`    | `/dms/tags`                | `dms:read`      | [GET_dms_tags.md](tags/GET_dms_tags.md)                             |
| `POST`   | `/dms/tags`                | `dms:update`    | [POST_dms_tags.md](tags/POST_dms_tags.md)                           |
| `DELETE` | `/dms/tags/:tag_id`        | `dms:delete`    | [DELETE_dms_tags_tag_id.md](tags/DELETE_dms_tags_tag_id.md)         |

## Clients (4 routes)

| Method   | Path                           | Permission       | File                                                                        |
|----------|--------------------------------|------------------|-----------------------------------------------------------------------------|
| `GET`    | `/dms/clients`                 | `dms:read`       | [GET_dms_clients.md](clients/GET_dms_clients.md)                            |
| `POST`   | `/dms/clients`                 | `dms:create`     | [POST_dms_clients.md](clients/POST_dms_clients.md)                          |
| `PATCH`  | `/dms/clients/:client_id`      | `dms:update`     | [PATCH_dms_clients_client_id.md](clients/PATCH_dms_clients_client_id.md)    |
| `DELETE` | `/dms/clients/:client_id`      | `dms:delete`     | [DELETE_dms_clients_client_id.md](clients/DELETE_dms_clients_client_id.md)  |

## Documents (7 routes)

| Method   | Path                              | Permission       | File                                                                                |
|----------|-----------------------------------|------------------|-------------------------------------------------------------------------------------|
| `GET`    | `/dms/documents`                  | `dms:read`       | [GET_dms_documents.md](documents/GET_dms_documents.md)                              |
| `POST`   | `/dms/documents`                  | `dms:create`     | [POST_dms_documents.md](documents/POST_dms_documents.md)                            |
| `GET`    | `/dms/documents/:id`              | `dms:read`       | [GET_dms_documents_id.md](documents/GET_dms_documents_id.md)                        |
| `PATCH`  | `/dms/documents/:id`              | `dms:update`     | [PATCH_dms_documents_id.md](documents/PATCH_dms_documents_id.md)                    |
| `DELETE` | `/dms/documents/:id`              | `dms:delete`     | [DELETE_dms_documents_id.md](documents/DELETE_dms_documents_id.md)                  |
| `GET`    | `/dms/documents/:id/full`         | `dms:read`       | [GET_dms_documents_id_full.md](documents/GET_dms_documents_id_full.md)              |
| `GET`    | `/dms/documents/:id/download`     | `dms:read`       | [GET_dms_documents_id_download.md](documents/GET_dms_documents_id_download.md)      |

## Sub-resources (9 routes)

### OCR Text

| Method   | Path                              | Permission       | File                                                                                              |
|----------|-----------------------------------|------------------|---------------------------------------------------------------------------------------------------|
| `GET`    | `/dms/documents/:id/text`         | `dms:read`       | [GET_dms_documents_id_text.md](sub-resources/GET_dms_documents_id_text.md)                        |
| `POST`   | `/dms/documents/:id/text`         | `dms:update`     | [POST_dms_documents_id_text.md](sub-resources/POST_dms_documents_id_text.md)                      |

### Metadata

| Method   | Path                              | Permission       | File                                                                                              |
|----------|-----------------------------------|------------------|---------------------------------------------------------------------------------------------------|
| `GET`    | `/dms/documents/:id/metadata`     | `dms:read`       | [GET_dms_documents_id_metadata.md](sub-resources/GET_dms_documents_id_metadata.md)                |
| `POST`   | `/dms/documents/:id/metadata`     | `dms:update`     | [POST_dms_documents_id_metadata.md](sub-resources/POST_dms_documents_id_metadata.md)              |

### Notes

| Method   | Path                              | Permission       | File                                                                                              |
|----------|-----------------------------------|------------------|---------------------------------------------------------------------------------------------------|
| `GET`    | `/dms/documents/:id/notes`        | `dms:read`       | [GET_dms_documents_id_notes.md](sub-resources/GET_dms_documents_id_notes.md)                      |
| `POST`   | `/dms/documents/:id/notes`        | `dms:update`     | [POST_dms_documents_id_notes.md](sub-resources/POST_dms_documents_id_notes.md)                    |

### Access Control

| Method   | Path                                      | Permission            | File                                                                                                      |
|----------|-------------------------------------------|-----------------------|-----------------------------------------------------------------------------------------------------------|
| `GET`    | `/dms/documents/:id/access`               | `dms:read`            | [GET_dms_documents_id_access.md](sub-resources/GET_dms_documents_id_access.md)                            |
| `POST`   | `/dms/documents/:id/access`               | `dms:manage_access`   | [POST_dms_documents_id_access.md](sub-resources/POST_dms_documents_id_access.md)                          |
| `DELETE` | `/dms/documents/:id/access/:grant_id`     | `dms:manage_access`   | [DELETE_dms_documents_id_access_grant_id.md](sub-resources/DELETE_dms_documents_id_access_grant_id.md)    |

---

**Total: 23 routes**
