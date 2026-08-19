---
name: intersight-fleet-inventory
title: Inventory the fleet without paging it all down
description: Query the server estate efficiently using OData filtering, selection and server-side aggregation instead
  of walking every page.
api: intersight:server
generated: '2026-08-19'
method: generated
source: openapi/intersight-*-openapi.json + conventions/intersight-conventions.yml + errors/intersight-problem-types.yml
operations:
- GetComputePhysicalSummaryList
- GetComputeBladeList
- GetComputeRackUnitList
- GetEquipmentChassisList
---

# Inventory the fleet without paging it all down

Query the server estate efficiently using OData filtering, selection and server-side aggregation instead of walking every page.

**Base URL** `https://intersight.com` · **Auth** HTTP Signature (or OAuth2) — see `intersight-api-key-and-signing`.

## Steps

1. Start at the union view. `GET /api/v1/compute/PhysicalSummaries` (`GetComputePhysicalSummaryList`) covers blades and rack units together; `GetComputeBladeList` and `GetComputeRackUnitList` are the per-form-factor views.
2. Narrow before you page. `$filter` takes OData predicates — `$filter=Model eq 'UCSX-210C-M7'`, or `$filter=CreateTime gt now() sub PT2H` for recent arrivals.
3. Cut the payload. `$select=Moid,Serial,Model,Name,OperState` returns only those fields. Without it these objects are large.
4. Count before you fetch. `$count=true` returns the count instead of the resources; `$inlinecount=allpages` returns both.
5. Aggregate server-side rather than client-side. `$apply` accepts OData `groupby`/`aggregate` — for example counting servers per model — which avoids pulling the fleet down to count it locally.
6. Page with `$top` and `$skip`. Default page size is 100. This is offset pagination, not cursors, so a very large `$skip` is expensive; prefer a tighter `$filter`.
7. Need a spreadsheet rather than JSON? These list operations also declare `text/csv` and XLSX responses — set `Accept` accordingly instead of converting client-side.

## Cross-cutting rules

- **Retry safely.** There is no `Idempotency-Key`. Use `If-None-Match: *` on creates and `If-Match: <ModTime>` on updates; a conflict returns **412 Precondition Failed**. See `conventions/intersight-conventions.yml`.
- **Errors.** `{code, messageId, message}` on `application/json`; `code` is a closed enum. Always capture the `x-starship-traceid` response header. See `errors/intersight-problem-types.yml`.
- **Backpressure.** Intersight publishes no rate limit and returns no `429` or `Retry-After` anywhere in the contract. Back off on `ServiceUnavailable` and keep concurrency modest.
- **Never invent an operationId.** Every id used above was verified present in the harvested specification.
