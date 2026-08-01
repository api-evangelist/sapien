---
name: Create a PoQ project and upload data
description: Create a Sapien Proof of Quality project from a poq.toml spec, open an upload session, declare and upload data files, and start ingestion.
api: openapi/sapien-poq-openapi-original.json
operations: [ping, createProject, createUploadSession, declareUploadFiles, processUploadSession, getUploadSessionStatus]
---

# Create a PoQ project and upload data

Use the Sapien Proof of Quality Developer API (`https://sapien-poq.up.railway.app/developer/v1`).

## Auth
Every request needs `Authorization: Bearer poq_live_...`. Project and upload endpoints require the key creator to be an org originator (else `403 not_org_originator`).

## Steps
1. `ping` — verify the key is valid before doing work.
2. `createProject` — POST `/projects` with the project `name` and the `poq.toml` spec. Invalid specs return `400 project_spec_invalid`. Keep the returned `projectId`.
3. `createUploadSession` — POST `/projects/{projectId}/upload-sessions` with the file manifest. Keep the `uploadId`.
4. `declareUploadFiles` — POST `/projects/{projectId}/upload-sessions/{uploadId}/files` to declare files and get presigned URLs. Rules: ≤5 GiB/file, ≤1000 files per declare call, relative paths only (no leading `/`, no `..`). Presigned URLs expire after 1 hour. PUT the bytes directly to the presigned URLs (bytes never go through the API).
5. `processUploadSession` — POST `/projects/{projectId}/upload-sessions/{uploadId}/process` to verify uploads and start ingestion.
6. `getUploadSessionStatus` — GET `/projects/{projectId}/upload-sessions/{uploadId}/status`; poll until complete or failed.

## Conventions
camelCase JSON; errors use `{error:{type,code,param,message}, requestId}`. See `conventions/sapien-conventions.yml` and `errors/sapien-problem-types.yml`.
