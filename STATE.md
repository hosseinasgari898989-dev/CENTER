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
- The CENTER rule now permits merge without CI only for PRs whose changed files are all Markdown and whose content is documentation-only, after full diff review. The `rubika-agent` CI-green merge rule remains unchanged.
- The main channel remains locked pending explicit owner approval.

## 2026-10-31 — Rubika upload-size boundary
- The observed Rubika upload acceptance boundary is approximately 50 MiB, not a proven universal/theoretical API maximum: run 40469257996 uploaded 49.9995 MiB successfully, sent it to the TEST channel, and confirmed deletion; run 40467474839 failed at 50 MiB and 49.9999 MiB; run 40453281819 failed at 64 MiB. The recorded sanitized failure label was rubika_upload_server_error.
- Current agent/brain/rubika_publisher.py (RubikaPublisher) has no local maximum-size guard and does not split large files; it passes media to the Rubka SDK upload/send methods. The observed upload boundary means videos around/above 50 MiB are rejected by the Rubika upload path, although the exact service-side limit is not established.
- Owner decision: investigate moving the owner console to a Telegram bot, considering Telegram Local Bot API's 2 GiB file limit. This investigation has NOT started; no Telegram migration or implementation is underway.
- Evidence reviewed read-only from rubika-agent main and the 2026-10-27 capacity-probe branch/runs. No files in rubika-agent were changed; this entry records the observed evidence and owner decision only.

## 2026-10-31 — failed-media preservation and console decisions
- Owner decision: when a video cannot be sent to Rubika because of size or any other error, preserve the original file in Mega without compression or quality loss. This applies to both TikTok edits and Mob X media.
- Existing Mob X upload behavior: `agent/brain/mob_x_rubika.py` uses `upload_preview_media` and `_send_file_once` for a single attempt; it does not automatically retry. This is a recorded finding, not a change to retry behavior.
- Existing Mega storage: `agent/storage/mega.py` provides `MegaStorage` through MEGAcmd under `/Rubika-Agent`, including upload, download, directory listing, delete, and round-trip verification. It has no public-link export method yet; add export support in a small new module rather than rewriting this large file. Failed originals are to live under a separate `failed/` path so the existing staging cleanup does not remove them. The current cleanup keeps failed/uncertain items untouched and cleans successful staged media after 24 hours.
- Owner-approved web console behavior: an “unsent” list showing caption and video preview, with download and delete actions; support single-item deletion, multi-select deletion, and “delete all” with a two-step confirmation. Deletion is irreversible and removes the Mega file, D1 row, and caption together. Downloading must not trigger deletion; removal happens only through the explicit delete control.
- Video preview must be available in both the owner's PV and the web console. Browser playback of the Mega export still needs a real test; if it does not play smoothly, create a smaller preview-only copy while keeping the original file unchanged.
- Owner authorized one D1 migration only: `0012` for the new `failed_media` table. No other migration is authorized by this decision. The proposed row records source (`tiktok` or `mob_x`), job/item ID, Mega path, export URL, caption, failure reason, size, status, and timestamps; deleting the row also removes the caption.
- Proposed flow (not implemented): on a Rubika send failure, the runner uploads the untouched original to Mega `failed/`, obtains a public export link through a new `mega-export` module, and records the item in D1. Web download uses that public link without requiring Mega credentials. Deletion is requested through the Worker and the existing automatic GitHub dispatch path (`GITHUB_DISPATCH_TOKEN`); the action runs `mega-rm` first and deletes the D1 row only after the file deletion succeeds. No manual dispatch.
- `docs/console/PARITY.md` (2026-10-27) records that the web console has no video preview because no media playback endpoint exists, and that Mob X owner approval is currently PV-only. The migration sequence recorded there ends at `0011_mob_x_link_flow.sql`; `0012` is the next migration number.
- Migration execution on D1 is still unknown. The first implementation-planning step is to inspect `deploy-worker.yml` and establish exactly how migrations are applied; this workflow has not yet been read for this task. No migration has been created or applied.
- GitHub Pages setup is an owner action, not yet confirmed complete: Settings → Pages → Deploy from branch → `main` → `/docs`. Expected console URL: https://hosseinasgari898989-dev.github.io/rubika-agent/console/. Record the actual result only after the owner reports it.
- Telegram-console investigation is now paused. The owner's stated reason is the 50 MB limit of the ordinary Bot API versus up to 2000 MB with the Local Bot API server; grammY is the suggested library. No Telegram implementation or migration has started.
- Implementation status: all three planned PRs remain NOT STARTED — (1) failed-video recording plus migration `0012`, (2) unsent list/download/single-bulk-delete UI, and (3) web video preview. Each is to be a small, separately backed-up PR and must meet the `rubika-agent` CI-green rule. The first next step remains reading `deploy-worker.yml` before deciding how to apply the authorized migration.
