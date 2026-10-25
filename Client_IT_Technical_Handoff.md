# Technical Handoff — Client IT

## Components
SAP S/4HANA CDS/OData or approved ODP provider; integration gateway; transformation workers; Kafka; FinSight loader; reconciliation/audit store; monitoring stack.

## Required confirmations
- Actual CDS views/providers and extraction semantics.
- SAP technical user, roles, RFC/ODP quotas and allowed extraction window.
- Firewall routes and TLS certificates between approved endpoints.
- FinSight API base URL, OAuth scopes, rate limits, idempotency behaviour and schema version.
- India-region storage and backup location.
- Log retention and access-control policy.

## Operational constraints
Do not run heavy extraction 01:00–04:30 IST. ODP delta extraction is limited to once every 30 minutes per provider. Keep RFC concurrency below the agreed cap and preserve network headroom. Schedule around SAP and FinSight maintenance windows.

## Support handover
Provide runbook, dashboard access, escalation owner, DLQ replay approval procedure, incident severity matrix, release/rollback procedure and evidence of test results. Do not store credentials in Git.
