# AMS Business Hub — Implementation Plan

## Product direction
AMS Enterprises and Services is the parent organisation. The home page is a central business hub made primarily of business cards, not a long-form corporate website.

## Business portfolio
| Business key | Display name | Status |
|---|---|---|
| cement | Cement Business | active |
| pos | POS & Agency Banking | planned |
| gravel | Gravel Business | planned |
| broiler | Broiler Farming | on_hold |
| future | Future Business Ventures | expandable |

## Access model
- Owner / Super Admin: sees and administers every registered business.
- Every other user has exactly one primary business assignment.
- Non-owner users see only their assigned business card.
- Business-level permissions further restrict actions inside that module.
- The server must verify identity, account status, primary business, and permissions on every protected operation. Hiding cards in the browser is not security.

## Google Sheets data store
Google Sheets remains the data store. Apps Script remains the trusted server-side layer. The current Cement Manager spreadsheet and working transaction data must be preserved.

Recommended central administration tabs (additive; do not rename or overwrite existing cement tabs):
1. **AMS Businesses** — business_id, business_name, status, description, module_key, display_order, created_at, updated_at.
2. **AMS Users** — user_id, full_name, login_email, role_key, primary_business_id, account_status, created_at, updated_at.
3. **AMS Roles** — role_key, role_name, scope, description.
4. **AMS Permissions** — role_key, business_id, permission_key, allowed.
5. **AMS Audit Log** — timestamp, actor_user_id, action, business_id, record_type, record_id, outcome.

Use stable IDs, not row numbers, as references. Validate all writes server-side. Do not store plaintext passwords in Sheets; use Google identity/session authentication where appropriate, or a properly designed password-authentication service. Never trust a client-supplied email or role.

## Initial roles
- owner
- business_manager
- business_staff

Permissions should be granular, e.g. dashboard.view, transactions.view, transactions.create, transactions.edit, reports.view, users.manage, settings.manage. Staff permissions are assigned only within their primary business.

## User experience
1. Sign-in.
2. Server resolves the authenticated user and permissions.
3. Business Hub displays the owner portfolio or the user's single assigned business card.
4. Selecting a card opens that business module.
5. Server re-checks access before returning data or accepting a write.

## Integration and safety
- Keep the existing Cement Manager deployment and its spreadsheet data operational.
- Do not run setupApp() as part of this work.
- Do not alter existing cement tabs, formulas, transaction records, or the current deployment while building the hub.
- The current Apps Script web app is the first business module. Integrate it only after a server-side access check is designed and tested.
- Future POS, gravel, and poultry modules should use the same identity and permission model while retaining appropriate business-specific data structures.

## Delivery sequence
1. Build the visual Business Hub shell as a separate preview.
2. Add and validate the central Google Sheets administration schema.
3. Implement Apps Script authentication, user resolution, and server-side authorisation.
4. Connect the cement module without changing its existing business records.
5. Test owner, manager, staff, unauthorised, disabled-user, and direct-URL access cases.
6. Add future business modules when operational.
