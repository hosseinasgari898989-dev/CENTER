# STATE (append new entries at the bottom, newest last)

## 2026-10-30
- rubika-agent main: 001617fa2742bcaab6e98687799bd8e396ce00f9 (after PR #131 squash merge). Deploy Worker run was still in progress at report time; verify result.
- Merged earlier: PR #129 (inactive-only link clear; Mob X exempt from daily cap and quiet hours), PR #130 (subtitle box per line BorderStyle 3, blur off by default).
- PR #131 added: src/console_retry.js (retry failed Mob X jobs, bulk link actions; POST /api/console/jobs/retry and /links/bulk), src/panel_retry_screens.js + src/panel_screens_ext.js (PV multi-select), docs/console/v2-bulk.js (web UI), tests/worker.test.mjs harness updated.
- Retried jobs go back to 'confirmed'; the 5-minute mob-x-link-processor schedule picks them up.
- Decision: quiet hours and daily cap apply only to TikTok edits.

## Task order
1. DONE: bulk select + retry (web console and PV).
2. NEXT (needs owner approval): one full real test of an X video link to the TEST channel (new subtitle, caption, speed). Check that retry of an old failed job does not collide with an existing items row.
3. Owner may add more items after task 2.
4. LAST: main-channel readiness, only with explicit owner approval.

## Open checks
- Confirm Deploy Worker finished green on 001617f.
- Verify PV home shows the new retry/select row and the callbacks p_fail*, p_lsel* work.
