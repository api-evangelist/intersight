---
name: intersight-firmware-upgrade
title: Run a firmware upgrade with an impact check first
description: Check the impact of a firmware upgrade before running it, then execute it and follow the resulting
  workflow.
api: intersight:server
generated: '2026-08-19'
method: generated
source: openapi/intersight-*-openapi.json + conventions/intersight-conventions.yml + errors/intersight-problem-types.yml
operations:
- GetFirmwareDistributableList
- CreateFirmwareUpgradeImpact
- CreateFirmwareUpgrade
- GetFirmwareUpgradeByMoid
- GetWorkflowWorkflowInfoByMoid
- GetComputePhysicalSummaryList
---

# Run a firmware upgrade with an impact check first

Check the impact of a firmware upgrade before running it, then execute it and follow the resulting workflow.

**Base URL** `https://intersight.com` · **Auth** HTTP Signature (or OAuth2) — see `intersight-api-key-and-signing`.

## Steps

1. Pick the firmware bundle. `GET /api/v1/firmware/Distributables` (`GetFirmwareDistributableList`), filtering on `Version` and `SupportedModels`.
2. Identify the targets. `GET /api/v1/compute/PhysicalSummaries` (`GetComputePhysicalSummaryList`).
3. Ask what the upgrade would do BEFORE doing it. `POST /api/v1/firmware/UpgradeImpacts` (`CreateFirmwareUpgradeImpact`) with the distributable and the target. This is the step that separates a safe agent from an unsafe one: it reports whether the upgrade requires a reboot and which components are affected.
4. Run the upgrade only after the impact is acceptable. `POST /api/v1/firmware/Upgrades` (`CreateFirmwareUpgrade`).
5. Follow it. `GET /api/v1/firmware/Upgrades/{Moid}` (`GetFirmwareUpgradeByMoid`) gives the upgrade resource; it carries a `WorkflowInfo` relationship. Expand or follow that to `GET /api/v1/workflow/WorkflowInfos/{Moid}` (`GetWorkflowWorkflowInfoByMoid`) and read `Status`.
6. If `WaitReason` on the workflow reads `RateLimit`, the orchestration engine has throttled you at account or instance level. Wait — do not resubmit. Resubmission produces a `Duplicate` wait reason, not faster execution.

## Cross-cutting rules

- **Retry safely.** There is no `Idempotency-Key`. Use `If-None-Match: *` on creates and `If-Match: <ModTime>` on updates; a conflict returns **412 Precondition Failed**. See `conventions/intersight-conventions.yml`.
- **Errors.** `{code, messageId, message}` on `application/json`; `code` is a closed enum. Always capture the `x-starship-traceid` response header. See `errors/intersight-problem-types.yml`.
- **Backpressure.** Intersight publishes no rate limit and returns no `429` or `Retry-After` anywhere in the contract. Back off on `ServiceUnavailable` and keep concurrency modest.
- **Never invent an operationId.** Every id used above was verified present in the harvested specification.
