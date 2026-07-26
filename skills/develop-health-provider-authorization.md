---
name: Authorize and look up providers
description: >-
  Initiate provider authorization, list authorized providers, and look up a
  provider using the Develop Health Public API.
api: openapi/develop-health-openapi.yml
operations:
  - authorize_provider_provider_authorize_post
  - get_authorized_providers_authorized_providers_get
  - lookup_provider_provider_lookup_post
generated: '2026-07-18'
method: generated
---

# Authorize and look up providers

Use this skill to manage the prescriber/provider authorization surface that
gates benefit verification and prior authorization submissions.

## Authentication
Frontegg JWT bearer token: `Authorization: Bearer <JWT>`.
Base URL: `https://api.develophealth.io`.

## Steps
1. **Initiate provider authorization.** `POST /provider/authorize`
   (`authorize_provider_provider_authorize_post`). A `400` indicates provider
   authorization was already initiated.
2. **List authorized providers.** `GET /authorized-providers`
   (`get_authorized_providers_authorized_providers_get`) returns the collection
   of providers authorized for your account.
3. **Look up a provider.** `POST /provider/lookup`
   (`lookup_provider_provider_lookup_post`) to resolve a provider's details
   before submitting BV/PA requests.

## Conventions & errors
- `422` validation errors use `{detail: [{loc, msg, type}]}`; `401` for a
  missing/invalid JWT.
- See `conventions/develop-health-conventions.yml` and
  `errors/develop-health-problem-types.yml`.
