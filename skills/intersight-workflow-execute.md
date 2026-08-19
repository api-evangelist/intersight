---
name: intersight-workflow-execute
title: Execute an orchestration workflow and follow it to completion
description: Start an Intersight Orchestrator workflow from its definition, follow the task tree, and interpret
  the wait reasons.
api: intersight:workflows
generated: '2026-08-19'
method: generated
source: openapi/intersight-*-openapi.json + conventions/intersight-conventions.yml + errors/intersight-problem-types.yml
operations:
- GetWorkflowWorkflowDefinitionList
- CreateWorkflowWorkflowInfo
- GetWorkflowWorkflowInfoByMoid
- GetWorkflowWorkflowInfoList
- GetWorkflowTaskInfoList
---

# Execute an orchestration workflow and follow it to completion

Start an Intersight Orchestrator workflow from its definition, follow the task tree, and interpret the wait reasons.

**Base URL** `https://intersight.com` · **Auth** HTTP Signature (or OAuth2) — see `intersight-api-key-and-signing`.

## Steps

1. Find the definition. `GET /api/v1/workflow/WorkflowDefinitions` (`GetWorkflowWorkflowDefinitionList`) with `$filter=Name eq '<workflow>'`. Note its `InputDefinition` — that is the contract for the inputs.
2. Start a run. `POST /api/v1/workflow/WorkflowInfos` (`CreateWorkflowWorkflowInfo`) with `WorkflowDefinition` set to the definition relationship and `Input` matching `InputDefinition`.
3. Follow the run. `GET /api/v1/workflow/WorkflowInfos/{Moid}` (`GetWorkflowWorkflowInfoByMoid`) — read `Status` and `WaitReason`.
4. Read the task tree when it stalls. `GET /api/v1/workflow/TaskInfos` (`GetWorkflowTaskInfoList`) with `$filter=WorkflowInfo.Moid eq '<moid>'`.
5. Interpret `WaitReason` literally: `RateLimit` means account/instance throttling — wait. `Duplicate` means an identical workflow is already running — do not submit again. `WaitTask` means a task has not reported yet.
6. For long-running calls generally, `Prefer: respond-async` (RFC 7240) asks the server to return 202 and complete out of band.

## Cross-cutting rules

- **Retry safely.** There is no `Idempotency-Key`. Use `If-None-Match: *` on creates and `If-Match: <ModTime>` on updates; a conflict returns **412 Precondition Failed**. See `conventions/intersight-conventions.yml`.
- **Errors.** `{code, messageId, message}` on `application/json`; `code` is a closed enum. Always capture the `x-starship-traceid` response header. See `errors/intersight-problem-types.yml`.
- **Backpressure.** Intersight publishes no rate limit and returns no `429` or `Retry-After` anywhere in the contract. Back off on `ServiceUnavailable` and keep concurrency modest.
- **Never invent an operationId.** Every id used above was verified present in the harvested specification.
