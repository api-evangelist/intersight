---
name: intersight-api-key-and-signing
title: Authenticate to the Intersight API
description: Create an API key and sign requests with the HTTP Signature scheme Intersight declares in its contract.
api: intersight:system
generated: '2026-08-19'
method: generated
source: openapi/intersight-*-openapi.json + conventions/intersight-conventions.yml + errors/intersight-problem-types.yml
operations:
- CreateIamApiKey
- GetIamApiKeyList
- GetIamUserList
- GetIamPermissionList
- GetIamRoleList
---

# Authenticate to the Intersight API

Create an API key and sign requests with the HTTP Signature scheme Intersight declares in its contract.

**Base URL** `https://intersight.com` · **Auth** HTTP Signature (or OAuth2) — see `intersight-api-key-and-signing`.

## Steps

1. Create a key. `POST /api/v1/iam/ApiKeys` (`CreateIamApiKey`). The secret is returned once. Store it in a secret manager; never in the repo.
2. Sign every request. The contract declares `http_signature` (`scheme: signature`, draft-cavage-http-signatures). Sign at minimum `(request-target)`, `(created)`, `Host`, `Date` and `Digest`; Cisco recommends also `(expires)` and `Content-Type`, with `(expires)` about thirty minutes after `(created)`. `Digest` is the RFC 3230 digest of the body. The signing host clock must be NTP-synchronized — skew is a common cause of `AuthenticationFailure`.
3. OAuth2 is the alternative. Register an `iam.AppRegistration`, then use the authorization-code flow (`/iam/app-authorize`, `/iam/token`) with `ROLE.*` scopes, or client credentials with the fine-grained `CREATE.*`/`READ.*`/`UPDATE.*`/`DELETE.*` scopes. The full 3,317-scope list is in `scopes/intersight-scopes.yml`.
4. Check what the credential can actually do before you act. `GET /api/v1/iam/Permissions` (`GetIamPermissionList`) and `GET /api/v1/iam/Roles` (`GetIamRoleList`).
5. A 401 with `messageId: iam_cookie_invalid` means you sent a stale `X-Starship-Token` cookie — that scheme is for browser SSO sessions, not for programmatic clients. Use HTTP Signature or OAuth2.

## Cross-cutting rules

- **Retry safely.** There is no `Idempotency-Key`. Use `If-None-Match: *` on creates and `If-Match: <ModTime>` on updates; a conflict returns **412 Precondition Failed**. See `conventions/intersight-conventions.yml`.
- **Errors.** `{code, messageId, message}` on `application/json`; `code` is a closed enum. Always capture the `x-starship-traceid` response header. See `errors/intersight-problem-types.yml`.
- **Backpressure.** Intersight publishes no rate limit and returns no `429` or `Retry-After` anywhere in the contract. Back off on `ServiceUnavailable` and keep concurrency modest.
- **Never invent an operationId.** Every id used above was verified present in the harvested specification.
