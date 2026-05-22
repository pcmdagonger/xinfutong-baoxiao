---
name: qingdao-baoxiao
description: Automate or guide Qingdao Xinfutong/Smart Expense material-invoice corporate reimbursement using direct Chrome/browser-control automation where available. Use when the user asks for baoxiao, reimbursement, corporate transfer payment, Xinfutong, Smart Expense, acceptance form creation, public-account reimbursement, or provides an invoice/PDF/reimbursement file path plus a funding card/project. Creates draft-only acceptance and corporate reimbursement/payment documents, extracts invoice line items and bank details, validates totals and payee accounts, avoids step-by-step screenshot spam, keeps the conversation compact during long runs, and never submits approval unless explicitly authorized. This skill is ASCII-only to avoid mojibake; Chinese UI labels are stored as Unicode escapes in the label map.
---

# Qingdao Baoxiao

## Encoding Rule

This file is intentionally ASCII-only. Chinese UI labels are written as JSON-style Unicode escapes in the label map below. Decode the escapes to the visible Chinese text before matching or typing in Xinfutong.

Example: `\u667a\u80fd\u8d39\u63a7` means the Smart Expense app label.

## UI Label Map

Use these labels exactly after decoding the Unicode escapes:

```text
SMART_EXPENSE = "\u667a\u80fd\u8d39\u63a7"
NEW_DOCUMENT = "\u65b0\u5efa\u5355\u636e"
GENERAL_ACCEPTANCE_FORM = "\u901a\u7528\u9a8c\u6536\u5355"
DOCUMENT_PROJECT = "\u5355\u636e\u9879\u76ee"
LINKED_DOCUMENT = "\u5173\u8054\u5355\u636e"
ACCEPTANCE_TYPE = "\u9a8c\u6536\u7c7b\u578b"
PROJECT_MATERIAL_ACCEPTANCE = "\u9879\u76ee\u6750\u6599\u9a8c\u6536\u5355"
TOTAL_AMOUNT_YUAN = "\u5408\u8ba1\u603b\u91d1\u989d\uff08\u5143\uff09"
ACCEPTANCE_RESULT = "\u9a8c\u6536\u7ed3\u8bba"
ACCEPTED_PASS = "\u9a8c\u6536\u5408\u683c\uff0c\u901a\u8fc7"
NON_FIXED_ASSET_OVER_1000 = "\u662f\u5426\u6d89\u53ca\u5355\u4ef7\u8d851000\u5143\u7684\u975e\u56fa\u5b9a\u8d44\u4ea7\u62a5\u9500"
SAVE_DRAFT = "\u5b58\u4e3a\u8349\u7a3f"
SUBMIT = "\u63d0\u4ea4"
ALL_APPS = "\u5168\u90e8\u5e94\u7528"
DOCUMENT_TYPE_SELECT = "\u9009\u62e9\u5355\u636e\u7c7b\u578b"
CORPORATE_DOCUMENTS = "\u5bf9\u516c\u5355\u636e"
CORPORATE_REIMBURSEMENT = "\u5bf9\u516c\u62a5\u8d26"
EXPENSE_PROJECT = "\u652f\u51fa\u9879\u76ee"
SUMMARY = "\u6458\u8981"
REIMBURSEMENT_DETAILS = "\u62a5\u9500\u660e\u7ec6"
PAYMENT_DETAILS = "\u4ed8\u6b3e\u660e\u7ec6"
ADD_EXPENSE = "\u6dfb\u52a0\u8d39\u7528"
NEW_EXPENSE = "\u65b0\u589e\u8d39\u7528"
UPLOAD_INVOICE_FILE = "\u4e0a\u4f20\u53d1\u7968\u6587\u4ef6"
EXPENSE_TYPE = "\u8d39\u7528\u7c7b\u578b"
RESEARCH_MATERIAL_FEE = "\u79d1\u7814\u4e8b\u4e1a\u652f\u51fa / \u6750\u6599\u8d39"
EXPENSE_CATEGORY = "\u8d39\u7528\u7c7b\u522b"
MATERIAL_FEE = "\u6750\u6599\u8d39"
EXPENSE_TOTAL = "\u8d39\u7528\u5408\u8ba1"
INVOICE_COUNT = "\u53d1\u7968\u5f20\u6570"
INVOICE_AMOUNT = "\u53d1\u7968\u91d1\u989d"
EDIT = "\u7f16\u8f91"
PAYEE = "\u6536\u6b3e\u65b9"
PAYEE_ACCOUNT = "\u6536\u6b3e\u8d26\u6237"
SAVE_SUCCESS = "\u4fdd\u5b58\u6210\u529f\uff01"
REIMPORT = "\u91cd\u65b0\u5bfc\u5165"
OPEN = "\u6253\u5f00"
BANK_ACCOUNT_NAME = "\u94f6\u884c\u8d26\u6237\u540d"
BANK_ACCOUNT = "\u94f6\u884c\u8d26\u53f7"
BANK_BRANCH_NAME = "\u5f00\u6237\u884c\u540d\u79f0"
IMPORT_PARSE_ERROR = "\u6587\u4ef6\u89e3\u6790\u5931\u8d25\uff0c\u8bf7\u4e0b\u8f7d\u6a21\u677f\u6838\u5bf9\u57fa\u7840\u4fe1\u606f\u8f93\u5165"
ZERO_ERROR_PREVIEW_EXAMPLE = "\u9519\u8bef0\u9879\uff0c\u6b63\u786e7\u9879"
QINGDAO_RESEARCH_INSTITUTE = "\u9752\u5c9b\u7814\u7a76\u9662"
REPORT_PREFIX = "\u62a5"
```

## Core Rule

Create drafts only. Do not click SUBMIT or any approval button unless the user explicitly authorizes submission in the current task.

## Browser Automation Mode

Prefer direct Chrome/browser-control automation over screenshot-driven browser operation.

Operational priorities:

1. Use the Codex Chrome/browser plugin direct-control surface when available.
2. Prefer DOM/Playwright-style locators, visible DOM, role/text/label selectors, and URL/title/toast checks.
3. Use screenshots only for:
   - initial orientation when DOM is insufficient,
   - visual-only errors,
   - ambiguous page state,
   - final evidence if the user explicitly asks for screenshots.
4. Do not take or emit a screenshot after every click or field entry.
5. After each action, perform the cheapest useful check:
   - unique locator count,
   - current URL/title,
   - visible toast text,
   - modal/drawer presence,
   - specific field value or row count.
6. Batch low-risk actions into short browser-control code cells. Keep each batch scoped to one section or one modal, then return only a compact status.
7. Reuse the same browser tab/session. Do not reopen or reload pages unless needed.
8. Avoid broad body-text dumps and repeated full DOM snapshots. Use one fresh DOM snapshot after navigation or major UI changes, then reuse it until stale.
9. Before clicking or filling, verify the target is unique when uniqueness is not obvious.
10. If a locator fails, take a fresh DOM snapshot and change strategy once. If still unclear, pause with a short status and, only then, take one screenshot.

If both an old screenshot-heavy browser tool and a direct Chrome/browser-control tool are available, choose direct Chrome/browser-control. Use coordinate/vision interaction only as a fallback for controls that cannot be reached through DOM or Playwright.

## Long-Run Context Control

This workflow can run long. Keep the conversation short and preserve state outside the chat.

1. Maintain a local run-state file in the current workspace, named `qingdao-baoxiao-run-state.md` or `qingdao-baoxiao-run-state.json`.
2. Store only compact facts in the run-state file:
   - current stage and step,
   - invoice path,
   - funding card/project,
   - invoice total and date,
   - seller/payee,
   - bank branch/account,
   - material line count and total,
   - created draft status,
   - unresolved blockers.
3. Update the run-state file after major milestones, not after every click.
4. In chat, report only milestone summaries:
   - Stage 1 started,
   - material import ready,
   - Stage 1 draft saved,
   - Stage 2 started,
   - payment verified,
   - Stage 2 draft saved,
   - blocker requiring user action.
5. Do not paste long DOM snapshots, OCR dumps, table rows, or browser logs into chat. Put necessary details in the run-state file and summarize.
6. If the thread becomes long or unstable, resume from the run-state file instead of re-reading the full conversation.
7. If interrupted, first read the run-state file, inspect the current browser tab cheaply, and continue from the latest confirmed milestone.
8. Keep final output brief and include only completion status, selected project, invoice total, payee/bank verification, and blockers.

## Required Inputs

Before starting, obtain:

- Reimbursement file path: invoice PDF or folder containing invoice/reimbursement files.
- Funding card/project: exact funding card, project name, or search keyword to select in the system.

Infer all other fields from the invoice and system when possible. Ask the user only when a required value cannot be confirmed.

## Preflight Extraction

Extract and verify these values from the invoice PDF:

- invoice total amount
- invoice date
- seller/payee name
- payee name, bank branch, and bank account from payment remarks
- each material line item: name, quantity, unit, tax-included unit price, amount, tax rate, and tax amount

Line-item names are fragile:

- Preserve every asterisk in invoice item names.
- Preserve the tax classification prefix, for example an item may start with `*...*`.
- Merge names split only by PDF visual wrapping.
- Do not split one wrapped item into two items.
- Verify line count, item names, and total amount before importing.

## Stage 1: Acceptance Form Draft

Goal: create one GENERAL_ACCEPTANCE_FORM from the invoice and save it as draft only.

1. Enter Xinfutong.
2. Open SMART_EXPENSE.
3. Click NEW_DOCUMENT.
4. Select GENERAL_ACCEPTANCE_FORM.
5. Fill base fields:
   - DOCUMENT_PROJECT: select the user-provided funding card/project.
   - LINKED_DOCUMENT: leave empty.
   - ACCEPTANCE_TYPE: select PROJECT_MATERIAL_ACCEPTANCE.
6. Prepare the material detail import from invoice line items.
7. Download the latest import template from the current page.
8. Fill the template with extracted material data.
9. Upload and preview the import file.
10. Import only when the preview has zero errors, such as ZERO_ERROR_PREVIEW_EXAMPLE.
11. If the system keeps the original blank first row, delete that blank row.
12. Check every imported line against the PDF.
13. Fill supplemental fields:
   - TOTAL_AMOUNT_YUAN: invoice total.
   - ACCEPTANCE_RESULT: ACCEPTED_PASS.
   - NON_FIXED_ASSET_OVER_1000: determine from line items.
14. Check the system bottom total.
15. Click SAVE_DRAFT.
16. Do not submit.

Before saving Stage 1, verify:

- Document type is GENERAL_ACCEPTANCE_FORM.
- Project matches the user-provided funding card/project.
- LINKED_DOCUMENT is empty.
- ACCEPTANCE_TYPE is PROJECT_MATERIAL_ACCEPTANCE.
- Material lines exactly match the PDF.
- Asterisks and tax classification prefixes are retained.
- Total amount equals invoice total.
- Bottom system total is correct.
- The over-1000-yuan non-fixed-asset judgment is correct.

## Stage 2: Corporate Reimbursement And Payment Draft

Goal: create one CORPORATE_REIMBURSEMENT and payment draft from the same invoice, save as draft only.

1. From the Stage 1 page, enter ALL_APPS.
2. Click SMART_EXPENSE to return to the workbench.
3. Click NEW_DOCUMENT.
4. In DOCUMENT_TYPE_SELECT, switch to CORPORATE_DOCUMENTS.
5. Click CORPORATE_REIMBURSEMENT.
6. Keep LINKED_DOCUMENT empty.
7. Set EXPENSE_PROJECT to the user-provided funding card/project.
8. Fill SUMMARY using this decoded format:

```text
REPORT_PREFIX + first material item name with asterisk + MATERIAL_FEE + invoice total amount
```

9. Scroll to REIMBURSEMENT_DETAILS; avoid overscrolling to PAYMENT_DETAILS.
10. Click ADD_EXPENSE.
11. In NEW_EXPENSE, click UPLOAD_INVOICE_FILE.
12. Upload the invoice PDF.
13. After OCR/recognition, verify seller, amount, and invoice date.
14. Set EXPENSE_TYPE to RESEARCH_MATERIAL_FEE.
15. Save the expense and return to the main form.
16. Verify reimbursement detail:
   - EXPENSE_CATEGORY: MATERIAL_FEE
   - EXPENSE_TOTAL: invoice total
   - INVOICE_COUNT: actual invoice count, usually 1
   - INVOICE_AMOUNT: invoice total
17. Go to PAYMENT_DETAILS and edit the existing payment detail using EDIT.
18. Click PAYEE.
19. If a same-name temporary supplier already exists, select it. Do not create a duplicate.
20. Check whether PAYEE_ACCOUNT auto-populates.
21. If it auto-populates, compare with PDF remarks:
   - payee name
   - bank branch
   - bank account
22. Save the payment popup.
23. Return to the main form and verify reimbursement amount, invoice amount, payee, bank branch, and bank account.
24. Click SAVE_DRAFT.
25. Treat SAVE_SUCCESS as completion.

Before saving Stage 2, verify:

- Document type is CORPORATE_REIMBURSEMENT.
- LINKED_DOCUMENT is empty.
- EXPENSE_PROJECT matches the user-provided funding card/project.
- SUMMARY follows the required format and amount equals invoice total.
- EXPENSE_TYPE is RESEARCH_MATERIAL_FEE.
- Invoice amount, reimbursement amount, and expense total match.
- Seller/payee matches the PDF.
- Bank branch and bank account match the PDF remarks.

## Exception Handling

If template import fails with IMPORT_PARSE_ERROR:

1. Stop retrying the same file.
2. Click REIMPORT.
3. Download the latest template from the current page.
4. Regenerate the Excel import file.
5. Upload and preview again.
6. Import only after zero errors.

If an extra blank row remains after import, delete it and recheck line count and totals.

If a red warning says the storage location must be QINGDAO_RESEARCH_INSTITUTE, do not silently override the user-provided location. Try the user location first; if blocked, ask before falling back to the system-required location.

If a click appears to do nothing, re-locate the exact decoded button text in the current business area and retry once. If still unclear, screenshot and pause.

If scrolling loses the target area, identify the current module heading and navigate back to the intended section.

If ALL_APPS does not respond, click the top navigation text itself rather than the edge of the region.

If ADD_EXPENSE appears unresponsive, check whether the right-side NEW_EXPENSE drawer already opened or whether a payment-detail button was clicked by mistake.

If the invoice upload file dialog does not open, try double-clicking UPLOAD_INVOICE_FILE. When the dialog appears, enter the absolute PDF path in the filename box and click OPEN.

If the payee account does not auto-populate, add or select account information from PDF remarks:

- BANK_ACCOUNT_NAME: supplier/payee name
- BANK_ACCOUNT: bank account in PDF remarks
- BANK_BRANCH_NAME: bank branch in PDF remarks

Save the account, then return to payment detail and select it.

## Anonymized Field-Format Example

Use this only as a format example, never as a default value. Do not store real project names, real suppliers, real bank accounts, or real invoice amounts in this skill file.

- Funding card/project: `PROJECT-CODE + funding-card-name`
- Invoice total: `1234.56`
- Summary shape: decoded REPORT_PREFIX + first asterisk-prefixed material name + decoded MATERIAL_FEE + `1234.56`
- Invoice date: `2099-01-01`
- Supplier type: temporary supplier, if that is what the system shows.
- Bank account: masked digits from PDF payment remarks, for example `****1234`.

## Completion Report

When finished, report:

- whether the GENERAL_ACCEPTANCE_FORM draft was saved
- whether the CORPORATE_REIMBURSEMENT draft was saved
- selected funding card/project
- invoice total
- payee, bank branch, and bank account verification result
- any unresolved issue requiring user action
