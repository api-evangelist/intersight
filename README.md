# Cisco Intersight

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Cisco Intersight is Cisco's SaaS operations platform for UCS servers, HyperFlex clusters, Nexus fabrics, third-party
storage and virtualization, covering provisioning, firmware lifecycle, workload optimization, telemetry and Kubernetes
service delivery.

## Ownership

Part of the Cisco family.

## Contract status

**Published, and harvested in full.** Cisco serves the complete Intersight OpenAPI 3.0.2 contract anonymously:

- **3,963 operations** across **2,448 paths** and **5,612 schemas**, spec version `1.0.11-20260807064027971`.
- **100% of operations** carry a summary and a tag, and **all 3,963 operationIds are unique with zero containing
  whitespace** — worth contrasting with Cisco Webex, where 41.6% of operationIds contain spaces.
- Published as twelve per-service documents plus one combined document. The **eleven** non-SDK service documents
  harvested into `openapi/` together cover **all 2,448 paths with nothing left over**.

### Correcting the previous entry

An earlier pass on 2026-08-19 recorded this provider as a **soft-404 farm with no confirmable contract**. That was
wrong, and the correction is recorded here rather than quietly overwritten.

The earlier pass probed page paths under `intersight.com`, where the `/apidocs` single-page application answers HTTP 200
with an identical 3,784-byte shell. But `intersight.com` returns a **real HTTP 404** on a nonsense control path
(`/zzz-control-nonsense-xyz789`, 1,193 bytes) — so it was never a soft-404 farm, only an SPA. The contract was never on
that host at all: the docs bundle fetches it from the CDN.

```
https://cdn.intersight.com/components/an-apidocs/<build>/model/intersight-openapi-v3-<build>.json
```

That URL returns **HTTP 200, 27.5 MB, `application/json`**, anonymously. The per-service split documents are indexed at
`model/api-ref-services.json` on the same host, and the API changelog is machine-readable at `model/changelog-<year>.json`.

Ownership was checked before anything was saved: `info.title` is "Cisco Intersight", `info.contact` is
`intersight@cisco.com`, and `servers[]` is `https://{server}` with `intersight.com` as the default.

## What Cisco publishes

| Surface | Status |
|---|---|
| OpenAPI 3.0.2 | 3,963 operations, refreshed every release |
| Changelog | Machine-readable per-release OpenAPI diff with a boolean `breaking` flag — 17 releases, 1,584 changes, **1 breaking** |
| OAuth 2.0 scopes | **3,317** scopes — 54 `ROLE.*` for authorization code, fine-grained `CREATE./READ./UPDATE./DELETE.*` for client credentials |
| Authentication | HTTP Signature (draft-cavage), OAuth 2.0, cookie SSO |
| SDKs | Python, PowerShell, Go, Terraform, Ansible — all first-party, all published within three weeks of this profile |
| Status page | `status.intersight.com`, with "Intersight API Services" as its own component |
| Security disclosure | Cisco PSIRT `security.txt` with CSAF machine-readable advisories (parent domain) |
| Pricing | Two tiers, Essentials and Advantage — capabilities published in detail, **no public price** |
| Rate limits | **None published.** No `429` and no `Retry-After` anywhere in 3,963 operations |
| MCP server | **None.** Community servers exist; Cisco ships none for Intersight |
| A2A agent card | **None.** Real 404 on both well-known paths |
| AsyncAPI / webhooks | **None** documented |

## Verified links

- [Portal](https://intersight.com/apidocs/introduction/overview/)
- [API Reference](https://intersight.com/apidocs/apirefs/)
- [Changelog](https://intersight.com/apidocs/introduction/changelog/)
- [Status](https://status.intersight.com/)
- [Getting started](https://developer.cisco.com/learning/tracks/intersight-infra/intersight-rest-api/)
- [Licensing](https://www.cisco.com/site/us/en/products/computing/hybrid-cloud-operations/intersight-platform/licensing.html)
- [GitHub organization](https://github.com/CiscoDevNet)
- [Postman collection](https://github.com/CiscoDevNet/intersight-postman)
- [ParentCompany](https://apis.io/providers/cisco/)

All URLs above returned HTTP 200 when probed on 2026-08-19.
