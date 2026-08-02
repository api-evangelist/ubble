---
name: Screen an applicant for AML risk with Ubble
description: Create an applicant, run an AML verification (screening + ongoing
  monitoring), and consume the review outcomes via webhooks.
api: openapi/ubble-identity-verification-openapi-original.yml
operations:
  - create_applicant
  - create_aml_verification
  - retrieve_aml_verification
generated: '2026-07-21'
method: generated
source: derived from the published OpenAPI + https://docs.ubble.ai/docs/aml-screening/
---

# Screen an applicant for AML risk

Base URL `https://api.ubble.ai`; HTTP Basic + mTLS; `application/json`.

1. **Create the applicant** — `create_applicant` (`POST /v2/applicants`) with the
   person's details (name, birth date). Save the `aplt_...` id.
2. **Create the AML verification** — `create_aml_verification`
   (`POST /v2/aml-verifications`) with `applicant_id` and your webhook URL.
   Save the `amlv_...` id. Initial risk scoring is configured per
   https://docs.ubble.ai/docs/aml-screening/set-up-an-initial-risk-scoring.
3. **Consume webhook events** (CloudEvents, `Cko-Signature`-verified):
   `aml_verification_onboarding_started` → `aml_verification_onboarding_completed`
   for the initial screening; `aml_verification_onboarding_reviewed` after manual
   review; `aml_verification_monitoring_alert` /
   `aml_verification_monitoring_reviewed` / `aml_verification_status_changed`
   for ongoing monitoring.
4. **Read the result** — `retrieve_aml_verification`
   (`GET /v2/aml-verifications/{aml_verification_id}`) for the current status and
   matches; results can be investigated manually in the dashboard per
   https://docs.ubble.ai/docs/aml-screening/manually-investigate-the-results.
