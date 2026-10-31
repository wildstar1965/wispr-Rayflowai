# RayFlow AI - Security Overview

RayFlow is a voice-first editor built with Wispr Flow. This document describes
how voice input, AI provider requests, user media, and production deployment
are kept inside the application's security boundaries.

## Secrets and credentials

- **Provider keys are backend-only.** Keys for configured AI providers are
  submitted to the authenticated backend, encrypted per workspace, and never
  returned to the browser.
- **Wispr Flow is a development workflow.** Voice prompts used to build the
  project are not application secrets. Runtime credentials still belong in the
  backend environment or Google Secret Manager, never in frontend code.
- **No secrets in images or git.** Dockerfiles copy source only. `.gitignore`
  excludes `.env`, production environment files, local storage, and SQLite.
- **Production secrets use Secret Manager.** Cloud Run services receive only
  the secrets they need through least-privilege service-account bindings.
- **No secret logging.** Provider errors are redacted and keys are never echoed
  in logs, validation responses, URLs, or generated bundles.

## Voice and privacy

- Browser speech input is submitted only when the user stops recording or sends
  the final transcript. Interim words are shown locally as listening feedback.
- Microphone permission, missing devices, unsupported speech recognition, and
  provider failures produce visible recovery paths. The editor remains usable
  with typed commands and direct controls.
- Uploaded video, images, and audio remain in RayFlow storage for editing and
  rendering. They are not sent to Wispr Flow as part of the development story.
- Workspace records, media, captions, and provider settings are scoped to the
  authenticated workspace.

## Input validation and safe operations

- **AI output is validated before use.** Director plans are checked against a
  fixed registry of renderer-supported actions and bounded parameters.
- **Approval is preserved.** Voice and AI requests create a proposal or a
  clearly reported command result; destructive or plan-level changes do not
  silently apply without the editor's approval flow.
- **Requests are bounded.** Pydantic schemas enforce required fields, limits,
  supported media types, and strict action parameters.
- **No shell execution from model text.** Assistant output is data interpreted
  by the action registry, never an executable command.
- **Paths are constrained.** Media access rejects absolute paths, traversal,
  drive letters, and invalid workspace locations.
- **Uploads are limited.** Request and media-size limits, MIME validation, and
  media probing protect the backend from malformed input.

## Transport and authorization

- Production traffic uses HTTPS and same-origin frontend proxying by default.
- CORS is empty by default in production and must be explicitly allow-listed
  for cross-origin use.
- Every workspace operation is ownership-scoped and requires an authenticated
  session.
- The backend, rather than the browser, populates trusted editor context before
  sending a plan to a provider. Client-supplied context cannot spoof another
  workspace's project.
- Provider retries are bounded, and render-job triggers use the service's
  identity instead of long-lived credentials embedded in the application.

## Containers and storage

- Backend and render-worker images run as an unprivileged user. The frontend is
  served by nginx on an unprivileged port.
- Cloud Storage uses uniform access and public-access prevention. Downloads are
  served through the backend or short-lived signed URLs.
- Health checks, graceful shutdown, and persisted render status protect work
  across restarts.
- Security headers are set by the backend and frontend proxy.

## Responsible disclosure

If you find a vulnerability, do not open a public issue with exploit details.
Contact the maintainers privately with reproduction steps so it can be addressed
before disclosure.
