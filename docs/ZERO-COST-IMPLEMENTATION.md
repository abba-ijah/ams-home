# AMS — zero-additional-cost implementation plan

## Owner's cost requirement

The owner prefers a free solution and does not want to add a billing account or incur cloud-hosting charges for this project.

## Architecture decision

Keep the complete AMS Cement Manager application hosted by Google Apps Script and keep Google Sheets as its system of record. Do not deploy Cloud Run, Firebase Blaze, a paid database, or another paid backend as part of this plan.

The existing application already uses Apps Script HtmlService and `google.script.run`. Hosting the full application in Apps Script preserves that same-origin execution model and the existing Google-account identity checks in `requireUser_()`.

GitHub Pages may remain a free project/branding repository, but it must not be treated as the application runtime or as a secure proxy to the spreadsheet.

## What this means

- Continue using the existing Apps Script deployment as the actual app.
- Keep the existing spreadsheet and all data.
- Preserve the existing `App Users` authorisation model.
- Improve the existing Apps Script HTML interface in place only after making a backup/version and validating the full source.
- Keep PWA-related metadata and icon assets where supported by Apps Script HTML output; test the iOS Add to Home Screen behaviour on an actual device.
- Do not introduce an external API bridge that requires billing or weaker authentication.
- No Google Cloud billing account is required for this chosen architecture.

## Important limitations

- Apps Script has quotas and execution limits; these differ by account type and may change. Monitor quota errors and execution history.
- A GitHub Pages static page cannot use `google.script.run` directly.
- A fully independent GitHub Pages frontend with a secure cross-origin backend is not included in this zero-additional-cost approach.
- "Free" means no new cloud-hosting/billing service is being introduced. It does not guarantee that every Google account feature, usage level, or future Google policy is free indefinitely.

## Production safeguards

- Never replace the existing production deployment until a tested version is ready.
- Make a backup of the Apps Script project before modifying it.
- Keep the current spreadsheet ID and production data unchanged.
- Do not modify formula columns I and O:Y.
- Preserve ScriptLock transaction ID generation, chronological stock-costing rules, and all existing authorisation checks.
- Preserve the rule that direct sales use Walk-in Sale and do not create named customer accounts.
- Regression-test all 15 transaction types, user roles, audit logging, accounts, history, and dashboard calculations.

## Current Google Cloud project

The owner has created/selected project ID `ams-enterprises-and-services`, but does not want to enable billing. Do not provision billable resources in it. It is not needed for the selected zero-additional-cost architecture.
