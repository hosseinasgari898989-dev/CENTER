# CURRENT — وضعیت فعلی پروژه / Current Project Snapshot

**Date:** 2026-11-14  
**Project:** Rubika-Agent  
**Canonical repo:** `hosseinasgari898989-dev/rubika-agent`

## وضعیت فعلی / Status

- `rubika-agent/main`: `0590db4e1497eb3effdd9f329d9a1a370742c60a`
- `CENTER/main`: `131eab5dee1e6691c34340f6b35d4a565b5c8567`
- وضعیت عملیاتی: TEST-only؛ کانال اصلی قفل است. هیچ Go-Live مجاز نشده است.

## ادغام‌شده / Merged

- Rubika-Agent #183: merge SHA `cca06d974211538583597a7ad8abc0be17737a37`.
- Rubika-Agent #184: merge/main SHA در آن مرحله `dc03d0b3d0cfb981f2b5091076b3f0cd1f8ecc43`.
- CENTER #68: merge/current main SHA `131eab5dee1e6691c34340f6b35d4a565b5c8567`.
- PR #183/#184 تکمیل‌شده‌اند؛ دوباره‌کاری نشود.

## PRهای باز / Open PRs

### rubika-agent

- [#185 — panel speed phase 2](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/185), HEAD `3ab4254a603be04bc5eecbd05767fe73c18f854d`: چهار CI سبز؛ شمارندهٔ D1 در سه مسیر `p_report/p_ai/p_status` از `43/37/32` به `14/13/13` رسیده. منتظر بازبینی نهایی مالک؛ merge نشده.
- [#186 — YouTube parking](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/186), HEAD `d46b00e7ca273d7beadffb2dfed6158e10c6f512`: سه CI سبز؛ فقط ۳۰ شناسهٔ تأییدشده و فقط `youtube + queued → skipped`. dry-run پیش‌فرض؛ اجرای واقعی نیازمند `dry_run=false` و `confirm=true`. هیچ dispatch یا اجرای واقعی انجام نشده. منتظر بازبینی و تأیید جداگانه برای اجرا.
- [#187 — unique Mob X media + cleanup](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/187), HEAD `8497515dffb2e930928743d653b05907e23e4289`: PR باز و mergeable؛ CI هنگام ثبت این snapshot در حال اجرا (یک workflow سبز، سه در حال اجرا). حذف فایل فقط پس از `sent + delivery_status=sent + rubika_message_id`؛ وضعیت‌های uncertain/failed/rejected/ready/sending و نام‌های legacy نگه داشته می‌شوند. منتظر CI کامل و بازبینی مالک؛ merge نشده.
- [#166](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/166)، [#17](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/17)، [#23](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/23): قفل؛ دست‌نخورده بمانند.

### CENTER

- [#58 — old STATE-only log](https://github.com/hosseinasgari898989-dev/CENTER/pull/58), HEAD `8d3fa835056c72978b8feaacf1d02ff1c2d94066`: باز و از base قدیمی منشعب؛ mergeable=false. منتظر reconcile/rebase و بازبینی؛ در این task دست‌نخورده می‌ماند.

## قفل‌ها / Locked

- کانال MAIN و هر مسیر انتشار به آن: **LOCKED** تا تأیید صریح و مستقل مالک طبق GO-LIVE.md.
- آیتم `7694421166508838165` و فایل `staging/tiktok-7694421166508838165-square1080x1080.mp4`: protected؛ نه خواندن/انتقال/حذف و نه retry/resend.
- PRهای #166/#17/#23: untouched.
- هیچ migration، D1 write، Mega delete، workflow dispatch، secret/variable، gate، `quiet_hours` یا cron change انجام نشود.

## تأیید مالک / Owner-approved IDs

تأیید داده‌شده فقط برای همین ۳۰ شناسهٔ YouTube است؛ این تأیید به‌معنای اجرای عملیات park نیست:

`youtube:agamsFTZh6s`, `youtube:0MD7Nk2WyaI`, `youtube:WrfEe3QY_YU`, `youtube:XttN2HoNXa8`, `youtube:jlEAp1HK5o4`, `youtube:kRH-RoP5stk`, `youtube:DM5MIUwcBGo`, `youtube:GvnZKPIjfSk`, `youtube:WB0lrwUYVt0`, `youtube:flIbd1yobH4`  
`youtube:vgEXCkjClnw`, `youtube:5VcR0MBISiY`, `youtube:DmTbveAWV2k`, `youtube:IQfGNIBExOY`, `youtube:JzbtA0ken-Y`, `youtube:clFsKUSEKmQ`, `youtube:5Wy0Mftm-js`, `youtube:8cR5Z0x4nwg`, `youtube:b-t0t5NSxFo`, `youtube:g6Ss6MN6eBs`  
`youtube:yfEPN8jWLDE`, `youtube:Xiaeg-BSZe8`, `youtube:iSf2GI1Sd-Q`, `youtube:sZ6SvR9OxK0`, `youtube:zSUIhgve6gY`, `youtube:0Ml2dBsP2Xk`, `youtube:EmlbH0Rom8U`, `youtube:IN0Wb7u43mQ`, `youtube:fyzLV_lL2aM`, `youtube:SOjI2Tg8yfA`

## سه گام بعدی / Next 3 steps

1. CI نهایی #187 را روی HEAD دقیق بررسی و مرز پاک‌سازی را بازبینی کن؛ بدون merge یا Mega operation.
2. #185 و #186 را برای تأیید مالک نگه دار؛ اجرای #186 فقط بعد از دستور مستقل و صریح با هر دو ورودی لازم.
3. Go-LIVE.md را مبنا نگه دار؛ MAIN تا تکمیل preflightها و تأیید نهایی مستقل قفل بماند.

## تصمیم‌های معوق / Pending owner decisions

- مجوز مستقل Go-Live و بازکردن MAIN: **در انتظار**.
- اجازهٔ اجرای واقعی park روی ۳۰ YouTube row با `dry_run=false` و `confirm=true`: **در انتظار**.
- بازبینی و تأیید نهایی PR #187 پیش از هر merge: **در انتظار**.

**Archive:** جزئیات تاریخی فقط در `STATE.md`؛ این فایل خلاصهٔ جاری است.
