# Custom API Integration Between Client ERP and Financial Analytics Platform

**Project:** Zetheta Project 1B  
**Scenario:** Meridian Manufacturing Ltd. — SAP S/4HANA to FinSight 4.2  
**Status:** Design and simulation package


## Project Overview

Project: Custom API Integration Between Client ERP and Financial Analytics Platform
Scenario: Meridian Manufacturing Ltd. integrates SAP S/4HANA 2023 FPS02 with Zetheta FinSight 4.2.
Objective: reduce the financial-data visibility delay while preserving SAP performance, data integrity, security, auditability and Indian data residency.

This is a design and simulation deliverable. No live SAP or FinSight credentials or production endpoints were provided; API hosts, schemas and sample payloads are illustrative and must be validated with the platform owners before production use.


## Business and Technical Requirements

Business goals:
- Deliver finance data to FinSight within a target 30-minute delta cycle outside the SAP nightly batch window.
- Support seven plants and three SAP company codes.
- Preserve GST components (CGST, SGST, IGST) for invoice-related records.
- Maintain traceability from SAP source document through transformation and load to the analytics record.
- Keep all financial data processing and storage within the India region.

Constraints from the brief:
- SAP batch window RGGBS000: 01:00–04:30 IST; no heavy extraction in this window.
- Integration RFC pool limit: 50 concurrent connections; design target is at most 40 to leave headroom.
- ODP delta extraction frequency: once per 30 minutes per provider.
- Shared MPLS bandwidth: 450 Mbps; integration target no more than 25% during business hours.
- SAP maintenance: second and fourth Saturday 22:00–06:00 IST.
- FinSight maintenance: first Sunday 02:00–06:00 IST.
- Finance data must remain in Indian borders.



## Proposed Architecture

Flow:
SAP S/4HANA CDS/OData or ODP extraction → API gateway / integration service → validation and transformation workers → Kafka topic by domain → FinSight API loader → reconciliation service → monitoring and audit store.

Design choices:
- Use CDS views/OData where supported for governed, externally consumable data; use ODP delta extraction where the approved provider supports delta tokens.
- Schedule each provider no more frequently than every 30 minutes and pause heavy extraction during the SAP nightly batch window.
- Kafka separates extraction from downstream loading and allows controlled retries and replay.
- Domain-specific transformations isolate GL, AP, AR, cost-centre, profit-centre, material ledger, purchase orders, sales orders, fixed assets, bank statements, budget/actual and inventory.
- Use idempotency keys based on domain + company code + source document key + version/delta token.
- Store audit events and reconciliation results in India-region storage.



## Security and Access

- Use TLS 1.2+ for service-to-service traffic and encrypt data at rest with managed keys.
- Use OAuth 2.0 client credentials for FinSight, with short-lived access tokens and secret storage in a managed vault.
- Use SAP technical users with least-privilege authorisations and separate read-only extraction access.
- Do not place credentials, tokens, personal data or financial payloads in logs.
- Apply role-based access to dashboards, logs, replay tools and DLQ operations.
- Retain immutable audit metadata: correlation_id, source system, source key, extraction time, transform version, target response and reconciliation result.



## Data Transformation

Transformation stages:
1. Structural conversion: SAP source structure to canonical JSON.
2. Semantic mapping: company code and account mappings, fiscal period and business meaning.
3. Normalisation: ISO dates, ISO currency codes, decimal precision and standard identifiers.
4. Enrichment: cost-centre/profit-centre labels and approved exchange-rate references.
5. Validation: required fields, allowed ranges, foreign keys, debit/credit balance and GST component checks.
6. Aggregation only where the target contract explicitly requests it; preserve line-level lineage for audit.
7. Output: versioned canonical records with source key, batch ID and correlation ID.

Currency conversion must use an approved rate source and record rate type, rate date, source currency, target currency and applied rate. Do not silently overwrite original amounts. Indian fiscal-year mapping must preserve SAP fiscal year/period and separately derive calendar month; special periods 13–16 require explicit handling.



## Error Handling and Resilience

Error classes:
- TRANSIENT: timeouts, connection resets, temporary 5xx; retry with exponential backoff and jitter.
- RATE_LIMIT: HTTP 429; honour Retry-After and rate-limit headers.
- AUTHENTICATION: 401/403; refresh token once where appropriate, then alert and stop repeated calls.
- DATA_QUALITY: invalid field, missing reference, invalid currency or unbalanced journal; quarantine for correction.
- PERMANENT: unsupported schema, invalid endpoint or rejected business rule; dead-letter after classification.
- SYSTEM: broker or storage outage; pause consumers and preserve offsets.

Suggested retry: delay = min(300 seconds, random(1 second, 2^attempt seconds)); maximum 6 attempts for transient failures. Use circuit breakers with Closed/Open/Half-Open states. Never advance the ODP delta checkpoint until records are durably staged and the batch outcome is recorded. Replays must be idempotent.



## Reconciliation and Data Quality

Reconcile per batch, company code and domain:
- Count reconciliation: extracted = accepted + quarantined + explicitly rejected.
- Financial reconciliation: total debits = total credits per journal/document.
- Amount reconciliation: source and target totals agree within a documented currency precision tolerance.
- Referential integrity: company code, GL account, cost centre, profit centre, vendor/customer and plant references resolve.
- Freshness: successful load timestamp remains within the agreed domain SLA.
- Duplicate control: business key and idempotency key unique at target.

Initial quality targets from the brief: completeness >99.5%, accuracy >99.9%, consistency 100%, timeliness >98% within SLA, validity >99%, uniqueness 100%. Variances are recorded and routed to an exception queue; do not silently discard breaks.



## Monitoring and Alerting

Dashboard panels (12):
1. Pipeline health by domain
2. Throughput (records/second)
3. API latency P50/P95/P99
4. Error rate by class
5. Dead-letter queue depth and age
6. Reconciliation status and variance
7. SAP RFC pool utilisation and response time
8. FinSight API health, quota headroom and token expiry
9. Data freshness by domain
10. CPU and memory utilisation
11. Kafka consumer lag and broker health
12. Batch history, duration and checkpoint progress

Alert examples:
- P1: pipeline red / no successful load for >4 hours; error rate >5%; DLQ >500; data loss suspected.
- P2: reconciliation break; SAP RFC pool >90%; resource >90%; FinSight quota headroom <10%.
- P3: freshness SLA nearing breach; elevated latency; repeated retries.
- P4: informational maintenance, planned pause or recovered incident.
All alerts include correlation/batch IDs, domain, first-seen time, impact, runbook link and owner.



## Testing Strategy

The included test plan covers functional, non-functional, failure-injection, security and reconciliation scenarios. Validate happy-path extraction, multi-company-code routing, fiscal periods, hierarchy flattening, P2P flow, bank statements, budget vs actual, API throttling, SAP outage, broker outage, malformed payloads, network partition, token expiry, unauthorised access and encryption. Production release requires passing critical tests, no unresolved P1/P2 issues, successful reconciliation and an approved rollback plan.



## Deployment and Rollback

Deployment sequence:
1. Confirm CAB approval, backups, maintenance windows and stakeholder notification.
2. Validate secrets, certificates, firewall rules, quotas and India-region resource placement.
3. Deploy to staging and run smoke tests.
4. Run a controlled historical sample load and reconciliation.
5. Enable one low-risk domain with a feature flag; monitor.
6. Increase domains gradually after success criteria are met.
7. Confirm dashboards, alerts, DLQ permissions and support rota.
8. Record release version, configuration hash and sign-off.

Rollback triggers include unexplained financial mismatch, duplicate posting risk, sustained P1 alert, unacceptable SAP performance impact or failed security control. Disable new ingestion, stop consumers safely, preserve offsets and audit data, revert to last known-good version, replay only after idempotency and reconciliation checks. Maximum rollback objective: under 15 minutes, to be validated in rehearsal.



## Assumptions and Limitations

This package is a project-ready design baseline based on the provided assignment brief. The exact FinSight API contract, SAP CDS view names, OAuth scopes, network addresses, secrets, actual data dictionary and sandbox credentials were not supplied. Example endpoints and schemas are therefore illustrative. Replace them with verified sandbox details and run real API linting/integration tests before claiming production readiness. No live ERP or analytics system was accessed from this environment.

