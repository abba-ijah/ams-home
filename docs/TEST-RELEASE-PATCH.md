# AMS free test-release patch

This is a small, reviewable frontend patch prepared from the owner's supplied Apps Script source. It is not deployed to production.

## Changes

1. Add an explicit Apple touch icon link pointing to the existing public AMS icon asset at `https://abba-ijah.github.io/ams-home/apple-touch-icon.png`.
2. Replace locale-dependent date-input formatting with a local YYYY-MM-DD formatter. HTML date inputs require this exact format.
3. Leave Code.gs and all backend business logic unchanged.

## Validation

- Patched frontend inline JavaScript passes Node.js syntax checking.
- Backend Code.gs passes Node.js syntax checking when checked as JavaScript source.
- Confirmed the locale-dependent date assignment is removed and the Apple touch icon link is present.

## Deployment boundary

The patch is packaged for a separate test Apps Script project and a copy of the spreadsheet. It has not been applied to the live Apps Script project. The owner must create the test copy and authorise the test deployment before live integration tests can be run.

Downloadable package prepared in this conversation: AMS_Cement_Manager_Free_Test_Release.zip.