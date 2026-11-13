# CURRENT — وضعیت فعلی پروژه / Current Project Snapshot

**Date:** 2026-11-14  
**Project:** Rubika-Agent  
**Canonical repo:** `hosseinasgari898989-dev/rubika-agent`

## وضعیت / Status

- `rubika-agent/main`: `cd26f2c125f03f82c3adf96503f001bfc407e11f`
- `CENTER/main` at this snapshot: `7cb19a5cf08020682863820e541d514608215f21`
- Runtime policy: TEST-only. MAIN channel remains **LOCKED**; no Go-Live approval was given.

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

## قفل‌ها / Locked

- MAIN channel and all routes to it remain **LOCKED** under GO-LIVE.md.
- Protected item `7694421166508838165` and `staging/tiktok-7694421166508838165-square1080x1080.mp4`: untouched; no read/move/delete/retry/resend.
- PRs [#166](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/166), [#17](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/17), and [#23](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/23): untouched.
- No workflow dispatch, D1 write, Mega deletion, migration, secret/variable/gate/cron/`quiet_hours` change, or main-channel action was performed.

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
