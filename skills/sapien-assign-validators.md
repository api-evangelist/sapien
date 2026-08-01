---
name: Invite and assign validators to a project
description: Invite validators by email, assign them to a Sapien PoQ project, and grant validator classes declared in the project spec.
api: openapi/sapien-poq-openapi-original.json
operations: [listValidators, createProjectInvite, assignValidator, listProjectClasses, grantValidatorClass, unassignValidator]
---

# Invite and assign validators to a project

## Auth
`Authorization: Bearer poq_live_...`; caller must be an org originator.

## Steps
1. `listValidators` — GET `/validators` (supports `?email=` lookup) to find existing org validators.
2. `createProjectInvite` — POST `/projects/{projectId}/invites` to invite a new person by email. This is an idempotent upsert keyed by `(lower(email), project_id)`: a pending invite is returned unchanged, a cancelled one is reactivated, an already-accepted one returns with status.
3. `assignValidator` — POST `/validators/{validatorId}/assign` with the `projects[]` to assign (each may optionally grant a `classId`). Use `autoEnroll: true` to promote an existing org member to validator. Unknown validator → `404 validator_not_found`.
4. `listProjectClasses` — GET `/projects/{projectId}/classes` to see the validator tiers declared in the poq.toml spec.
5. `grantValidatorClass` — POST `/projects/{projectId}/validators/{validatorId}/classes` with `classId` and an `evidenceNote` justifying qualification.
6. `unassignValidator` — POST `/validators/{validatorId}/unassign` to revoke access (idempotent; class grants are revoked separately).

## Conventions
See `conventions/sapien-conventions.yml`; revoke/unassign operations are idempotent.
