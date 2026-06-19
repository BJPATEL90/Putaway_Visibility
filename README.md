# Putaway Visibility

Static GitHub Pages dashboard with a Google Apps Script backend for daily putaway report imports.

## Files

- `web/index.html` - GitHub-hosted dashboard.
- `apps-script/code.gs` - Google Apps Script backend for Gmail import, Google Sheets storage, authorization checks, and email alerts.

## Google Sheet Tabs

The Apps Script creates these tabs automatically:

- `Transactions`
- `AuthorizedUsers`
- `ImportLogs`
- `AlertRecipients`

## Config

Add authorized users in `AuthorizedUsers`:

| Facility Name | Authorized User | Active |
| --- | --- | --- |
| SL Mother Hub | User 1 | YES |
| SL Mother Hub | User 2 | YES |

Add email recipients in `AlertRecipients`:

| Email | Active |
| --- | --- |
| ops@example.com | YES |

## Setup

1. Create a Google Sheet.
2. Open Extensions > Apps Script.
3. Paste `apps-script/code.gs`.
4. Deploy as a Web App.
5. Run `installDailyImportTrigger` once from Apps Script.
6. Deploy the Apps Script Web App with access set so the GitHub page can call it.
7. Confirm `web/index.html` points to the deployed Apps Script URL.
8. Publish the `web` folder through GitHub Pages.

## Logic

- Gmail subject searched: `Export Job Complete - Copy of Putaway All facility`
- CSV link is extracted from `Export File Path:`
- Report date uses yesterday by `Updated Date` in `dd-mm-yyyy`.
- `Putaway Item Id` is the transaction key.
- Existing transaction rows are overwritten when the same `Putaway Item Id` appears again.
- Attention is required only when:
  - `Inventory Type = BAD_INVENTORY`
  - `Status Code = COMPLETE`
  - `Created By` is not configured as authorized for the facility
- Brand is derived from SKU prefix:
  - `MWMM` = ManMatters
  - `MMBW` or `MWBB` = Bodywise
  - `MWLJ` = LittleJoys
