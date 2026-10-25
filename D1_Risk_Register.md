# D1 — Risk Register

| ID | Risk | Probability | Impact | Mitigation |
|---|---|---|---|---|
| R01 | SAP extraction affects production performance | Medium | High | Enforce provider schedule, concurrency cap, monitoring and batch blackout |
| R02 | API contract differs from placeholder specification | High | High | Validate with FinSight owner and sandbox before build |
| R03 | Duplicate records after retry/replay | Medium | High | Idempotency key and duplicate detection |
| R04 | ODP checkpoint advances prematurely | Low | Critical | Advance only after durable staging and recorded outcome |
| R05 | Currency conversion inconsistency | Medium | High | Approved rate source; preserve original amount and rate metadata |
| R06 | Fiscal period mapping error | Medium | High | Test fiscal variant and special periods 13–16 |
| R07 | GST components lost during transformation | Low | High | Explicit CGST/SGST/IGST mapping and reconciliation |
| R08 | FinSight rate limiting causes backlog | Medium | Medium | Honour Retry-After, quota monitoring and bounded concurrency |
| R09 | Secrets exposed in repository/logs | Low | Critical | Secret vault, scanning, redaction and least privilege |
| R10 | Data processed outside India | Low | Critical | Region policy, deployment guardrails and audit checks |
| R11 | DLQ grows without action | Medium | High | Age/depth alerts, owner and replay runbook |
| R12 | Reconciliation breaks not escalated | Medium | High | Automated alerts, exception ownership and release gates |
