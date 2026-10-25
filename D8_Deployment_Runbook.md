# D8 — Deployment Runbook and Rollback

## Pre-deployment checklist
- [ ] CAB approval recorded.
- [ ] Change ticket and release owner identified.
- [ ] Architecture/API/mapping versions tagged.
- [ ] Secrets in managed vault; no secrets in repository.
- [ ] TLS certificates valid.
- [ ] Firewall allowlists approved.
- [ ] Resources confirmed in India region.
- [ ] SAP service user and least-privilege roles validated.
- [ ] FinSight OAuth scopes and quota confirmed.
- [ ] Kafka topics, retention and DLQ configured.
- [ ] Backups and configuration snapshots completed.
- [ ] Maintenance windows checked.
- [ ] Alert routes and on-call owners tested.
- [ ] Test data contains no unauthorised production data.
- [ ] Rollback owner and decision authority confirmed.

## Deployment steps
1. Announce change and freeze unrelated changes.
2. Validate configuration, secrets and connectivity in staging.
3. Deploy integration services with feature flags disabled.
4. Run health checks and API contract smoke tests.
5. Run a controlled sample extraction.
6. Validate transformations and reconciliation.
7. Enable one low-risk domain.
8. Monitor throughput, freshness, SAP RFC utilisation, DLQ and reconciliation.
9. Enable remaining domains in controlled groups.
10. Obtain IT and finance sign-off; record release/version.

## Post-deployment checks
- [ ] All services healthy.
- [ ] Scheduled extraction respects 30-minute ODP limit.
- [ ] No heavy extraction during 01:00–04:30 IST batch window.
- [ ] SAP RFC utilisation below agreed threshold.
- [ ] FinSight load accepted and idempotent.
- [ ] Source/target counts reconcile.
- [ ] Debit-credit totals reconcile.
- [ ] GST components preserved.
- [ ] No unexpected DLQ growth.
- [ ] Alerts and dashboards functioning.
- [ ] Audit lineage can trace a sample record.
- [ ] Data remains in India-region resources.

## Rollback
Trigger rollback for suspected data loss, duplicate financial records, unresolved reconciliation break, sustained P1, unacceptable SAP performance impact or security failure. Disable ingestion feature flag; stop consumers safely; preserve offsets and audit evidence; revert to the last known-good release; validate configuration; restart with a controlled sample; replay only after idempotency and reconciliation checks. Target rollback under 15 minutes, subject to rehearsal. Do not delete DLQ or audit evidence.
