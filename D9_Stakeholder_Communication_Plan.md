# D9 — Stakeholder Communication Plan

| Audience | Message | Cadence / Trigger | Owner | Channel |
|---|---|---|---|---|
| CFO / Finance | Freshness, reconciliation, financial impact, top risks | Weekly during pilot; immediate on material break | Project lead | Executive summary/dashboard |
| Client IT / SAP Basis | SAP load, maintenance, connectivity, RFC/ODP usage | Before changes; daily during pilot | Integration lead | Change ticket/operations channel |
| Platform Engineering | API contract, schemas, throughput, retries, DLQ | Design review and release checkpoints | API lead | Technical review/repository |
| Internal Audit | Lineage, access controls, reconciliation evidence | Each release and monthly review | Controls owner | Audit report |
| Operations | Cost allocation accuracy, variance and exception status | Daily dashboard | Finance operations | Dashboard/exception queue |

All incidents must include severity, affected domains, first-seen time, data impact, mitigation, owner and next update time. Do not claim production outcomes until measured and approved.
