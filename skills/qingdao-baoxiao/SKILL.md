---
name: qingdao-baoxiao
description: Automate or guide Qingdao Xinfutong/Smart Expense material-invoice corporate reimbursement. Use when the user asks for baoxiao, reimbursement, corporate transfer payment, Xinfutong, Smart Expense, acceptance form creation, public-account reimbursement, or provides an invoice/PDF/reimbursement file path plus a funding card/project. Creates draft-only acceptance and corporate reimbursement/payment documents, extracts invoice line items and bank details, validates totals and payee accounts, and never submits approval unless explicitly authorized.
---

# Qingdao Baoxiao

## Core Rule

Create drafts only. Do not click any submit or approval button unless the user explicitly authorizes submission in the current task.

## Required Inputs

Before starting, obtain:

- Reimbursement file path: invoice PDF or folder containing the invoice/reimbursement files.
- Funding card/project: the exact funding card, project name, or search keyword to select in the system.

Infer all other fields from the invoice and system when possible. Ask the user only when a required value cannot be confirmed.

## Preflight Extraction

Extract and verify these values from the invoice PDF:

- invoice total amount
- invoice date
- seller/payee name
- payee name, bank branch, and bank account from the payment remarks
- each material line item: name, quantity, unit, tax-included unit price, amount, tax rate, and tax amount

Line-item names are fragile:

- Preserve every asterisk in invoice item names.
- Preserve the tax classification prefix, such as `*阀门龙头*氮气减压器`.
- Merge names split only by PDF visual wrapping.
- Do not split one wrapped item into two items.
- Verify line count, item names, and total amount before importing.

## Stage 1: Acceptance Form Draft

Goal: create one `通用验收单` from the invoice and save it as draft only.

1. Enter Xinfutong.
2. Open `智能费控`.
3. Click `新建单据`.
4. Select `通用验收单`.
5. Fill base fields:
   - `单据项目`: select the user-provided funding card/project.
   - `关联单据`: leave empty.
   - `验收类型`: select `项目材料验收单`.
6. Prepare the material detail import from invoice line items.
7. Download the latest import template from the current page.
8. Fill the template with extracted material data.
9. Upload and preview the import file.
10. Import only when the preview has zero errors, for example `错误0项，正确7项`.
11. If the system keeps the original blank first row, delete that blank row.
12. Check every imported line against the PDF.
13. Fill supplemental fields:
   - `合计总金额（元）`: invoice total.
   - `验收结论`: `验收合格，通过`.
   - `是否涉及单价超1000元的非固定资产报销`: determine from line items.
14. Check the system bottom total.
15. Click `存为草稿`.
16. Do not submit.

Before saving Stage 1, verify:

- Document type is `通用验收单`.
- Project matches the user-provided funding card/project.
- `关联单据` is empty.
- `验收类型` is `项目材料验收单`.
- Material lines exactly match the PDF.
- Asterisks and tax classification prefixes are retained.
- Total amount equals the invoice total.
- Bottom system total is correct.
- The over-1000-yuan non-fixed-asset judgment is correct.

## Stage 2: Corporate Reimbursement And Payment Draft

Goal: create one `对公报账` and payment draft from the same invoice, save as draft only.

1. From the Stage 1 page, enter `全部应用`.
2. Click `智能费控` to return to the workbench.
3. Click `新建单据`.
4. In `选择单据类型`, switch to `对公单据`.
5. Click `对公报账`.
6. Keep `关联单据` empty.
7. Set `支出项目` to the user-provided funding card/project.
8. Fill `摘要` using this format:

```text
报 + first material item name with asterisk + 材料费 + invoice total amount
```

Example:

```text
报*阀门龙头*氮气减压器材料费19345.00
```

9. Scroll to `报销明细`; avoid overscrolling to `付款明细`.
10. Click `添加费用`.
11. In `新增费用`, click `上传发票文件`.
12. Upload the invoice PDF.
13. After OCR/recognition, verify seller, amount, and invoice date.
14. Set `费用类型` to `科研事业支出 / 材料费`.
15. Save the expense and return to the main form.
16. Verify reimbursement detail:
   - `费用类别`: `材料费`
   - `费用合计`: invoice total
   - `发票张数`: actual invoice count, usually `1`
   - `发票金额`: invoice total
17. Go to `付款明细` and edit the existing payment detail.
18. Click `收款方`.
19. If a same-name temporary supplier already exists, select it. Do not create a duplicate.
20. Check whether `收款账户` auto-populates.
21. If it auto-populates, compare with PDF remarks:
   - payee name
   - bank branch
   - bank account
22. Save the payment popup.
23. Return to the main form and verify reimbursement amount, invoice amount, payee, bank branch, and bank account.
24. Click `存为草稿`.
25. Treat `保存成功！` as completion.

Before saving Stage 2, verify:

- Document type is `对公报账`.
- `关联单据` is empty.
- `支出项目` matches the user-provided funding card/project.
- `摘要` follows the required format and amount equals the invoice total.
- `费用类型` is `科研事业支出 / 材料费`.
- Invoice amount, reimbursement amount, and expense total match.
- Seller/payee matches the PDF.
- Bank branch and bank account match the PDF remarks.

## Exception Handling

If template import fails with `文件解析失败，请下载模板核对基础信息输入`:

1. Stop retrying the same file.
2. Click `重新导入`.
3. Download the latest template from the current page.
4. Regenerate the Excel import file.
5. Upload and preview again.
6. Import only after zero errors.

If an extra blank row remains after import, delete it and recheck line count and totals.

If a red warning says the storage location must be `青岛研究院`, do not silently override the user-provided location. Try the user location first; if blocked, ask before falling back to the system-required location.

If a click appears to do nothing, re-locate the exact button text in the current business area and retry once. If still unclear, screenshot and pause.

If scrolling loses the target area, identify the current module heading and navigate back to the intended section.

If `全部应用` does not respond, click the top navigation text itself rather than the edge of the region.

If `添加费用` appears unresponsive, check whether the right-side `新增费用` drawer already opened or whether a payment-detail button was clicked by mistake.

If the invoice upload file dialog does not open, try double-clicking `上传发票文件`. When the dialog appears, enter the absolute PDF path in the filename box and click `打开`.

If the payee account does not auto-populate, add or select account information from PDF remarks:

- `银行账户名`: supplier/payee name
- `银行账号`: bank account in PDF remarks
- `开户行名称`: bank branch in PDF remarks

Save the account, then return to payment detail and select it.

## Historical Example

Use this only as a field-format example, never as a default value:

- Funding card/project: `平台人才-PRJ20250420144154-王富强工作室启动经费`
- Invoice total: `19345.00`
- Summary: `报*阀门龙头*氮气减压器材料费19345.00`
- Invoice date: `2026-05-12`
- Seller/payee: `荣成市新拓威实验仪器商行（个体工商户）`
- Supplier type: `临时供应商`
- Bank branch: `中国银行股份有限公司威海开发区支行`
- Bank account: `226054929904`

## Completion Report

When finished, report:

- whether the `通用验收单` draft was saved
- whether the `对公报账` draft was saved
- selected funding card/project
- invoice total
- payee, bank branch, and bank account verification result
- any unresolved issue requiring user action
