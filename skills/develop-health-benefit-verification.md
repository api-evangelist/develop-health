---
name: Run a medication benefit verification
description: >-
  Submit a medication benefit verification for a patient, then retrieve or cancel
  it, using the Develop Health Public API. Covers auth, the async submit-then-webhook
  flow, and error handling.
api: openapi/develop-health-openapi.yml
operations:
  - create_benefit_verification_route_benefit_verification_post
  - get_benefit_verification_route_benefit_verification__id__get
  - cancel_benefit_verification_route_benefit_verification__id__cancel_post
generated: '2026-07-18'
method: generated
---

# Run a medication benefit verification

Use this skill to check a patient's medication coverage through Develop Health.

## Authentication
All requests use a Frontegg-issued JWT as an HTTP bearer token:
`Authorization: Bearer <JWT>`. Obtain the token from the Develop Health platform
dashboard. Base URL: `https://api.develophealth.io`.

## Steps
1. **Create the benefit verification.** `POST /benefit-verification`
   (`create_benefit_verification_route_benefit_verification_post`). Provide the
   patient, provider, drug(s), and — recommended for accuracy — `diagnoses` and
   discrete `insurance_content`. For testing, set `mock_result` to receive a
   deterministic mock outcome. The response returns an `id` (prefix `bv_`).
2. **Wait for the result asynchronously.** The verification is processed in the
   background. Prefer webhooks: subscribe to `benefit_verification.status_change`
   (a single terminal event when the BV completes or fails). Verify the
   `X-Webhook-Secret` JWT signature on each delivery.
3. **Or poll.** `GET /benefit-verification/{id}`
   (`get_benefit_verification_route_benefit_verification__id__get`) returns the
   full object including `status`, `status_description`, `drugs`
   (with `determination_details`), and any `error`.
4. **Cancel if needed.** `POST /benefit-verification/{id}/cancel`
   (`cancel_benefit_verification_route_benefit_verification__id__cancel_post`)
   returns `204`.

## Conventions & errors
- Content type `application/json`; standard errors use `{error, message}`.
- `422` validation failures return `{detail: [{loc, msg, type}]}`.
- `401` means a missing/invalid JWT.
- See `conventions/develop-health-conventions.yml` and
  `errors/develop-health-problem-types.yml`.
