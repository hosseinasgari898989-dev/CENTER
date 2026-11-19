# CURRENT — وضعیت فعلی پروژه / Current Project Snapshot

## 2026-11-19 — PR #218 merged; manual read-only channel probe awaiting owner run

- **FACT — [rubika-agent PR #218](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/218):** squash-merged after all 7 CI checks succeeded on head `f7e2ecdad83e197b708f0b23e50043defab112a0`. Merge SHA and current `rubika-agent/main`: `fe31aa089e33215790ab658f65f64777ad8553ca`.
- **Backup retained:** `backup/pre-channel-read-probe-2026-11-19`.
- **Owner action:** the manual-only workflow has not been dispatched; the owner must run it once and inspect the JSON comparison for TEST and MAIN.

channel read-only probe merged (workflow 'Rubika channel read-only probe', manual); owner must run it once; then read the JSON (TEST vs MAIN get_chat). MAIN still LOCKED; #215 unreviewed/unmerged.

## 2026-11-19 — PR #217 merged; read-only MAIN preflight result

- **FACT — PR #217:** [Improve read-only MAIN preflight diagnostics](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/217) was squash-merged. Reviewed head SHA: `2fbf8aafbe0da306955a57989c4524b37f8d9410`; merge SHA and current `rubika-agent/main`: `fe31aa089e33215790ab658f65f64777ad8553ca`.
- **FACT — automatic preflight:** the existing push-triggered [Rubika main-channel credential preflight run #44511714512](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/44511714512) started on the merge SHA and completed with failure. No manual workflow dispatch was used.
- **FACT — final JSON line from the run log:** `ok=false`, `failure_stage=main_chat_failed`, `bot_identity_ok=true`, `main_matches_test_pin=false`, `main_destination_accessible=false`, `accessible_via=null`, `chat_exception_type=null`, `chat_response_status=INVALID_INPUT`, `chat_response_description=null`; `read_only_checks` contains only `get_chat` with `ok=false` and `status=INVALID_INPUT`; `read_only_method_presence` reports `get_chat=true` and all other allowlisted methods false. Exact sanitized JSON:
```json
{"ok":false,"failure_stage":"main_chat_failed","bot_identity_ok":true,"main_matches_test_pin":false,"main_destination_accessible":false,"accessible_via":null,"chat_exception_type":null,"chat_response_status":"INVALID_INPUT","chat_response_description":null,"read_only_checks":[{"method":"get_chat","ok":false,"status":"INVALID_INPUT"}],"read_only_method_presence":{"get_admin_chats":false,"get_channel_info":false,"get_chat":true,"get_chat_admins":false,"get_chat_info":false,"get_chat_member":false,"get_chats":false}}
```
- **FACT — separate pull-request checks:** all required PR checks passed on reviewed head `2fbf8aafbe0da306955a57989c4524b37f8d9410`; the actual MAIN verification job was skipped for the PR and only ran after merge via the existing push trigger.
- **Operational lock:** main channel still LOCKED; #215 still unreviewed and unmerged. No cause is inferred for `main_chat_failed`.
- **Backup retained:** `backup/pre-main-channel-preflight-diag-2026-11-19`.
main_chat_failed: get_chat returned INVALID_INPUT with empty description; cause not yet determined; MAIN still LOCKED; #215 unreviewed/unmerged.

**Date:** 2026-11-19  
**Project:** Rubika-Agent  
**Canonical repo:** `hosseinasgari898989-dev/rubika-agent`

## وضعیت / Status

- `rubika-agent/main`: `1beb6003b7e83152ed1dd25dba225f6200c36b4a`
- `CENTER/main` before this docs-only follow-up: `b453b2c1e1565f66fa26c9cf7d522c58d26e5989`
- Runtime policy: TEST-only. MAIN channel remains **LOCKED**; no Go-Live approval was given.

## 2026-11-16 — PR #205/#206 merged؛ Task J preflight BLOCKED

- **FACT — Task H / [PR #205](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/205):** squash-merged. Exact reviewed head `33a4b3ebd3ef1ad70e2ea82373197755ba05189b`; all 3 checks passed on that SHA: Agent [43832466064](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43832466064), Cloud [43832466035](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43832466035), Playwright [43832466055](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43832466055). Merge SHA: `e062043dc159e4bfda1945856d038809bd33fe7f`.
- **FACT — Task I / [PR #206](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/206):** after #205 moved main, backed up the old head and synced the branch by a non-force merge commit `90f33f5362c3699ea971778d8315380efaac090a`. The overlapping `tests/worker.test.mjs` retained both H's owner/viewer access assertions and I's scan-now tests. All required checks passed on the updated head: Agent [43834195558](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43834195558), Cloud [43834195596](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43834195596), Playwright [43834195426](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43834195426); Mob X workflow run [43834195466](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43834195466) completed, with claim/stage, deletion and publish jobs skipped. PR squash-merge SHA and current `rubika-agent/main`: `44224aaa74e3915df1655db5e37bc81ac8397c7a`.
- **FACT — post-merge:** Deploy Worker [43834500279](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43834500279), Pages [43834499628](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43834499628), and deployed Worker health smoke [43834500250](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43834500250) succeeded on `44224aaa`. Read-only GET `/api/health` returned `ok=true`, `database=connected`, `mode=bootstrap`, `publishing=false`.
- **FACT — Termux cron dispatch path:** `src/workflow_cron.js` passes `null` for `termux-docker-discovery.yml`; `src/owner_panel.js:dispatchMobXWorkflow` maps that cron path to `inputs: { dry_run: "false" }`. The task-I tests verify that exact payload. The owner-facing Scan Now path remains explicitly `dry_run="true"`; it was not activated by this task.
- **FACT — first post-deploy Worker-Cron Termux run:** run [43835875253](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43835875253) started `2026-11-16T09:00:14Z` on main SHA `44224aaa`, 14 seconds after the configured 09:00 UTC Worker-Cron tick. GitHub labels it `workflow_dispatch` because the Worker calls the Actions dispatch API; the paired Mob X link-processor dispatch [43835886124](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43835886124) followed at `09:00:19Z`. The Termux job Summary explicitly rendered `Mode: normal metadata intake`, and `Persistent D1 intake: success`. It evaluated 41 candidates: 34 eligible, 7 filtered, 0 newly inserted, and 34 already existing. The deployed cron path is confirmed normal, not dry-run. No dry-run occurred between the final Worker deployment and this first cron-dispatched run.

- **FACT — downstream TEST queue run:** the Termux workflow confirmed `queue_processor_dispatch_http=204` and chained one TEST-only Daily queue processor [43836287929](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43836287929). Its result was `queue_empty`, `sent_count=0`, `failed_count=0`, `queued_remaining=0`; its storage cleanup reported `mode=apply` and `deleted_count=0`.
- **FACT — paired Mob X processor:** [run 43835886124](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43835886124) reported `publishing_attempted=false`, reason `no_owner_approved_mob_x_job`; Mob X media cleanup reported `deleted_count=0` and `d1_writes=0`. No Mob X post or media deletion occurred in that run.

- **FACT — Task J / read-only GO-LIVE preflight:** **BLOCKED**. The owner has not given explicit plain-text Go-Live approval; destination/admin verification and the complete TEST-channel end-to-end/capacity-alert checks are not evidenced by this preflight. Deployment and health checks passed, but they do not satisfy those missing gates. MAIN channel remains **LOCKED**; runtime stays TEST-only. No secret, variable, gate, migration, cron, quiet-hours, D1, Mega, publishing or channel state was changed.
- **Backup / docs branch for Task K:** `backup/pre-task-k-center-update-2026-11-16` / `docs/task-k-current-go-live-2026-11-16`. This entry is documentation only.

## 2026-11-14 — H2 final: PR #189 سپس #190 ادغام شدند

- [PR #189](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/189) با Squash ادغام شد؛ Head بررسی‌شده `ed3c20b6af8d5a8e101f0abe61b8b378edf364c6` و Merge SHA برابر `0f99d411ec92f99236b6d12cadeaab8e64eb8ff0` بود. هر سه Check روی همان Head موفق شدند.
- [PR #190](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/190) پس از sync ایمن با merge commit `cbeda2a370d2b8f6a3b4c99dd26853b96e9aa588` (بدون force-push) بررسی شد؛ هر سه Check روی Head نهایی `cbeda2a370d2b8f6a3b4c99dd26853b96e9aa588` موفق شدند و سپس PR با Squash در `27ccc5adf24e0ab69fefe8fb91eb5b18ae2b11d4` ادغام شد.
- Run [Worker health smoke #43544386078](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43544386078) روی SHA نهایی موفق شد؛ GET `/health` سلامت Worker و اتصال D1 را تأیید کرد.
- [GitHub Pages build/deployment #43544384971](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43544384971) روی همان SHA نهایی موفق شد.
- وضعیت عملیاتی همچنان TEST-only است و MAIN channel **LOCKED** باقی می‌ماند. هیچ Workflow دستی، D1 write، Mega عملیات، migration، secret/variable/gate/cron یا تغییر publishing انجام نشد؛ تست Health نیز AI، دانلود رسانه و Rubika post انجام نداد.

## ادغام‌شده / Merged — requested order

- [#186 — YouTube parking](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/186): reviewed head after main sync `83f1813158a83a3415db6729088e2eb438c81b0d`; merge/main SHA `c313f63e644393bcb084211828f235ff3bf9785e`. All 3 checks green: [Agent](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43522216168), [Cloud](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43522216157), [Playwright](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43522216161). No park workflow dispatch or execution.
- [#187 — unique Mob X media + conservative cleanup](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/187): reviewed head after main sync `fffd58fb0280369c47256af2a0910917cf3bb39c`; merge SHA `200b9869fa93df546ada4b6dfbacff29a7263fa8`. All 4 checks green: [Mob X](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43522820186), [Agent](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43522820257), [Cloud](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43522820294), [Playwright](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43522820256). Uncertain/recoverable references and legacy shared filenames are retained; no Mega deletion was run.
- [#185 — panel speed phase 2](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/185): reviewed head after main sync `f1ce5107d3c98267bb57566b58a719a71ca4b262`; final main/merge SHA `cd26f2c125f03f82c3adf96503f001bfc407e11f`. All 4 checks green: [Cloud](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43523371121), [Agent](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43523371125), [Mob X](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43523371283), [Playwright](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43523371128). D1 click queries `p_report/p_ai/p_status`: `43/37/32 → 14/13/13`.

Previous completed merges: #183 `cca06d974211538583597a7ad8abc0be17737a37`; #184 `dc03d0b3d0cfb981f2b5091076b3f0cd1f8ecc43`.

## پس از merge / Post-merge verification

- [Deploy Cloudflare Worker](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43523625012): **success** on `cd26f2c`.
- [GitHub Pages build/deployment](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43523624510): **success** on `cd26f2c`.
- [Deployed Worker health smoke](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43523625033): **success**.
- [Console Playwright](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43523625024) and [Cloud bootstrap](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43523625029) on final main: **success**.
- Read-only `GET /api/health`: `ok=true`, `service=rubika-agent`, `database=connected`, `mode=bootstrap`, `publishing=false`, `workflow_cron=null`. No health endpoint write/action was performed.
- Later automatic report commit: `github-actions[bot]` advanced main from `cd26f2c` to `5fc297c`, changing only `artifacts/termux_discovery.json`; it did not change Worker source. Pages on this artifact-only head succeeded: [43524150051](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43524150051). The latest Worker deployment remains the successful code-merge deployment above.
- Independent automation observed: Daily Rubika queue processor [run #477](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43524158398), actor `github-actions[bot]`, event `workflow_dispatch`, workflow conclusion **success**. Its queue report was operationally partial: `failed_count=0`, `sent_count=0`, `skipped_count=30`, `queued_remaining=30`. Its automatic storage-cleanup step ran in `apply` mode and reported exactly one deleted path: `/Rubika-Agent/staging/tiktok_7707409580694768948.mp4`. The cleanup report retained explicit protection for item `7694421166508838165`. This independent run was not started or cancelled by this task and is not the YouTube parking workflow.

## قفل‌ها / Locked

- MAIN channel and all routes to it remain **LOCKED** under GO-LIVE.md.
- Protected item `7694421166508838165` and `staging/tiktok-7694421166508838165-square1080x1080.mp4`: untouched; no read/move/delete/retry/resend.
- PRs [#166](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/166), [#17](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/17), and [#23](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/23): untouched.
- No manual workflow dispatch, D1 write, Mega deletion, migration, secret/variable/gate/cron/`quiet_hours` change, or main-channel action was initiated by this task. The YouTube parking workflow remains unrun. An independent bot-triggered Daily queue run is noted below.

## Owner-approved YouTube IDs

Owner approval covers only these exact 30 IDs; it does **not** mean the park operation has been run:

`youtube:agamsFTZh6s`, `youtube:0MD7Nk2WyaI`, `youtube:WrfEe3QY_YU`, `youtube:XttN2HoNXa8`, `youtube:jlEAp1HK5o4`, `youtube:kRH-RoP5stk`, `youtube:DM5MIUwcBGo`, `youtube:GvnZKPIjfSk`, `youtube:WB0lrwUYVt0`, `youtube:flIbd1yobH4`  
`youtube:vgEXCkjClnw`, `youtube:5VcR0MBISiY`, `youtube:DmTbveAWV2k`, `youtube:IQfGNIBExOY`, `youtube:JzbtA0ken-Y`, `youtube:clFsKUSEKmQ`, `youtube:5Wy0Mftm-js`, `youtube:8cR5Z0x4nwg`, `youtube:b-t0t5NSxFo`, `youtube:g6Ss6MN6eBs`  
`youtube:yfEPN8jWLDE`, `youtube:Xiaeg-BSZe8`, `youtube:iSf2GI1Sd-Q`, `youtube:sZ6SvR9OxK0`, `youtube:zSUIhgve6gY`, `youtube:0Ml2dBsP2Xk`, `youtube:EmlbH0Rom8U`, `youtube:IN0Wb7u43mQ`, `youtube:fyzLV_lL2aM`, `youtube:SOjI2Tg8yfA`

## OWNER TO-DOS

1. In Actions → **Park owner-approved YouTube queue rows**, run first with `dry_run=true` and verify exactly 30 matched; then manually run with `dry_run=false` and `confirm=true`. This workflow has not been dispatched or executed by this task.
2. In the TEST bot, press the panel **status**, **report**, and **AI** buttons; compare the displayed numbers and response speed.
3. Manually delete legacy Mega files `official/mob_x_media.jpg` and `official/mob_x_media.mp4` only after #187 is merged and after confirming no Mob X job is running. Neither file was listed or deleted by this task.

**Archive:** historical details stay in `STATE.md`. New chats read this file first.

## 2026-11-14 — PR #191 و #192 ادغام و بررسی پس از انتشار

- [PR #191](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/191) ابتدا با squash ادغام شد: head نهایی پس از sync با main برابر `111139671b6210a8c39e80f2d270984e83a48a13` و merge/main SHA برابر `ab70bbfafa0403ce7cb51c3c730a4b991e710791` بود. همهٔ checkها روی همان head موفق شدند.
- [PR #192](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/192) پس از sync با main شامل #191، با squash ادغام شد: head نهایی `e8da4a07bc6bb0d70a292992fd9fe2177652703c` و merge/main SHA برابر `7a09233a7dbc56f071b20a5cc9615ca67dccbb5f` بود. همهٔ checkها روی همان head موفق شدند.
- [Deploy Cloudflare Worker #43578001533](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43578001533) روی SHA نهایی موفق شد؛ [GitHub Pages build/deploy #43578001189](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/43578001189) نیز موفق شد.
- فقط GET خواندنی `/api/health` انجام شد: `ok=true`, `database=connected`, `mode=bootstrap`, `publishing=false`, `workflow_cron=null` (timestamp پاسخ: `2026-11-14T08:55:44.214Z`).
- TEST-only حفظ شد و MAIN همچنان **LOCKED** است. این کار هیچ D1 write، workflow dispatch، Mega deletion، migration، secret/variable/cron/gate یا تغییر publishing انجام نداد؛ آیتم محافظت‌شدهٔ `7694421166508838165` دست‌نخورده ماند.
