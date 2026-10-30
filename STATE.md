# STATE (append new entries at the bottom, newest last)

## 2026-10-30
- rubika-agent main: 001617fa2742bcaab6e98687799bd8e396ce00f9 (after PR #131 squash merge). Deploy Worker run was still in progress at report time; verify result.
- Merged earlier: PR #129 (inactive-only link clear; Mob X exempt from daily cap and quiet hours), PR #130 (subtitle box per line BorderStyle 3, blur off by default).
- PR #131 added: src/console_retry.js (retry failed Mob X jobs, bulk link actions; POST /api/console/jobs/retry and /links/bulk), src/panel_retry_screens.js + src/panel_screens_ext.js (PV multi-select), docs/console/v2-bulk.js (web UI), tests/worker.test.mjs harness updated.
- Retried jobs go back to 'confirmed'; the 5-minute mob-x-link-processor schedule picks them up.
- Decision: quiet hours and daily cap apply only to TikTok edits.

## Task order
1. DONE: bulk select + retry (web console and PV).
2. DONE (owner-reported): the owner personally completed a real X video-link test through to the TEST channel and reported success.
3. NEXT: owner may add more items after the successful test.
4. LAST: main-channel readiness, only with explicit owner approval.

## Open checks
- Main-channel readiness remains locked and is not authorized until the owner gives explicit approval in plain words.

## 2026-10-30 — verification follow-up
- Confirmed rubika-agent main SHA 001617fa2742bcaab6e98687799bd8e396ce00f9. Deploy Cloudflare Worker run #27 (ID 41007298431), event push, completed with conclusion success; the Deploy Worker job and step both succeeded.
- PV menu wiring confirmed in rubika-agent main: owner_panel.js builds its home keyboard from HOME_ROWS_EXT; panel_screens_ext.js inserts RETRY_HOME_ROW before the final help/main-menu rows. The row labels are “🔁 تلاش مجدد ناموفق‌ها” and “☑️ انتخاب لینک‌ها”.
- panel_screens_ext.js routes retry screen IDs to buildRetryScreen; owner_panel.js routes recognized screen callbacks through buildScreen. The retry screen module defines p_fail* retry and p_lsel* inactive-link selection actions.
- This verifies the main-branch code and callback wiring, not a live interactive Rubika PV session at the time of that check.
- Root cause of the stale open checks: STATE was first logged while Deploy Worker run #27 was still in progress. The code review already showed the new PV row wiring, but live Rubika rendering was not independently checked then.
- This follow-up is documentation-only. No rubika-agent code, secrets, variables/gates, migrations, or quiet_hours were changed; no workflow was manually dispatched.

## 2026-10-30 — owner completion update
- Task 1 is DONE: bulk select + retry is complete for the web console and PV (owner-confirmed).
- Task 2 is DONE (owner-reported): the owner ran the real X video-link test through to the TEST channel and it succeeded. This result is recorded from the owner's report, not independent observation by the assistant.
- The CENTER rule now permits merge without CI only for PRs whose changed files are all Markdown and whose content is documentation-only, after full diff review. The rubika-agent CI-green merge rule remains unchanged.
- The main channel remains locked pending explicit owner approval.

## 2026-10-31 — Rubika upload-size boundary
- The observed Rubika upload acceptance boundary is approximately 50 MiB, not a proven universal/theoretical API maximum: run 40469257996 uploaded 49.9995 MiB successfully, sent it to the TEST channel, and confirmed deletion; run 40467474839 failed at 50 MiB and 49.9999 MiB; run 40453281819 failed at 64 MiB. The recorded sanitized failure label was rubika_upload_server_error.
- Current agent/brain/rubika_publisher.py (RubikaPublisher) has no local maximum-size guard and does not split large files; it passes media to the Rubka SDK upload/send methods. The observed upload boundary means videos around/above 50 MiB are rejected by the Rubika upload path, although the exact service-side limit is not established.
- Owner decision: investigate moving the owner console to a Telegram bot, considering Telegram Local Bot API's 2 GiB file limit. This investigation has NOT started; no Telegram migration or implementation is underway.
- Evidence reviewed read-only from rubika-agent main and the 2026-10-27 capacity-probe branch/runs. No files in rubika-agent were changed; this entry records the observed evidence and owner decision only.
