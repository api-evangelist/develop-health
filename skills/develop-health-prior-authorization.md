---
name: Submit and track a prior authorization
description: >-
  Create a prior authorization request, track its status via webhooks, update the
  preferred pharmacy, submit feedback, and cancel transfers or the request itself,
  using the Develop Health Public API.
api: openapi/develop-health-openapi.yml
operations:
  - create_prior_auth_route_prior_authorization_post
  - get_prior_auth_submission_by_id_prior_authorization__id__get
  - update_ndp_submission_by_id_prior_authorization__id__ndp_submission_patch
  - submit_prior_auth_feedback_prior_authorization__id__feedback_post
  - cancel_prescription_transfer_by_id_prior_authorization__id__prescription_transfer_cancel_post
  - cancel_prior_auth_submission_by_id_prior_authorization__id__cancel_post
generated: '2026-07-18'
method: generated
---

# Submit and track a prior authorization

Use this skill to file and manage a prior authorization (PA) with Develop Health.

## Authentication
Frontegg JWT bearer token: `Authorization: Bearer <JWT>`.
Base URL: `https://api.develophealth.io`.

## Steps
1. **Create the PA.** `POST /prior-authorization`
   (`create_prior_auth_route_prior_authorization_post`). Supply patient,
   provider, insurance, prescription, diagnoses, and optional `priority`. For
   testing set `mock_result`. Returns an `id` (prefix `pa_req_`). A `409` means
   the prescription is already linked to an existing PA.
2. **Track status.** Subscribe to `prior_authorization.status_change` and the
   related events (`activity_log_updated`, `message`,
   `prescription_transfer_change`, `real_time_benefits_check_change`). Or poll
   `GET /prior-authorization/{id}`
   (`get_prior_auth_submission_by_id_prior_authorization__id__get`), which
   returns `status`, `url`, `activity_log` (downloadable faxes), and
   `message_thread`.
3. **Update preferred pharmacy.** `PATCH /prior-authorization/{id}/ndp-submission`
   (`update_ndp_submission_by_id_prior_authorization__id__ndp_submission_patch`) —
   allowed only while the request is active and no transfer attempts have been
   made (`400` on terminal requests).
4. **Submit feedback.** `POST /prior-authorization/{id}/feedback`
   (`submit_prior_auth_feedback_prior_authorization__id__feedback_post`) returns `201`.
5. **Cancel a prescription transfer.**
   `POST /prior-authorization/{id}/prescription-transfer/cancel`
   (`cancel_prescription_transfer_by_id_prior_authorization__id__prescription_transfer_cancel_post`).
6. **Cancel the PA.** `POST /prior-authorization/{id}/cancel`
   (`cancel_prior_auth_submission_by_id_prior_authorization__id__cancel_post`) —
   `400` if the PA is already completed, failed, or cancelled.

## Conventions & errors
- `422` validation errors use `{detail: [{loc, msg, type}]}`; `404` when the PA
  id does not exist. Verify the `X-Webhook-Secret` JWT on webhook deliveries.
- See `conventions/develop-health-conventions.yml`,
  `errors/develop-health-problem-types.yml`, and
  `asyncapi/develop-health-webhooks.yml`.
