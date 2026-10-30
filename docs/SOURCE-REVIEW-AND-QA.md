# AMS Cement Manager — source review and static QA

Review date: 2026-10-30
Source reviewed: owner-provided Apps Script backend and full HTML frontend
Scope: static source review only. No live spreadsheet or deployed Apps Script was accessed.

## Executive result

The supplied frontend and backend both pass JavaScript syntax parsing. The frontend already contains a substantial responsive AMS interface, an embedded app icon, iOS home-screen metadata, transaction selection tiles, modal transaction forms, searchable transaction history, detail views, account summaries, and reduced-motion CSS.

This is not a blank application that needs rebuilding. The correct low-risk path is to preserve and refine this existing Apps Script application.

## Static checks completed

| Check | Result | Notes |
|---|---|---|
| Backend JavaScript syntax | PASS | Node.js syntax check |
| Frontend inline JavaScript syntax | PASS | Extracted inline script and checked with Node.js |
| Responsive layout rules | PRESENT | Desktop, tablet, mobile and narrow-screen breakpoints |
| iOS home-screen metadata | PRESENT | Apple web-app metadata and viewport safe-area |
| Embedded app icon | PRESENT | PNG data URI; no Drive icon dependency in frontend |
| Reduced-motion support | PRESENT | prefers-reduced-motion CSS |
| Transaction types | PRESENT | Backend declares 15 types |
| Permission enforcement | PRESENT | Backend calls requireUser_() in protected operations |
| Formula-column protection | PRESENT | Transaction write updates A:H and J:N, leaving I and O:Y alone |
| Transaction ID concurrency | PRESENT | ScriptLock used around write path |
| Direct-sale account rule | PRESENT | Direct sale is kept as walk-in and excluded from named customer creation |

## Business rules observed in source

- Transaction types: Supplier Order, Goods Received, Supplier Payment, Customer Deposit, Customer Payment, Customer Collection, Direct Sale, Credit Sale, Expense, Owner Capital In, Owner Withdrawal, Opening Stock, Quick Restock, Stock Adjustment In, Stock Adjustment Out.
- Payment methods: Cash, Bank Transfer, POS, Other.
- Backdated transactions are rejected against the latest transaction date to protect weighted-average costing and running balances.
- Stock-consuming transaction types are checked against available stock.
- Supplier references are checked for Goods Received and Supplier Payment.
- Transaction writes preserve formula columns I and O:Y.
- Transaction IDs are generated while holding a script lock.
- Direct Sale is represented as a walk-in sale, not a new named customer account.
- User identity is derived from Session.getActiveUser().getEmail() and checked against the active App Users record.
- History, dashboard, accounts and staff management have server-side permission checks.

## Small frontend hardening item identified

The transaction form currently sets its date input using new Date().toLocaleDateString('en-CA'). An HTML date input requires an exact YYYY-MM-DD value. Locale formatting can vary across browser/OS combinations, so this should be replaced with local date-component formatting rather than relying on locale output:

```javascript
function localDateInputValue(date = new Date()) {
  const year = date.getFullYear();
  const month = String(date.getMonth() + 1).padStart(2, '0');
  const day = String(date.getDate()).padStart(2, '0');
  return year + '-' + month + '-' + day;
}
```

Then replace the existing locale-based date assignment with:

```javascript
$('date').value=localDateInputValue();
```

This is a low-risk date-input robustness improvement. It does not change backend date validation or stored transaction dates.

## Tests that static review cannot honestly claim to have passed

The following require execution inside the owner's Apps Script deployment against a controlled test spreadsheet or an approved test copy:

1. Google account identity availability under the actual web-app deployment settings.
2. Owner and staff sign-in, inactive-user rejection and permission boundaries.
3. All 15 transaction submissions and their exact sheet writes.
4. Weighted-average costing and stock balance calculations after each transaction.
5. Backdated-entry rejection.
6. Insufficient-stock rejection.
7. Formula preservation in columns I and O:Y after writes.
8. ScriptLock behaviour under simultaneous submissions.
9. Customer and supplier account creation and reference matching.
10. Dashboard and history results against actual sheet formulas.
11. iPhone Safari behaviour, Add to Home Screen icon, standalone launch, keyboard/modal viewport and session expiry.
12. Apps Script quotas and execution-time behaviour with real data volume.

## Required live test procedure

- First create a copy of the production spreadsheet and a separate Apps Script test project bound to that copy.
- Populate test-only owner and staff accounts in the test copy.
- Deploy the test web app with the same execution/access configuration intended for production.
- Exercise all transaction types with controlled sample data.
- Compare sheet changes, formula columns, audit log and dashboard outputs before and after each test.
- Test on iPhone Safari and desktop.
- Only after all checks pass, copy the approved source into the production Apps Script project and deploy a new version. Keep the existing deployment/version available for rollback.

## Safety boundary

No live Apps Script source, production spreadsheet, production transaction data, staff list, or deployment configuration was modified during this review. Static syntax checks are not a substitute for live integration tests.