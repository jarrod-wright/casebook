# 000. Example diagnosis: why an order workflow failed overnight

Worked diagnosis (example). Published 2026-11-04.

**This is a worked example, built on a test system. It is not a client's report.** The business, the people, the orders and every figure below are invented to show the shape of the work. The n8n security advisory it cites is real.

Prepared by Jarrod Wright. Diagnosis of one workflow, read-only, 3 business days.

---

## Page 1. Summary for the owner

**What happened.** From 02:10 on Tuesday, paid orders stopped creating invoices in the accounting system. Nobody was told.

47 orders went through the shop with no invoice. On Wednesday morning a customer asked where theirs was.

**Why.** The connection to the accounting system expired at 02:10. The workflow was set to carry on when a step failed, and no alert was attached to it, so it kept running and showed as active while the invoices stopped. When someone re-ran three of the failed orders by hand, three customers were invoiced twice.

**What this means for the business.**
- 47 orders have no invoice, and 3 customers hold a duplicate. Both lists are in the appendix.
- The same gap will hide the next failure, whatever causes it.
- Separately, the n8n version on this server has a published critical security flaw. It was not the cause of this failure, but it needs fixing first.

**What I recommend.** Repair, not rebuild. Fix the two root causes, make reprocessing safe, upgrade n8n, and add an alert that reaches you within 5 minutes of any failure. Options and trade-offs are on page 3.

**What I need from you.** A yes or no on the recommendation, and your sign-off on the acceptance criteria on page 4.

Nothing in production was changed during this diagnosis.

---

## Page 2. Technical appendix

### Scope and method

- **In scope:** one workflow, "Order to invoice" (24 nodes), on a self-hosted n8n instance, version 1.118.2.
- **Access:** a named read-only user created for this review, removed at the end.
- **Evidence:** workflow export, 6 days of execution history, n8n server logs, and a reproduction on a test instance.
- **Method:** IS/IS-NOT analysis (Kepner-Tregoe) to isolate the cause, then a blameless review of contributing factors in the Google SRE style. Security findings are rated on FIRST CVSS bands.

### IS / IS-NOT

| Question | IS | IS NOT | What it tells us |
|---|---|---|---|
| What fails | The "Create invoice" step | The order trigger, the stock update | The fault is at the accounting connection, not the shop |
| When | Every run from 02:10 Tuesday | Runs before 02:10 | Something changed at 02:10 |
| Error seen | HTTP 401 from the accounting API | Timeouts, 5xx errors | Authentication, not availability |
| Visible to staff | No | Yes | The failure path is silent |

### Root causes

**RC-1. The accounting credential expired, and the failing step was set to continue.**
- Evidence: execution 18842 at 02:10:07 Tuesday is the first with `401 Unauthorized` on "Create invoice". Every later run shows the same, and all 47 report success at workflow level, because the node's error setting is "Continue (using error output)" with nothing connected to that output.
- Reproduction: on a test copy, revoke the token and run one order. The run reports success and no invoice is created.

**RC-2. No error workflow is attached, so nothing alerts.**
- Evidence: the workflow settings have no error workflow. The instance has none defined.
- Reproduction: force any node to fail on the test instance. No notification is sent anywhere.

### Contributing factor (not a root cause)

**CF-1. Reprocessing is not idempotent.** The invoice step has no idempotency key, so a manual re-run creates a second invoice. Evidence: executions 19007, 19008 and 19011 (Wednesday, manual) each created an invoice for an order that already had one, after the token was refreshed.

Nobody here did anything unreasonable. The setting that hid the failure is a common default for keeping a workflow moving, and the re-runs were the obvious response to a customer complaint. The gap is in the design, and the design is what gets fixed.

### Security finding

**SF-1. Critical (CVSS 10.0): the instance runs an n8n version affected by CVE-2026-21858.** The advisory covers self-hosted versions from 1.65.0 up to 1.121.0, reached through form-based workflows without authentication.

This instance runs 1.118.2 and has one public form. The flaw is fixed in 1.121.0.

No sign of exploitation was found in the logs reviewed. Under the scope rules a Critical finding stops repair work until it is contained, so the recommended first step is the upgrade.

### Hardening scorecard (baseline)

| Check | Result |
|---|---|
| Error workflow attached | Fail |
| Failing steps stop or route to an alert | Fail |
| Idempotency on every side effect | Fail |
| Bounded retries with a stop | Pass |
| Credentials monitored for expiry | Fail |
| Platform version clear of critical advisories | Fail |
| n8n security audit clean | Not run (read-only review; run after upgrade) |

---

## Page 3. Repair options

| Option | What it involves | Trade-offs |
|---|---|---|
| **A. Repair (recommended)** | Upgrade n8n past the advisory. Route the failing step to an alert. Attach an error workflow. Add an idempotency key on invoice creation. Replay the 47 missing invoices once, safely. | Smallest change, fastest. Keeps the workflow your team knows. |
| B. Refactor | As A, plus split invoice creation into a sub-workflow with its own retries and dead-letter path. | More robust for growth. Two more days and a larger change to verify. |
| C. Rebuild | A new workflow from scratch. | Not justified here. The design is sound apart from the two causes, and a rebuild throws away what already works. |

**Recommendation: A.** It fits the Fix tier: one workflow, two root causes. The upgrade is handled first, as a change order, because of SF-1.

---

## Page 4. Acceptance criteria (written with the client)

The repair is accepted when all five are shown on a test instance and then confirmed on the live system.

1. Every paid order creates exactly one invoice within 5 minutes.
2. Re-running any order never creates a second invoice.
3. Any failed step sends an alert to the operations inbox within 5 minutes.
4. An expired credential is reported before it stops an order.
5. n8n runs a version clear of CVE-2026-21858, and the n8n security audit reports no critical findings.

Each criterion is proven by a test that first reproduced the failure, and each change keeps a rollback.
