---
name: Verify a person's identity with Ubble (Checkout.com Identities API)
description: Create an applicant, open an identity verification, create an attempt,
  redirect the applicant to the hosted capture flow, and read the result.
api: openapi/ubble-identity-verification-openapi-original.yml
operations:
  - create_applicant
  - create_identity_verification
  - create_attempt
  - retrieve_identity_verification
  - fetch_attempts
  - pdf_identity_verification
  - notify_identity_verification
generated: '2026-07-21'
method: generated
source: derived from the published OpenAPI + https://docs.ubble.ai/docs/identity-verification/
---

# Verify a person's identity

Base URL `https://api.ubble.ai`. Authenticate with HTTP Basic credentials from
https://dashboard.ubble.ai/ and include your mTLS certificate on every call.
All requests and responses are `application/json`.

1. **Create the applicant** — `create_applicant` (`POST /v2/applicants`).
   Optionally set `external_applicant_id` to your own ID. Save the returned
   `aplt_...` id. (In test accounts, magic `external_applicant_id` values from
   `sandbox/ubble-sandbox.yml` select a simulated outcome.)
2. **Create the verification** — `create_identity_verification`
   (`POST /v2/identity-verifications`) with `applicant_id` and your `usj_...`
   configuration id (from your account manager) plus your webhook URL. Save the
   `idv_...` id. (`create_and_start_identity_verification` at
   `POST /v2/create-and-start-idv` combines steps 2-3.)
3. **Create an attempt** — `create_attempt`
   (`POST /v2/identity-verifications/{identity_verification_id}/attempts`). The
   response carries the hosted verification URL; redirect the applicant to it
   (iframe/webview/redirect options in `components/ubble-components.yml`). The
   URL expires after 15 minutes by default.
4. **Wait for webhooks** — deliveries are CloudEvents signed with the
   `Cko-Signature` header (ECDSA/SHA-512; verify per
   `asyncapi/ubble-webhooks.yml`). Act on
   `identity_verification_checks_completed`; retriable outcomes surface as
   `identity_verification_checks_inconclusive` or `..._capture_aborted`.
5. **Read the result** — `retrieve_identity_verification`
   (`GET /v2/identity-verifications/{identity_verification_id}`) and
   `fetch_attempts` for per-attempt detail. Key business rules on `status`
   (approved / declined / retry_required / inconclusive); treat `response_codes`
   (registry in `errors/ubble-response-codes.yml`) as supporting detail only —
   new codes are added in a backward-compatible way.
6. **Optional** — `pdf_identity_verification` downloads the PDF report;
   `notify_identity_verification` re-triggers the webhook; on 400/422 read the
   `error_type` + `error_codes` envelope (`errors/ubble-problem-types.yml`).
