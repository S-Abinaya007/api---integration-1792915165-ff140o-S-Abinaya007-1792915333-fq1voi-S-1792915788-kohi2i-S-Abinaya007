# D4 — Error Handling and Retry Framework

## Classification
| Class | Examples | Action |
|---|---|---|
| TRANSIENT | timeout, reset, HTTP 5xx | exponential backoff with jitter, up to 6 attempts |
| RATE_LIMIT | HTTP 429 | honour Retry-After and quota headers |
| AUTH | 401/403 | refresh token once where valid; otherwise stop and alert |
| DATA_QUALITY | missing mandatory field, invalid currency, unbalanced journal | quarantine, create exception record |
| PERMANENT | unsupported schema/business rule rejection | dead-letter after classification |
| SYSTEM | Kafka/storage outage | pause consumer, preserve offsets, alert |

Retry delay: `min(300 seconds, random(1 second, 2^attempt seconds))`. Add circuit breakers (Closed/Open/Half-Open), bounded retry budgets, request timeouts (5s API target; 30s batch target), and per-domain bulkheads. Never advance a delta checkpoint before durable staging and recorded batch outcome.

## Dead-letter queue
Store message ID, domain, source key, batch ID, correlation ID, error class, sanitized error details, first/last failure time, retry count, transform version and replay status. Access is restricted. Replay requires correction/approval and idempotency validation.
