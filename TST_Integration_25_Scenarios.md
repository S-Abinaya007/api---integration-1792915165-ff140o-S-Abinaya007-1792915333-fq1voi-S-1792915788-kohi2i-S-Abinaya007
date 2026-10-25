# D7 — Integration Test Plan

| ID | Category | Scenario | Input | Expected Result | Priority |
|---|---|---|---|---|---|
| T01 | Functional | GL extraction happy path | Valid GL delta batch | All records staged and loaded; counts reconcile | High |
| T02 | Functional | Accounts payable sync | Valid AP invoices and vendors | Invoices mapped; open amounts correct | High |
| T03 | Functional | Accounts receivable sync | Valid AR invoices | Customer references resolve | High |
| T04 | Functional | Multi-company code routing | Records from MC01, MC02, MC03 | Each routed to correct tenant/entity | High |
| T05 | Functional | Fiscal period mapping | Normal and special periods 13–16 | Original period retained; calendar mapping correct | High |
| T06 | Functional | Hierarchy flattening | Nested cost-centre hierarchy | Parent-child lineage preserved without cycles | Medium |
| T07 | Functional | Purchase-to-pay flow | PO, GR and AP invoice sample | PO references and GR/IR controls match | High |
| T08 | Functional | Bank statement normalisation | Statement with debit/credit rows | Dates, signs, currency and IDs normalised | Medium |
| T09 | Functional | Budget versus actual | Budget and actual records | Period/company/account keys align | High |
| T10 | Functional | End-of-day reconciliation | Completed daily batches | Counts and financial totals reconcile | High |
| T11 | Non-functional | Peak load | Simulated 5x volume | No loss; latency and backlog remain within agreed limits | High |
| T12 | Non-functional | Concurrent extraction | Multiple providers due | RFC pool remains below 40 target / 50 limit | High |
| T13 | Non-functional | API latency | Representative load | P95 target measured and reported | Medium |
| T14 | Non-functional | Volume scalability | Large historical batch | Pagination and chunking complete without duplicates | High |
| T15 | Non-functional | 24-hour endurance | Continuous scheduled cycles | No leak or checkpoint drift; alerts operate | Medium |
| T16 | Failure injection | SAP connection failure | Drop SAP connection mid-batch | Retry; no checkpoint advancement before safe commit | High |
| T17 | Failure injection | FinSight API throttling | Return HTTP 429 | Retry-After honoured; no request storm | High |
| T18 | Failure injection | Kafka broker failure | Stop broker/consumer | Offsets preserved; replay succeeds | High |
| T19 | Failure injection | Malformed data | Invalid currency/date/required field | Record quarantined with reason; batch accounted for | High |
| T20 | Failure injection | Network partition | Break SAP-to-integration route | Circuit breaker opens; recovery is controlled | High |
| T21 | Security | Token expiry | Expire access token | Refresh flow works or safely stops and alerts | High |
| T22 | Security | Unauthorised access | Use insufficient scope | 403 response; no data leakage | High |
| T23 | Security | Encryption verification | Inspect transport/storage config | TLS and at-rest encryption controls verified | High |
| T24 | Reconciliation | Debit-credit mismatch | Journal totals deliberately differ | Break detected; no silent success | High |
| T25 | Reconciliation | Duplicate replay | Replay same batch twice | Idempotency prevents duplicate target records | High |


## Exit criteria
- All high-priority tests pass or have formally approved exceptions.
- No unresolved P1/P2 defect.
- Reconciliation passes for the test batch.
- Security and data-residency controls are verified.
- Rollback rehearsal is completed and documented.
