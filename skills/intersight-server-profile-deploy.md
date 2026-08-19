---
name: intersight-server-profile-deploy
title: Deploy a server profile from a template
description: Attach a UCS server profile to a physical server from an existing template and deploy it, then poll
  until the deployment settles.
api: intersight:server
generated: '2026-08-19'
method: generated
source: openapi/intersight-*-openapi.json + conventions/intersight-conventions.yml + errors/intersight-problem-types.yml
operations:
- GetServerProfileTemplateList
- GetComputePhysicalSummaryList
- CreateServerProfile
- GetServerProfileByMoid
- UpdateServerProfile
- GetServerProfileList
---

# Deploy a server profile from a template

Attach a UCS server profile to a physical server from an existing template and deploy it, then poll until the deployment settles.

**Base URL** `https://intersight.com` · **Auth** HTTP Signature (or OAuth2) — see `intersight-api-key-and-signing`.

## Steps

1. Find the template. `GET /api/v1/server/ProfileTemplates` (`GetServerProfileTemplateList`) with `$filter=Name eq '<template>'`. Keep the `Moid`.
2. Find the target server. `GET /api/v1/compute/PhysicalSummaries` (`GetComputePhysicalSummaryList`) with `$filter=Serial eq '<serial>'`. Keep its `Moid` and `ObjectType` — you need both, because the profile's `AssignedServer` is a polymorphic relationship that can point at `compute.Blade` or `compute.RackUnit`.
3. Create the profile from the template. `POST /api/v1/server/Profiles` (`CreateServerProfile`) with `SrcTemplate` set to the template relationship, `AssignedServer` set to the server relationship, and `Organization` set to the target organization. Send `If-None-Match: *` so a retry of this call cannot create a second profile — a duplicate is refused with 412 Precondition Failed rather than duplicated.
4. Deploy it. `POST /api/v1/server/Profiles/{Moid}` (`UpdateServerProfile`) with body `{"Action": "Deploy"}`. Deployment is asynchronous; the call returns immediately.
5. Poll to completion. `GET /api/v1/server/Profiles/{Moid}` (`GetServerProfileByMoid`) and watch `ConfigContext.ControlAction`, `ConfigContext.ConfigState` and `ConfigContext.OperState`. Back off between polls — there is no published rate limit and no Retry-After header to read.
6. On any 4xx, read `code` and `messageId` from the error body and capture the `x-starship-traceid` response header before retrying. `UnauthorizedOperation` means the credential is missing a role scope, not that the request was malformed.

## Cross-cutting rules

- **Retry safely.** There is no `Idempotency-Key`. Use `If-None-Match: *` on creates and `If-Match: <ModTime>` on updates; a conflict returns **412 Precondition Failed**. See `conventions/intersight-conventions.yml`.
- **Errors.** `{code, messageId, message}` on `application/json`; `code` is a closed enum. Always capture the `x-starship-traceid` response header. See `errors/intersight-problem-types.yml`.
- **Backpressure.** Intersight publishes no rate limit and returns no `429` or `Retry-After` anywhere in the contract. Back off on `ServiceUnavailable` and keep concurrency modest.
- **Never invent an operationId.** Every id used above was verified present in the harvested specification.
