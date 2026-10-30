# AMS Business Hub — Google Sheets / Apps Script implementation

This package adds the central AMS Business Hub while preserving the existing Cement Manager records and formulas.

## Files
- `Code.gs`: current Cement Manager backend plus additive Business Hub endpoints, Google ID-token verification, business scoping, and routing.
- `Index.html`: existing Cement Manager frontend with the Google ID token appended to server calls.
- `Hub.html`: central card-based business hub with owner user administration.
- `appsscript.json`: manifest template for the web app deployment.
- `docs/business-hub-plan.md`: architecture and Google Sheets schema.

## Data model
Google Sheets remains the source of truth. The one existing spreadsheet is used. The setup function creates only:
- `AMS Businesses`
- `AMS Access Audit`
and adds two new columns to the existing `App Users` sheet:
- J: Primary Business ID
- K: Business Role

It does not clear, rename, reorder, or rewrite existing cement transaction data or formula columns. It does not call `setupApp()`.

## Required Google OAuth configuration (must be completed before staff sign-in)
1. In Google Cloud Console, select the Google Cloud project associated with the Apps Script project.
2. Configure the OAuth consent screen for the intended users.
3. Create an OAuth 2.0 Client ID of type **Web application**.
4. Add the Google Apps Script hosting origins used by the deployed web app to the authorised JavaScript origins. Google may require both the Apps Script and script.googleusercontent.com origins; use the exact origins shown by the browser.
5. Copy the client ID into `AMS_AUTH.GOOGLE_CLIENT_ID` in `Code.gs`.
6. Ensure the OAuth consent screen allows the intended staff Google accounts.

The server verifies the Google ID token with Google's token-info endpoint and checks audience, issuer, expiry, and verified email before looking up the user in the `App Users` allowlist. Never accept an email or role sent as ordinary client text.

## Installation sequence
1. Back up the current Apps Script project files and spreadsheet.
2. In the existing Apps Script project, add an HTML file named **Hub** and paste `Hub.html`.
3. Replace the project `Code.gs` with this package's `Code.gs`.
4. Replace `Index.html` with this package's updated `Index.html`.
5. Enable **Show appsscript.json manifest file** and update the manifest scopes/settings from this package. The web app runs as the deploying owner; the page is accessible to signed-in Google users, but backend calls require a verified Google ID token and an allowlisted account.
6. Save the project. Run **setupAmsHub() once** from the Apps Script editor as the owner. Review the permission prompt and approve the spreadsheet and external-request scopes.
7. Confirm the new `AMS Businesses` and `AMS Access Audit` tabs exist, and confirm the existing `App Users` values in columns A:I remain intact.
8. Deploy the existing web app as a **new version**. Keep the same deployment URL.
9. Open the deployment URL. Sign in as the owner. The owner should see all four business cards.
10. Add a staff account, assign one active primary business, and test with that Google account. Staff should see only their assigned business. A non-cement user must not be able to call Cement Manager endpoints.
11. Test the cement module using a staff account assigned to Cement. Do not create test financial transactions in the live sheet.

## Access model
- Owner: all business cards and user administration.
- Other users: exactly one primary business.
- A user can be assigned only to a business whose status is `active`.
- Cement module access is checked server-side on every existing Cement Manager operation.
- Planned/on-hold divisions are visible to the owner but cannot be opened until an actual module is implemented and the business is activated.
- No passwords are stored in Sheets. Google sign-in is the identity provider.

## Important limits / review
- The package cannot create the OAuth client ID or deploy into the user's Apps Script account. Those are Google Cloud / Apps Script account actions.
- Do not run `setupApp()`. Do not replace the spreadsheet ID. Do not clear or restructure the existing cement tabs.
- Do not change the production deployment until a backup exists and the new deployment has passed the tests above.
- If the deployment currently has restricted access, update access only as described above after OAuth is configured. Do not use anonymous public access.
