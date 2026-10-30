# AMS PWA migration — technical decision

## Current system

- The production interface is served by Google Apps Script HtmlService.
- The browser frontend calls backend functions through `google.script.run`.
- Backend access is authorised using the signed-in Google account and the `App Users` sheet.
- Google Sheets remains the system of record.
- GitHub Pages currently contains a branded launch page and PWA icon assets; it is not the production application.

## Decision

Do not move the current frontend to GitHub Pages by simply copying the HTML. A GitHub Pages document cannot call `google.script.run`, and the current Apps Script user-identity check must not be weakened.

Do not expose spreadsheet access through an unauthenticated endpoint, trust a client-supplied email address, embed long-lived secrets in browser JavaScript, or use an iframe as an assumed authentication solution.

The safest immediate production arrangement is to keep the complete application hosted by Apps Script, where its existing Google identity and permission checks continue to work. GitHub Pages remains a development and branding asset until a tested authenticated bridge exists.

## Requirements for any future GitHub-hosted frontend

A future migration must provide all of the following before it replaces the current app:

1. A supported Google sign-in flow for the PWA.
2. Server-side verification of Google-issued identity tokens; never trust an email supplied by the browser.
3. An authorisation check against the existing active-user and role records.
4. A server-side bridge to Sheets that keeps spreadsheet identifiers and privileged credentials out of client code.
5. Explicit CORS, token expiry, replay protection, and error-handling tests.
6. Regression tests for every transaction type, stock valuation, chronological-entry rules, formula columns, audit logging, and staff permissions.
7. iPhone Safari and Add to Home Screen testing, including standalone launch and session renewal.

A small authenticated backend service (for example, Cloud Run) may be needed to provide the cross-origin API safely. This is a separate infrastructure change and must be configured and tested before any production cutover.

## Production safety

- Do not edit or redeploy the live Apps Script project as part of this branch-only planning step.
- Do not modify the production spreadsheet.
- Do not change the `App Users` permission model.
- Do not change the existing production deployment URL.
- Keep all implementation and experiments on the `ams-pwa-migration` branch until the owner approves a tested release.

## Current status

The development branch was created successfully. The existing `main` branch and production application remain unchanged. This document records the safe migration boundary; it is not a claim that the PWA/backend bridge has been implemented.
