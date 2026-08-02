---
name: Re-authenticate a returning user with Ubble face authentication
description: Authenticate a previously verified applicant biometrically - create a
  face authentication, create an attempt, redirect the user, and read the result.
api: openapi/ubble-identity-verification-openapi-original.yml
operations:
  - create_face_authentication
  - create_attempt_face_authentication
  - retrieve_face_authentication
  - fetch_attempts_face_authentication
  - notify_face_authentication
generated: '2026-07-21'
method: generated
source: derived from the published OpenAPI + https://docs.ubble.ai/docs/face-authentication/
---

# Re-authenticate a returning user by face

Base URL `https://api.ubble.ai`; HTTP Basic + mTLS; `application/json`. The
applicant must already have an approved identity verification to match against.

1. **Create the face authentication** — `create_face_authentication`
   (`POST /v2/face-authentications`) with the `aplt_...` applicant id, your
   `usj_...` configuration id, and webhook URL. Save the `fav_...` id.
2. **Create an attempt** — `create_attempt_face_authentication`
   (`POST /v2/face-authentications/{face_authentication_id}/attempts`); redirect
   the user to the returned hosted capture URL (expires after 15 minutes).
3. **Wait for webhooks** — act on `face_authentication_checks_completed`;
   handle `face_authentication_checks_inconclusive` and
   `face_authentication_capture_aborted` by creating a new attempt
   (see https://docs.ubble.ai/docs/face-authentication/manage-retries).
4. **Read the result** — `retrieve_face_authentication`
   (`GET /v2/face-authentications/{face_authentication_id}`) and
   `fetch_attempts_face_authentication` for attempt detail. Key on `status`;
   decline code 62321 `face_face_mismatch` means the person is not the one who
   performed the identity verification (`errors/ubble-response-codes.yml`).
5. **Optional** — `notify_face_authentication` re-triggers the webhook delivery.
