# D6 — Monitoring and Alerting Specification

## Dashboard panels
1. Pipeline health by domain (30s; P1 red if >4h stale or last batch failed)
2. Throughput records/sec (15s; warning below 50% of baseline)
3. API latency P95 (15s; warning >5s)
4. Error rate by class (15s; P1 if >5%)
5. DLQ depth and oldest age (30s; P1 if >500 messages)
6. Reconciliation status and variance (5m; P2 on break)
7. SAP RFC pool and response time (30s; critical >90%)
8. FinSight API latency, quota headroom and token expiry (30s; P2 if headroom <10%)
9. Data freshness per domain (60s; P3 near SLA breach)
10. CPU/memory (15s; P2 if >90%)
11. Kafka consumer lag and broker health (30s; alert on sustained lag)
12. Batch duration and delta checkpoint progress (60s; alert on stalled checkpoint)

## Structured log fields
timestamp, level, service, environment, correlation_id, batch_id, domain, source_key_hash, operation, duration_ms, outcome, error_class, transform_version, target_status. Never log secrets or full financial payloads.

## Alert rules
P1: pipeline unavailable; suspected data loss; error rate >5%; DLQ >500; load stale >4h.
P2: reconciliation break; SAP RFC pool >90%; CPU/memory >90%; quota headroom <10%; sustained consumer lag.
P3: freshness nearing SLA; elevated P95 latency; repeated retry burst; batch duration above baseline.
P4: planned maintenance, recovery notification, non-actionable informational events.
Each alert includes owner, runbook, correlation_id, batch_id, first-seen time, impact and escalation path.
