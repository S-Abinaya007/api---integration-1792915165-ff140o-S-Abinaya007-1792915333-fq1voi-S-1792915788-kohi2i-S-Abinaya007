# D5 — Reconciliation and Data Quality

## Reconciliation dimensions
- Completeness: extracted = accepted + quarantined + explicitly rejected.
- Accuracy: compare source and target values after transformation; financial totals by domain/company code.
- Timeliness: successful loads meet the agreed domain SLA.
- Consistency: all references resolve and cross-domain keys agree.

## Financial controls
- Total debits equal total credits per journal/document.
- Compare source and target totals by company code, currency, period and domain.
- Preserve original amount/currency alongside any converted amount and rate metadata.
- Preserve CGST, SGST and IGST components on invoice records.
- Keep lineage from SAP document/line key through target record.

## Data quality targets from brief
Completeness >99.5%; accuracy >99.9%; consistency 100%; timeliness >98%; validity >99%; uniqueness 100%.

## Minimum rule set
1. Required source key present.
2. Company code maps to entity.
3. Posting date parses.
4. Fiscal year is present.
5. Fiscal period is in supported range.
6. GL account resolves.
7. Currency is valid ISO 4217.
8. Amount is numeric.
9. Debit/credit indicator is valid.
10. Journal debits equal credits.
11. Vendor ID resolves for AP.
12. Customer ID resolves for AR.
13. Due date is not earlier than invoice date unless approved exception.
14. GST components are non-negative.
15. GST component sum aligns with tax amount within tolerance.
16. Cost centre resolves.
17. Profit centre resolves.
18. Parent hierarchy has no cycle.
19. Plant code is configured.
20. Purchase order item key is unique.
21. Material ID resolves.
22. Bank statement ID is unique.
23. Fixed asset depreciation method maps.
24. Target idempotency key is unique.
25. Delta checkpoint does not move on failed batch.
26. Source/target record counts reconcile.
27. No duplicate source document/line key.
28. Currency conversion has rate/date/source metadata.
29. Target response includes accepted/rejected status.
30. Audit event exists for every batch.
