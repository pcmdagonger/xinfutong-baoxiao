---
name: qingdao-baoxiao
description: 自动完成或指导薪福通/智能费控中的材料类发票对公转账报销流程。Use when the user asks to automate reimbursement, 对公报销, 对公转账, 通用验收单, 智能费控, 薪福通, or says they will provide a reimbursement file/PDF/invoice location and a funding card/经费卡/支出项目. The workflow creates a 通用验收单草稿 and 对公报账/付款草稿, extracts invoice line items and bank details, fills reimbursement/payment fields, validates amounts and supplier accounts, and stops before submission unless explicitly authorized.
---

# 青岛对公转账报销自动化

## Core Rule

全流程默认只保存草稿。不要点击 `提交`、`提交审批` 或任何最终审批按钮，除非用户在当前任务中明确授权。

## Required Inputs

Before starting, obtain or infer:

- `报销文件位置`: invoice PDF or a folder containing invoice/reimbursement files, preferably an absolute path.
- `经费卡/支出项目`: the funding card, project name, or search keyword to select in the system.

If either is missing, ask the user for it. For all other fields, first extract from the PDF or system, then ask only when uncertain.

## Preflight Extraction

Extract structured data from the invoice PDF before filling forms:

- invoice total amount
- invoice date
- seller/payee name
- payment note fields: payee name, bank branch, bank account
- every material line item: name, quantity, unit, tax-included unit price, amount, tax rate, tax amount

Material names are fragile:

- Preserve every `*` in the invoice item name.
- Preserve the full tax classification prefix, for example `*阀门龙头*氮气减压器`.
- Merge names split by visual PDF line wrapping into one item name.
- Do not split a single item merely because it wraps visually.
- Verify line count, item names, and total amount before import.

## Stage 1: 通用验收单草稿

Goal: create one `通用验收单` from the invoice and save it as draft only.

1. Enter 薪福通.
2. Open `智能费控`.
3. Click `新建单据`.
4. Select `通用验收单`.
5. Fill base fields:
   - `单据项目`: select the user-specified 经费卡/支出项目.
   - `关联单据`: keep empty.
   - `验收类型`: select `项目材料验收单`.
6. Prepare material detail import from the invoice line items.
7. Download the latest import template from the current system page.
8. Fill the template with extracted material data.
9. Upload and preview the import file.
10. Import only if preview has zero errors, such as `错误0项，正确7项`.
11. After import, delete the original blank first row if the system retained one.
12. Check each imported line against the PDF.
13. Fill supplemental fields:
   - `合计总金额（元）`: invoice total.
   - `验收结论`: `验收合格，通过`.
   - `是否涉及单价超1000元的非固定资产报销`: determine from line items.
14. Check the system bottom total amount.
15. Click `存为草稿`.
16. Do not submit.

Before saving Stage 1, verify:

- 单据类型 is `通用验收单`.
- 项目 matches the user-specified 经费卡/支出项目.
- 关联单据 is empty.
- 验收类型 is `项目材料验收单`.
- Materials match the PDF exactly.
- Material `*` prefixes are retained.
- 合计金额 equals invoice total.
- Bottom system total is correct.
- The non-fixed-asset over-1000-yuan judgment is correct.

## Stage 2: 对公报账与付款草稿

Goal: create one `对公报账` and payment draft from the same invoice, save as draft only.

1. From the Stage 1 page, enter `全部应用`.
2. Click `智能费控` to return to the workbench.
3. Click `新建单据`.
4. In `选择单据类型`, switch to `对公单据`.
5. Click `对公报账`.
6. Keep `关联单据` empty.
7. Set `支出项目` to the user-specified 经费卡/支出项目.
8. Fill `摘要` with:

```text
报 + 第一条带*的物品名称 + 材料费 + 发票总金额
```

Example format:

```text
报*阀门龙头*氮气减压器材料费19345.00
```

9. Scroll to `报销明细`; avoid overscrolling to `付款明细`.
10. Click `添加费用`.
11. In `新增费用`, click `上传发票文件`.
12. Upload the invoice PDF.
13. After OCR/recognition, verify seller, amount, and invoice date.
14. Set `费用类型` to:

```text
科研事业支出 / 材料费
```

15. Save the expense and return to the main form.
16. Verify reimbursement detail:
   - 费用类别: `材料费`
   - 费用合计: invoice total
   - 发票张数: actual invoice count, usually `1`
   - 发票金额: invoice total
17. Go to `付款明细` and edit the existing payment detail.
18. Click `收款方`.
19. If an existing same-name temporary supplier exists, select it; do not create a duplicate.
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

- 单据类型 is `对公报账`.
- 关联单据 is empty.
- 支出项目 matches the user-specified 经费卡/支出项目.
- 摘要 follows the rule and amount equals invoice total.
- 费用类型 is `科研事业支出 / 材料费`.
- 发票金额, 报销金额, and 费用合计 match.
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

If the system keeps an extra blank imported row, delete it, then recheck line count and totals.

If a red warning says storage location must be `青岛研究院`, do not silently override the user-specified location. Use the user location first; if blocked, ask before falling back to the system-required location.

If a click appears to do nothing, re-locate the exact button text in the current business area and retry once. If still unclear, screenshot and pause.

If scrolling loses the target area, identify the current module heading, then navigate back to the intended section.

If `全部应用` does not respond, click the top navigation text itself rather than an edge region.

If `添加费用` appears unresponsive, check whether the right-side `新增费用` drawer already opened or whether a payment-detail button was clicked by mistake.

If the invoice upload file dialog does not open, try double-clicking `上传发票文件`. When the dialog appears, enter the absolute PDF path in the filename box and click `打开`.

If the payee account does not auto-populate, add/select account information from PDF remarks:

- 银行账户名: supplier/payee name
- 银行账号: bank account in PDF remarks
- 开户行名称: bank branch in PDF remarks

Save the account, then return to payment detail and select it.

## Historical Example

Use this only as a format example, never as a default value:

- 经费卡/支出项目: `平台人才-PRJ20250420144154-王富强工作室启动经费`
- 发票总金额: `19345.00`
- 摘要: `报*阀门龙头*氮气减压器材料费19345.00`
- 开票日期: `2026-05-12`
- 销售方/收款方: `荣成市新拓威实验仪器商行（个体工商户）`
- 供应商类型: `临时供应商`
- 开户行: `中国银行股份有限公司威海开发区支行`
- 银行账号: `226054929904`

## Completion Report

When finished, report:

- whether the 通用验收单 draft was saved
- whether the 对公报账 draft was saved
- selected 经费卡/支出项目
- invoice total
- payee, bank branch, and bank account verification result
- any unresolved issue requiring user action
