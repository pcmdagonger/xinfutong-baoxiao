# xinfutong-baoxiao

Codex skill for automating Qingdao Xinfutong material invoice reimbursement drafts.

The skill is tuned for direct Chrome/browser-control automation. It includes explicit timeout, retry, run-state, dedicated Chrome tab, zero-screenshot-by-default, and checkpoint-verification rules to avoid taking over the user's current page or falling into slow screenshot-heavy loops during long reimbursement runs.

## Skill

- `skills/xinfutong-baoxiao`

Invoke in Codex as:

```text
$xinfutong-baoxiao
```
