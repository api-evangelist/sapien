---
name: Retrieve consensus outcomes and issue a PoQ Report
description: Poll a datapoint's terminal consensus outcome and issue the signed Proof of Quality Report (attestation/v1) for a Sapien project.
api: openapi/sapien-poq-openapi-original.json
operations: [getProject, getDatapointConsensus]
---

# Retrieve consensus outcomes and issue a PoQ Report

## Auth
`Authorization: Bearer poq_live_...`.

## Steps
1. `getProject` — GET `/projects/{projectId}` to confirm the project state and that validation is progressing.
2. `getDatapointConsensus` — GET `/datapoints/{datapointId}/consensus` to fetch the terminal consensus outcome for a datapoint. Before consensus finalizes this returns `409 consensus_not_finalized` — retry after more validator inputs arrive.
3. Issue the report — GET `/projects/{projectId}/proof-report` to obtain the signed PoQ Report (`attestation/v1`). Until every required validator input is in, this returns `404 proof_report_not_ready`.

## Notes
The PoQ Report is the durable attestation a customer/auditor inspects: what was reviewed, by whom, against what criteria, and with what level of agreement. Only attestation metadata goes onchain — the underlying data stays in your systems. See `conventions/sapien-conventions.yml` and `errors/sapien-problem-types.yml`.
