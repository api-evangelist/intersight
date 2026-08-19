---
name: intersight-alarm-triage
title: Triage active alarms
description: Read active alarms across the account, group them, and suppress noise deliberately rather than by ignoring
  it.
api: intersight:system
generated: '2026-08-19'
method: generated
source: openapi/intersight-*-openapi.json + conventions/intersight-conventions.yml + errors/intersight-problem-types.yml
operations:
- GetCondAlarmList
- GetCondAlarmByMoid
- GetCondAlarmAggregationList
- CreateCondAlarmSuppression
- GetCondAlarmDefinitionList
---

# Triage active alarms

Read active alarms across the account, group them, and suppress noise deliberately rather than by ignoring it.

**Base URL** `https://intersight.com` · **Auth** HTTP Signature (or OAuth2) — see `intersight-api-key-and-signing`.

## Steps

1. List what is firing. `GET /api/v1/cond/Alarms` (`GetCondAlarmList`) with `$filter=Severity eq 'Critical'` and `$orderby=CreateTime desc`.
2. Group instead of counting by hand. `GET /api/v1/cond/AlarmAggregations` (`GetCondAlarmAggregationList`) returns pre-aggregated alarm counts.
3. Read one alarm in full. `GET /api/v1/cond/Alarms/{Moid}` (`GetCondAlarmByMoid`); follow `AffectedMo` to the object that raised it.
4. Understand the rule. `GET /api/v1/cond/AlarmDefinitions` (`GetCondAlarmDefinitionList`) explains what the alarm means and its default severity.
5. Suppress with an expiry, never silently. `POST /api/v1/cond/AlarmSuppressions` (`CreateCondAlarmSuppression`) — and note there is a matching dry-run resource (`CreateCondAlarmSuppressionDryRun`) to check what a suppression would hide before you create it. Use it.

## Cross-cutting rules

- **Retry safely.** There is no `Idempotency-Key`. Use `If-None-Match: *` on creates and `If-Match: <ModTime>` on updates; a conflict returns **412 Precondition Failed**. See `conventions/intersight-conventions.yml`.
- **Errors.** `{code, messageId, message}` on `application/json`; `code` is a closed enum. Always capture the `x-starship-traceid` response header. See `errors/intersight-problem-types.yml`.
- **Backpressure.** Intersight publishes no rate limit and returns no `429` or `Retry-After` anywhere in the contract. Back off on `ServiceUnavailable` and keep concurrency modest.
- **Never invent an operationId.** Every id used above was verified present in the harvested specification.
