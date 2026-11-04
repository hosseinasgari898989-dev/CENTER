# گزارش کامل TikTok — ITEM 16

**تاریخ بررسی:** 2026-11-04  
**دامنه:** فقط‌خواندنی؛ وضعیت مخزن زنده، workflowها، گزارش‌های GitHub Actions و SELECT از D1.  
**نسخهٔ بررسی‌شدهٔ rubika-agent/main:** `c6e5035c343135b97f5ee7766d2cf8b0e3757dbe`  
**نسخهٔ مبنای CENTER/main هنگام شروع:** `1b6b806be5fdaba09553e89d8c5e869140edf668`

برچسب‌ها: **FACT** یعنی شاهد مستقیم؛ **GUESS** یعنی برداشت محتمل؛ **UNKNOWN** یعنی دادهٔ کافی نداریم. «رد نهایی» یعنی همان queue item با وضعیت failed/skipped/uncertain تمام شده؛ نه اینکه حتماً هیچ تلاش داخلی محدود انجام نشده باشد.

---

## 1) تغییر سیاست در ۲۳ اکتبر ۲۰۲۶

مراجع اصلی: [PR #41](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/41)، [PR #42](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/42)، merge commit [97578088](https://github.com/hosseinasgari898989-dev/rubika-agent/commit/97578088cebcdc58fa6cf520e07fb38c84410b8c) و commit پیگیری [e3fc45c](https://github.com/hosseinasgari898989-dev/rubika-agent/commit/e3fc45cf190f7b592e9e545379e61395c13592b7).

| قاعده | پیش از ۲۳ اکتبر | تغییر/وضعیت پس از ۲۳ اکتبر و امروز | دلیل ثبت‌شده |
|---|---|---|---|
| کشف منبع | **FACT:** شش حساب منتخب + فیدهای هشتگ عمومی/منابع ناشناس؛ سقف ۶۰ مورد، شامل ۴۸ مورد حساب‌های شناخته‌شده و ۱۲ مورد ناشناس؛ حداکثر ۸ از هر حساب و فیلتر سن ۹۰ روز. | **FACT:** فقط شش حساب منتخب؛ حداکثر ۸ از هر حساب، یعنی حداکثر ۴۸ نتیجهٔ عملی در اجرای فعلی. مسیر هشتگ عمومی/ناشناس حذف شد. config هنوز مقدار قدیمی tiktok_max_results=60 دارد، اما collector مقدار 48 می‌گیرد. | **FACT:** متن PR #41: حذف مسیر unknown-source؛ حفظ فقط شش creator feed منتخب مالک. |
| کلیدواژه | **FACT:** تطبیق چندگانهٔ نام بازی/شخصیت‌ها، با منطق relevance و edit marker. | **FACT:** تطبیق case-insensitive زیررشته‌ای در caption یا hashtags؛ فقط یکی از poppy یا playtime کافی است. این یک شرط OR است؛ حضور نام سازنده به‌تنهایی کافی نیست. | **FACT:** PR #41 می‌گوید دقیقاً caption/hashtag substring rule با poppy OR playtime. |
| edit marker | **FACT:** برای شش حساب، سیاست قدیمی در عمل relevance یا edit marker را می‌پذیرفت؛ flag جداگانهٔ require_edit_signal_check وجود داشت. | **FACT:** شرط edit/AEP/montage جداگانه حذف شده است. | **FACT:** PR #41 صریحاً می‌گوید «remove the separate edit-marker requirement». |
| متن منفی/توهین‌آمیز | **FACT:** فیلتر negative text در مسیر قدیمی اجرا می‌شد. | **FACT:** PR #41 ابتدا منطق TikTok را سبک کرد؛ PR #42 روشن کرد که negative-text gate روی TikTok اجرا نشود؛ برای non-TikTok باقی ماند. | **FACT:** متن PR #42: «Do not reject selected TikTok videos using the extra negative-text gate». |
| audio stream | **FACT:** بررسی پایهٔ stream صوتی وجود داشت. | **FACT:** حفظ شده؛ برای عبور نهایی TikTok باید validation.has_audio دقیقاً true باشد. no-audio رد می‌شود. | **FACT:** PR #41 صریحاً audio/media checks را حفظ کرد؛ #167 نیز audio را حفظ کرد. |
| music / thresholds | **FACT:** بررسی موسیقی لازم بود؛ تصمیم محافظه‌کارانه با آستانه‌های 0.70 و 0.15 در Worker اجرا می‌شد. | **FACT:** قاعده تغییر نکرده است: p≥0.70 یعنی music=true؛ p≤0.15 یعنی false؛ 0.15<p<0.70 یعنی null/ambiguous و رد fail-closed. مقدار گم‌شده/نامعتبر هم قبول نمی‌شود. | **FACT:** PR #41 music check را حفظ کرد؛ PR #167/#168 نیز صریحاً گفتند thresholds و ambiguity تغییر نمی‌کنند. |
| فیلد بصری is_poppy_playtime | **FACT:** فیلد در قرارداد قدیمی وجود داشت و از metadata/قواعد Python ساخته می‌شد؛ شاهدی از classifier تصویری متصل در آن مسیر نیست. | **FACT:** بازنویسی policy در #41 دیگر این فیلد را برای TikTok تولید نمی‌کرد، اما config همچنان require_poppy_relevance_check=true داشت و guard نهایی آن را مطالبه می‌کرد. **FACT:** PR #167 فقط همین شرط مانده را از gate نهایی TikTok حذف کرد؛ برای مسیرهای غیر-TikTok دست نزد. | **UNKNOWN:** نیت دقیق commit اکتبر برای نگه‌داشتن این flag مستند نشده است. **GUESS:** این یک باقیماندهٔ قدیمی/مهاجرت ناقص بود، نه الزام آگاهانهٔ classifier بصری. شاهد: moderator فقط صوت می‌فرستد؛ متن STATE مالک در ۳ نوامبر آن را «leftover visual requirement» می‌نامد و #167 را صریحاً تأیید می‌کند. |
| require_poppy_relevance_check | **FACT:** در config شش حساب true بود. | **FACT:** در config امروز هنوز true است، اما پس از #167 شرط نهایی is_poppy_playtime برای TikTok دیگر اجرا نمی‌شود. keyword check مستقل، همچنان فعال است. | **FACT:** تصمیم مالک در STATE: keyword caption/hashtags کافی است؛ فیلد بصری قدیمی نباید TikTok را متوقف کند. |
| خروجی و نسبت تصویر | **FACT:** سه حساب square_1080x1080 و سه حساب preserve_source بودند. | **FACT:** همین تفکیک فعلی باقی است: meloaep_، smyxae و menezxl9 مربع؛ rickkzn_، saylex.aep812 و poppytv_ae با نسبت اصلی. | **FACT:** PR #41 حفظ نسبت اصلی برای سه حساب را ثبت کرده؛ config فعلی نیز همین را نشان می‌دهد. |
| وضوح/کیفیت | **FACT:** حد کوتاه‌ترین ضلع برای شش حساب 480 بود؛ حساب‌های منتخب require_brain_quality_check=false داشتند. برای unknown-source حد 720 و quality check سخت‌گیرانه فعال بود. | **FACT:** مسیر ناشناس و شرط quality سخت‌گیرانهٔ مخصوص unknown حذف شد. حد شش منبع منتخب 480 باقی است؛ duration باید 3 تا 180 ثانیه و فایل حداقل 50,000 بایت باشد. codec whitelist و bitrate حداقل 10KB/s در policy فعلی برای شش منبع اجباری نیست. | **FACT:** PR #41 حذف unknown source را ثبت می‌کند؛ کد policy فعلی حد 480، مدت و اندازه را نشان می‌دهد. |
| سقف کیفیت دانلود و upscaling | **UNKNOWN:** مقدار پیشین قطعی کیفیت Cobalt در اسناد/PR body بررسی‌شده به‌روشنی مشخص نشده است. | **FACT:** درخواست Cobalt اکنون حداکثر کیفیت پیکربندی‌شدهٔ 1080 است؛ allow_upscale=false. منبع زیر 1080 در مسیر square بزرگ‌نمایی نمی‌شود؛ نسخهٔ اصلی با وضوح native حفظ می‌شود. برای preserve_source نسبت اصلی حفظ می‌شود. | **FACT:** PR #41 کیفیت 1080 و ممنوعیت upscaling را در scope ثبت می‌کند. مقدار دقیق قبلی را حدس نمی‌زنیم. |
| فیلتر تاریخ | **FACT:** discovery قدیمی حداکثر 90 روز را اعلام می‌کرد. | **FACT:** فیلتر تاریخ TikTok به Phase 6 و موتور per-channel since_utc موکول شد؛ در collector فعلی فیلتر 90روزه اعمال نمی‌شود. | **FACT:** PR #41: «Defer TikTok date filtering to Phase 6's per-channel since_utc engine». |
| dedupe | **FACT:** تشخیص duplicate خارجی و SHA256 فایل در pipeline وجود داشت. | **FACT:** URL کوتاه/دارای tracking به canonical URL با ID عددی ویدئو تبدیل می‌شود؛ همان video ID حتی با URL متفاوت یک هویت است. SHA256 دقیق فایل بعد از دانلود نیز بررسی می‌شود. perceptual hash در این مسیر دیده نشد. | **FACT:** PR #42 تست کرد که ID یکسان با URL دارای tracking و canonical فقط یک candidate بسازد. |
| caption و MEGA | **FACT:** PR #41 caption را plain text کرد و قرارداد نگهداری فایل دوردست را روشن کرد. | **FACT:** caption خروجی ثابت EDIT_CAPTION است؛ staging remote در MEGA حفظ می‌شود و فقط local copy پس از رسید قطعی موفق حذف می‌شود. cleanup زمان‌بندی‌شده فقط فایل staging با همهٔ ارجاع‌های sent و سن حداقل 24 ساعت را حذف می‌کند. | **FACT:** PR #41 plain text caption و retention را ثبت می‌کند؛ workflow cleanup حداقل 24 ساعت را اجرا می‌کند. |

**جمع‌بندی بصری:** **FACT:** در ۲۳ اکتبر قرارداد metadata/TikTok به keyword + music تغییر کرد، ولی flag و final guard قدیمی بصری کامل پاک نشدند. **UNKNOWN:** قصد دقیق آن لحظه ثبت نشده است. **GUESS:** باقی‌ماندن guard یک باقیماندهٔ ناخواسته بود؛ مالک در ۳ نوامبر همین را تأیید و PR #167 را تصویب کرد. PR #167 در [a2ca032](https://github.com/hosseinasgari898989-dev/rubika-agent/commit/a2ca032fb6b0c0a2705f954bc3de12f8100bc224) ادغام شد. پنج تست mocked برای موفقیت و رد keyword/audio/ambiguous/missing result سبز شدند.

**شاهدهای کد:** [config/tiktok_source_policy.json](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/config/tiktok_source_policy.json)، [policy.py](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/policy.py)، [e2e_from_report.py](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/e2e_from_report.py)، [vision_moderator.py](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/vision_moderator.py)، [PR #41](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/41)، [PR #42](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/42).

---

## 2) مسیر فعلی TikTok بعد از #167

### 2.1 زمان‌بندی و ورود metadata

1. **FACT — Termux discovery:** workflow آنلاین در دقیقه‌های 07 و 37 هر ساعت trigger می‌شود؛ یعنی هر نیم‌ساعت. یک gate پنج‌ساعته پس از آخرین intake موفق، اجرای واقعی discovery/intake را محدود می‌کند. **FACT:** collector فقط metadata می‌خواند؛ دانلود رسانه یا انتشار نمی‌کند. [workflow](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/.github/workflows/termux-docker-discovery.yml#L1-L20) و [daily_discovery.py](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/termux/daily_discovery.py#L41-L50).
2. **FACT — منابع:** شش حساب منتخب؛ حداکثر 8 مورد از هر حساب و سقف عملی 48 candidate در هر جمع‌آوری کامل. تعداد واقعی می‌تواند کمتر باشد. [config](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/config/tiktok_source_policy.json) و [collector](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/termux/daily_discovery.py#L41-L50).
3. **FACT — فیلتر صف:** URL canonical، ID عددی، منبع منتخب و keyword قبل از enqueue بررسی می‌شوند. keyword باید در caption یا hashtags شامل poppy یا playtime باشد. intake حداکثر چهار item جدید در هر اجرای کامل اضافه می‌کند؛ ترتیب بین سازندگان round-robin است. [queue_intake.py](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/queue_intake.py#L40-L155) و [L242-L320](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/queue_intake.py#L242-L320).
4. **FACT — Daily queue:** هر روز ساعت 07:00 UTC اجرا می‌شود؛ صف را می‌خواند، حداکثر 100 ردیف را می‌بیند، روزانه حداکثر 4 ارسال موفق و حداکثر 20 candidate attempt دارد. این 20 عدد تلاش برای itemهای صف است، نه تعداد retryهای شبکه. [workflow](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/.github/workflows/daily-queue-processor.yml#L1-L5) و [queue_worker.py](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/queue_worker.py#L20-L24).
5. **FACT — Mob X جداست:** لینک‌های تأییدشدهٔ Mob X هر 5 دقیقه poll می‌شوند؛ مسیر خبر رسمی Mob X هر 5 ساعت زمان‌بندی جداگانه و gate خودش را دارد. این دو، مسیر TikTok نیستند. [Mob X link workflow](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/.github/workflows/mob-x-link-processor.yml#L1-L25)، [official news workflow](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/.github/workflows/mob-x-news-production.yml#L1-L25).

### 2.2 Dedupe دقیقاً چیست؟

| لایه | **FACT — تعریف تکراری** | رفتار |
|---|---|---|
| URL ورودی | video ID عددی TikTok پس از canonicalization؛ URL کوتاه و URL دارای tracking به URL canonical تبدیل می‌شود. | یک video ID برابر یک identity؛ اختلاف URL یا query string هویت تازه نمی‌سازد. |
| گزارش discovery | زوج platform + external_id داخل همان گزارش. | duplicate_within_report کنار گذاشته می‌شود. |
| صف/D1 و ledger | tiktok:<external_id> در وضعیت‌های موجود صف یا seen_index/ledger. | هر ID موجود از enqueue دوباره کنار گذاشته می‌شود؛ status فعلی آن ملاک «تازه بودن ID» نیست. |
| فایل بعد از download | SHA-256 کامل فایل رسانه. | byte-identical file duplicate_file_hash؛ این مرحله پس از download و inspection است. |
| perceptual hash | **UNKNOWN / شاهدی پیدا نشد.** | شباهت تصویری/فریم در این مسیر dedupe نمی‌شود. |

شاهد: [tiktok_urls.py](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/tiktok_urls.py)، [queue_intake.py](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/queue_intake.py#L40-L155)، [pipeline.py](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/pipeline.py#L100-L155)، [inspector.py](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/inspector.py).

### 2.3 اعتبارسنجی، دانلود و moderation

6. **FACT — claim و quiet hours:** هنگام تغییر queue item از queued به processing، Worker مقدار quiet_hours را از D1 می‌خواند. snapshot بررسی‌شده: 00:30 تا 07:00 UTC؛ ابتدا شامل و انتها خارج از بازه است. فیلد enabled در JSON حاضر نیست؛ شرط کد enabled !== false است، پس بازه فعال محسوب می‌شود. در ساعت 04:26 UTC که snapshot گرفته شد، quiet hours هنوز فعال بود؛ 07:00 UTC به بعد این guard مانع نیست. [src/index.js](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/src/index.js#L1357-L1395). **FACT:** publishing_enabled=false و test_channel_only=true در D1 ثبت شده‌اند، اما این دو مقدار در مسیر Python sender به‌عنوان gate خوانده نمی‌شوند؛ quiet_hours در مرز queue claim خوانده می‌شود.
7. **FACT — Cobalt:** TikTok از Cobalt محلی استفاده می‌کند؛ درخواست videoQuality=1080، مهلت API برابر 45 ثانیه، stream برابر 90 ثانیه، حداکثر حجم 100 MiB و حداکثر زمان دریافت 180 ثانیه. فایل باید بیش از 50 KiB باشد. URL باید HTTPS و از دامنهٔ مجاز TikTok باشد. **FACT:** خود downloader عملیات re-encode انجام نمی‌دهد؛ فایل برگشتی را stream می‌کند. [downloaders.py](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/downloaders.py#L75-L220).
8. **FACT — file inspection:** ffprobe نوع ویدئو، duration، width/height و وجود audio stream را بررسی می‌کند؛ SHA-256 فایل نیز محاسبه می‌شود. سقف داخلی inspector برابر 500 MiB است؛ محدودیت Cobalt یعنی 100 MiB زودتر اعمال می‌شود. [inspector.py](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/inspector.py).
9. **FACT — حداقل رسانه:** کوتاه‌ترین ضلع حداقل 480 پیکسل؛ duration بین 3 و 180 ثانیه؛ حجم فایل حداقل 50,000 بایت. منبع square زیر 1080 با allow_upscale=false بزرگ‌نمایی مصنوعی نمی‌شود و نسخهٔ اصلی حفظ می‌شود. سه حساب preserve_source نسبت تصویر اصلی را حفظ می‌کنند. [policy.py](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/policy.py#L70-L145)، [pipeline.py](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/pipeline.py#L100-L230).
10. **FACT — audio stream:** اگر ffprobe هیچ audio stream نبیند، has_music=false و reason مربوط به نبود stream ایجاد می‌شود؛ AI برای آن فایل صدا فراخوانی نمی‌شود. gate نهایی TikTok الزام می‌کند validation.has_audio دقیقاً true باشد.
11. **FACT — music model:** فقط soundtrack صوتی به Cloudflare Workers AI فرستاده می‌شود؛ مدل دقیق Worker برابر @cf/cloudflare/clef-omni است. تصویر، فریم، caption و هویت سازنده ارسال نمی‌شود. FFmpeg حداکثر 60 ثانیهٔ صدا را به mono 16 kHz MP3 با bitrate برابر 48 kbps تبدیل می‌کند؛ سقف فایل صوتی 8 MiB است. خروجی CLEF به احتمال 0 تا 1 تبدیل می‌شود: ≥0.70 = true؛ ≤0.15 = false؛ بین این دو = null/ambiguous؛ missing/invalid نیز رد است. [vision_moderator.py](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/vision_moderator.py)، [src/index.js](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/src/index.js#L280-L330) و [L490-L600](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/src/index.js#L490-L600).
12. **FACT — retry moderation:** فقط خطاهای موقت allow-list شده، مانند بعضی timeoutها و HTTP 408/425/429/5xx، حداکثر یک retry داخلی دارند؛ مجموعاً 2 تلاش با مکث 0.75 ثانیه. ابهام موسیقی، no-audio، نبود keyword و رد کیفیت retry محتوایی نمی‌شوند. [recovery.py](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/recovery.py#L14-L24)، [pipeline.py](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/pipeline.py#L140-L220).

### 2.4 staging، caption و ارسال

13. **FACT — MEGA:** رسانهٔ stage شده در ریشهٔ /Rubika-Agent/staging نگهداری می‌شود؛ ledger مرجع storage key و SHA-256 را نگه می‌دارد و نسخهٔ معتبر در retry قابل بازیابی است. [mega.py](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/storage/mega.py)، [e2e_from_report.py](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/e2e_from_report.py).
14. **FACT — preserve_failed_media:** در دو حالت اصل خام نگه داشته می‌شود: (الف) دانلود کامل شده، اما staging در انتقال/MEGA شکست خورده و رد عمدی validation/duplicate/moderation نبوده است؛ (ب) ارسال Rubika failed یا uncertain شده است. original خام Cobalt به /Rubika-Agent/failed/ منتقل و از API موجود در Worker ثبت می‌شود؛ از رسانهٔ normalize‌شده به‌جای original استفاده نمی‌شود. خطای این sidecar نباید خطای اصلی ارسال را پنهان کند. فایل‌هایی که پیش از download وجود ندارند یا به‌علت رد عمدی validation/duplicate/moderation از مرحله حذف شده‌اند، original قابل نگهداری ندارند. [failed_media.py](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/failed_media.py)، [e2e_from_report.py](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/e2e_from_report.py).
15. **FACT — پاک‌سازی staging:** workflow پس از Daily، cleanup را با حداقل سن 24 ساعت و حداکثر 25 فایل اجرا می‌کند. فقط فایل‌های مستقیم staging که همهٔ ارجاع‌های ledger آن sent و timestamp معتبر قدیمی دارند قابل حذف‌اند؛ failed، uncertain و untracked محافظت می‌شوند. این cleanup، پوشهٔ failed/ را هدف نمی‌گیرد. [daily workflow](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/.github/workflows/daily-queue-processor.yml#L190-L220)، [storage_cleanup.py](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/storage_cleanup.py#L35-L160).
16. **FACT — caption:** متن caption ثابت EDIT_CAPTION است؛ caption AI-generated نیست. [edit_caption.py](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/edit_caption.py).
17. **FACT — مقصد:** مقدار خام Secret خوانده نشده است. کد، SHA-256 مقصد را با pin ثابت TEST مقایسه می‌کند؛ مقصد خالی یا هر مقصد دیگر به خطای rubika_destination_not_test_channel ختم می‌شود. این check در ابتدای run_one و دوباره در send_media انجام می‌شود. **UNKNOWN:** Secret فعلی هنوز مستقل بررسی نشده است. **FACT تاریخی:** Daily #37 از ابتدای run_one عبور کرد، پس در همان اجرا guard آن Secret را TEST تشخیص داده؛ این مدرک، تغییرنکردن Secret از آن زمان را ثابت نمی‌کند. [test_channel.py](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/test_channel.py#L7-L22)، [e2e_from_report.py](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/e2e_from_report.py#L338-L350)، [rubika_publisher.py](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/rubika_publisher.py#L23-L31).
18. **FACT — retry ارسال:** پاسخ قطعی ردشده از Rubika حداکثر 3 بار با مکث 1 ثانیه امتحان می‌شود؛ transport exception یا پاسخ ناقص/مبهم uncertain می‌شود و دوباره ارسال نمی‌شود تا پست تکراری ایجاد نشود. پس از رسید OK، message_id ثبت و local copy پاک می‌شود؛ remote MEGA باقی می‌ماند. [delivery.py](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/delivery.py#L22-L87).

**ریسک مهم:** **FACT:** config می‌گوید tiktok_to_rubika_enabled=true، ولی در run_one فقط delivery_enabled("youtube") بررسی می‌شود؛ این flag در مسیر TikTok مصرف نمی‌شود. همچنین PUBLISHING_ENABLED در Worker برابر false و D1 publishing_enabled=false است، اما Python sender این مقدارها را نمی‌خواند. در عمل guard مؤثر مستقیم برای این مسیر، pin مقصد TEST است. این اختلاف‌ها مستند می‌شوند؛ در این task هیچ‌کدام تغییر نکردند.

---

## 3) جدول کدهای رد و خطا در مسیر TikTok

کدها به تفکیک مرحله گروه‌بندی شده‌اند. کدهای «ممکن» لزوماً در runهای بررسی‌شده رخ نداده‌اند. retry فقط همان‌جایی انجام می‌شود که ستون آخر می‌گوید.

| مرحله / کد | معنا و تولیدکننده | retry / نتیجه |
|---|---|---|
| Intake: invalid_candidate_shape؛ canonical_url_invalid؛ external_id_invalid؛ canonical_url_id_mismatch | ساختار candidate، URL یا ID عددی نامعتبر/ناسازگار؛ queue_intake.py | **FACT:** رد نهایی همان candidate؛ از صف وارد نمی‌شود. |
| Intake: source_not_selected؛ required_keyword_missing | سازنده خارج از شش منبع مجاز، یا caption/hashtags فاقد poppy و playtime؛ queue_intake.py و policy.py | **FACT:** رد نهایی؛ بدون دانلود. required_keyword_missing در run #112 هفت بار دیده شد. |
| Intake: duplicate_within_report؛ skipped_existing_count | تکرار همان platform+video ID در یک report یا ID از قبل در صف/ledger/seen index؛ queue_intake.py | **FACT:** candidate دوباره وارد صف نمی‌شود؛ retry ندارد. skipped_existing_count یک metric است، نه reject code. |
| Pipeline: duplicate_external_id؛ duplicate_file_hash | ID در seen set یا SHA-256 فایل برابر فایل قبلی؛ pipeline.py | **FACT:** رد نهایی item؛ فایل دانلودشده در شاخهٔ duplicate پاک می‌شود؛ retry محتوا ندارد. |
| Media: media_validation_failed و زیرکدهای file_unreadable، empty_or_missing_file، file_size_limit، unsupported_media_type، file_hash_failed، ffprobe_not_installed، ffprobe_timeout، ffprobe_execution_failed، media_probe_failed، invalid_probe_response، invalid_video_stream | بازرسی فایل و ffprobe در inspector.py | **FACT:** خطای رسانه؛ برای validation نامعتبر retry محتوا ندارد. timeout صرفاً در allow-list مشخص‌شده می‌تواند از مسیر retry محدود بالادست استفاده کند؛ خطای فایل نهایی است. |
| Quality: source_below_policy_minimum_resolution؛ media_duration_out_of_range؛ media_size_below_minimum؛ source_below_minimum_square_normalization_resolution؛ unsupported_output_format_policy؛ normalized_output_invalid؛ preserved_output_invalid؛ source_and_destination_must_differ؛ source_invalid:<inspector-code>؛ upscale_disallowed_native_source_required؛ normalization_timeout؛ normalization_failed؛ normalized_output_validation_failed | حداقل ضلع 480، مدت 3–180، حجم حداقل 50KB، خروجی/normalize نامعتبر؛ policy.py/pipeline.py/normalizer.py | **FACT:** رد نهایی همان candidate؛ source_invalid جزئیات خطای inspector را حمل می‌کند؛ timeout در allow-list مشخص retry محدود می‌شود، بقیه final هستند. |
| Audio: no_audio؛ source_has_no_audio_stream | فایل stream صوتی ندارد؛ inspector.py/vision_moderator.py | **FACT:** رد نهایی؛ AI موسیقی برای no-audio فراخوانی نمی‌شود؛ music_probability می‌تواند null بماند. |
| Music: music_not_detected؛ music_not_detected_below_threshold | probability≤0.15 و has_music=false؛ Worker/pipeline | **FACT:** رد نهایی؛ threshold تغییر نمی‌کند. |
| Music: music_probability_ambiguous | 0.15<p<0.70؛ Worker | **FACT:** رد fail-closed؛ در allow-list retry محتوایی نیست؛ نیازمند داده/بازبینی است. در #31، #32، #34، #37 مشاهده شد. |
| Music: music_answer_missing_or_invalid؛ music_probability_invalid؛ music_api_invalid_response؛ music_model_output_invalid؛ music_inference_failed؛ music_usage_counter_unavailable؛ music_usage_guard_unavailable؛ moderation_audio_format_invalid؛ moderation_audio_size_invalid؛ moderation_audio_size_or_container_invalid؛ moderation_audio_base64_invalid؛ moderation_platform_invalid؛ workers_ai_binding_missing؛ needs_review_missing_moderation_fields_has_music | پاسخ مدل، احتمال خارج از بازه، audio payload/counter/binding نامعتبر یا moderation field مفقود؛ src/index.js، vision_moderator.py و policy.py | **FACT:** خطای پردازش؛ فقط کدهای transient مشخص مثل music_api_timeout و control_api_http_* در allow-list هستند؛ خطای فرمت/داده/مدل نهایی است. |
| FFmpeg: ffmpeg_not_installed؛ music_transcode_timeout؛ ffmpeg_execution_failed؛ music_transcode_failed؛ music_sample_too_small؛ music_sample_size_limit؛ video_inspection_failed؛ video_duration_unavailable | استخراج/آماده‌سازی صدای mono MP3 یا بررسی ویدئو ناموفق؛ vision_moderator.py | **FACT:** فقط music_transcode_timeout حداکثر یک retry داخلی دارد؛ سایر کدهای این ردیف final هستند. |
| Cobalt: cobalt_platform_not_supported؛ unsupported_video_platform_or_type؛ invalid_source_url؛ https_source_url_required؛ noncanonical_source_url؛ source_host_mismatch؛ ip_literal_source_rejected؛ permission_confirmation_required؛ destination_already_exists؛ cobalt_http_<status>؛ cobalt_request_timeout؛ cobalt_unreachable؛ cobalt_invalid_response؛ cobalt_rejected_source؛ cobalt_no_single_video؛ cobalt_unsafe_download_url؛ source_restricted_or_unavailable؛ cobalt_rate_limited | محدودیت پلتفرم/URL/مجوز/پاسخ API/tunnel؛ downloaders.py | **FACT:** خطاهای Cobalt در retry allow-list فعلی نیستند؛ همان تلاش final می‌شود و Daily سراغ item بعدی می‌رود. #35 یک cobalt_http_400 داشت. |
| Download stream: media_http_<status>؛ media_redirect_rejected؛ cobalt_returned_non_media؛ media_download_timeout؛ source_media_size_limit؛ media_download_failed؛ local_write_failed؛ source_media_too_small | دریافت stream؛ محدودیت زمان 180s، حجم 100MiB، فایل حداقل 50KiB؛ downloaders.py | **FACT:** خطای دانلود همان item final؛ retry عمومی Cobalt انجام نمی‌شود. |
| Staging/MEGA: no_new_relevant_candidate_in_report؛ staged_storage_key_missing؛ could_not_identify_single_staged_candidate؛ staged_media_file_missing؛ cloud_sync_failed؛ megacmd_not_installed؛ megacmd_timeout؛ megacmd_execution_failed؛ megacmd_command_failed_<command>؛ local_file_missing_or_empty؛ invalid_remote_filename؛ invalid_relative_directory؛ remote_path_outside_agent_root؛ invalid_remote_path؛ download_output_missing؛ sha256_mismatch | نبود candidate قابل انتخاب، stage/ledger/MEGA نامعتبر یا sync/storage ناموفق؛ e2e_from_report.py و mega.py | **FACT:** خطای همان item final می‌شود؛ retry مستقل خودکار برای همهٔ این موارد وجود ندارد. فقط reuse رسانهٔ stage‌شدهٔ معتبر در شرایط تعریف‌شده ممکن است. |
| Queue/E2E: no_candidate؛ already_seen_or_unavailable؛ queue_claim_<Exception>؛ control_api_unreachable؛ control_api_http_408/425/429/500/502/503/504؛ queue_read_failed؛ queue_worker_failed؛ rules_invalid_json؛ quiet_hours؛ staging_failed؛ brain_checks_not_publishable؛ pipeline_error_cobalt_http_400 | صف خالی/تکراری، خروجی نامعتبر worker، خطای claim/API، rules JSON نامعتبر، توقف quiet hours یا wrapper stage failure؛ queue_worker.py، src/index.js و e2e_from_report.py | **FACT:** no_candidate به skipped می‌رود؛ quiet_hours تا پایان بازه مانع claim است؛ فقط control_api_unreachable، statusهای مشخص HTTP و music_transcode_timeout در recovery allow-list هستند؛ باقی موارد fail/skip می‌شوند. |
| Final gate بعد از #167: required_tiktok_keyword_audio_music_check_not_passed | یکی از این سه شرط دقیقاً true نیست: keyword، validation.has_audio، moderation.has_music؛ e2e_from_report.py | **FACT:** رد نهایی؛ هیچ‌کدام خودکار به true تبدیل نمی‌شوند. |
| Destination: rubika_destination_not_test_channel؛ missing_RUBIKA_TOKEN؛ missing_RUBIKA_CHANNEL_GUID؛ rubika_publisher_unavailable؛ d1_attempt_preflight_failed؛ media_file_missing_or_empty | مقصد غیر TEST/خالی، publisher یا پیش‌شرط D1/فایل نامعتبر؛ publisher/e2e_from_report.py | **FACT:** مقصد غیر TEST فوراً fail-closed است. خطای D1 attempt preflight پیش از ارسال، ارسال را متوقف می‌کند. |
| Delivery: rubika_rejected_<status>؛ transport_<Exception>؛ ambiguous_response | پاسخ قطعی رد، خطای transport، یا پاسخ خالی/ناقص؛ delivery.py | **FACT:** رد قطعی تا 3 تلاش؛ transport یا پاسخ مبهم به uncertain تبدیل می‌شود و retry نمی‌شود تا duplicate ایجاد نشود. |
| Failed-media sidecar: failed_media_invalid_arguments؛ failed_media_local_file_missing؛ failed_media_id_missing؛ failed_media_upload_failed؛ failed_media_record_failed؛ failed_media_internal_error | ثبت original ناموفق در Mega failed/ یا API Worker؛ failed_media.py | **FACT:** تلاش کمکی است؛ خطای آن نباید نتیجهٔ اصلی ارسال را تغییر دهد؛ retry خودکار مستقل در این تابع تعریف نشده است. |
| Workflow preflight: required_credentials_missing؛ workflow_tests_failed؛ dependency_install_failed؛ ffmpeg_setup_failed؛ cobalt_api_must_be_local_http؛ cobalt_service_unavailable؛ mega_auth_or_listing_failed؛ rubika_bot_authentication_failed؛ rubika_main_destination_unavailable؛ delivery_receipt_missing | پیش‌شرط workflow، سرویس Cobalt/MEGA، احراز هویت Bot یا نبود رسید؛ daily-queue-processor.yml | **FACT:** قبل/حین workflow اجرا را متوقف می‌کند؛ retry در این workflow تضمین نشده و اجرای بعدی schedule جداگانه است. |
| Historical: required_visual_or_music_check_not_passed | guard قدیمی که برای TikTok is_poppy_playtime را هم الزام می‌کرد. | **FACT:** برای TikTok پس از #167 غیرفعال است؛ در runهای #31–#37 قبل از #167 رخ داده. برای غیر-TikTok مسیر قدیمی می‌تواند باقی باشد. |
| Historical: negative_or_insulting_text_signal؛ edit_signal_not_found_by_brain؛ poppy_relevance_not_found_by_brain | فیلترهای سیاست قدیمی TikTok. | **FACT:** برای TikTok پس از #41/#42 مسیر فعال نیستند؛ مسیرهای غیر-TikTok یا کد legacy را نباید با TikTok فعلی اشتباه گرفت. |

**نکتهٔ retry:** **FACT:** سقف 20 در Daily تعداد candidate attempt است؛ retry موسیقی/زیرساخت و retry Rubika جداگانه‌اند. queue itemهای شکست‌خورده در آن اجرا به failed می‌روند؛ مورد uncertain به دلیل احتمال ارسال موفق، اجرا را متوقف می‌کند و نیازمند تطبیق رسید است.

---

## 4) شواهد رفتاری Daily Queue

**FACT:** آخرین Daily Queue موجود در GitHub هنگام بررسی، #37 است. جدول زیر artifactهای هفت run پیاپی را نشان می‌دهد. هر مورد «attempted» یک ویدئوی TikTok است که Daily آن را پردازش کرده است. همهٔ runها صفر ارسال داشتند.

| Run / تاریخ UTC | مورد بررسی‌شده | نتیجه / reject code | music_probability | has_audio_stream |
|---|---:|---|---:|---|
| [#31 — 28 اکتبر](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/40700363624) | 1 | 0 sent؛ 1 music_probability_ambiguous | در artifact ثبت نشده | در artifact ثبت نشده؛ مسیر AI، وجود stream را محتمل می‌کند |
| [#32 — 29 اکتبر](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/40886826417) | 2 | 0 sent؛ یک required_visual_or_music_check_not_passed و یک music_probability_ambiguous | 0.8628 برای مورد بصری؛ مورد مبهم ثبت نشده | ثبت نشده؛ مورد با p=0.8628 در snapshot قدیمی D1 stream صوتی معتبر داشت |
| [#33 — 30 اکتبر](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/41066926726) | 1 | 0 sent؛ required_visual_or_music_check_not_passed | 0.9474 | ثبت نشده؛ snapshot قدیمی D1 stream را تأیید می‌کند |
| [#34 — 31 اکتبر](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/41205322518) | 1 | 0 sent؛ music_probability_ambiguous | ثبت نشده | ثبت نشده؛ مسیر AI، وجود stream را محتمل می‌کند |
| [#35 — 1 نوامبر](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/41314434060) | 2 | 0 sent؛ یک required_visual_or_music_check_not_passed و یک pipeline_error_cobalt_http_400 | 0.9269 برای مورد بصری | مورد بصری در snapshot قدیمی D1 stream داشت؛ مورد Cobalt، UNKNOWN |
| [#36 — 2 نوامبر](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/41485619799) | 1 | 0 sent؛ required_visual_or_music_check_not_passed | 0.9236 | ثبت نشده؛ snapshot قدیمی D1 stream را تأیید می‌کند |
| [#37 — 3 نوامبر](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/41658070960) | 2 | 0 sent؛ یک music_probability_ambiguous و یک required_visual_or_music_check_not_passed | در artifact ثبت نشده | در artifact ثبت نشده؛ item دوم از stage موسیقی را گذرانده بود |

**FACT:** جمع #31 تا #37 برابر 10 attempt و صفر ارسال است: 4 مورد ambiguous، 5 مورد که موسیقی را گذرانده بودند اما به visual gate قدیمی برخورد کردند، و 1 خطای Cobalt HTTP 400 پیش از ffprobe. جزئیات 10 مورد در [ITEM-13-MUSIC-AUDIT.md](https://github.com/hosseinasgari898989-dev/CENTER/blob/main/ITEM-13-MUSIC-AUDIT.md) ثبت شده است.

**FACT:** artifactهای #31–#37 قبل از PR #168 ساخته شدند؛ بنابراین فیلدهای music_probability و has_audio_stream در این گزارش‌ها وجود ندارند. «ثبت نشده» به معنی صفر یا false نیست. PR [#168](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/168)، merge SHA [b7750343](https://github.com/hosseinasgari898989-dev/rubika-agent/commit/b7750343a4fd9b88585e7433831cf509088a0a57)، این دو scalar تشخیصی را برای نتیجه‌های بعدی اضافه کرد؛ مقدار نامعتبر/ناموجود باید null بماند.

**FACT — آخرین ارسال موفق:** SELECT فقط‌خواندنی از D1 نشان داد آخرین TikTok item با status=sent در جدول items زمان 2026-10-23 12:41:33.027 UTC دارد؛ رکورد queue متناظر 12:41:34.845 UTC را ثبت کرده است. creator برابر rickkzn_ است. خود این ردیف D1 شناسهٔ مقصد کانال را نگه نمی‌دارد؛ **FACT:** کد مسیر فعلی فقط TEST را قبول می‌کند. **UNKNOWN:** مقصد واقعی همان ارسال تاریخی از فیلد D1 قابل اثبات مستقیم نیست.

**FACT:** این ارسال پیش از merge شدن PR #41 در 20:28 UTC همان روز رخ داده است. **GUESS:** توقف پایدار ارسال‌ها احتمالاً از تغییر سیاست ۲۳ اکتبر و بعدتر از mismatch میان moderation صوتی و visual-field قدیمی ناشی شد. اما بین ۲۳ تا اولین run موجود #31، Daily artifact کامل نداریم؛ روز دقیق توقف و علت تمام موارد بین این تاریخ‌ها **UNKNOWN** است.

**FACT:** #31 اولین شکست موجود در این سری است؛ اولین مورد آن به علت music_probability_ambiguous متوقف شد، پس همان مورد به visual gate نرسید. در #32 تا #37، مواردی که موسیقی را گذراندند به visual-field باقیمانده برخورد کردند. PR #167 در ۳ نوامبر ساعت 18:44 UTC و #168 ساعت 18:58 UTC ادغام شدند؛ Daily #37 ساعت 14:52 UTC، قبل از هر دو، اجرا شده بود.

**FACT — اجرای امروز:** ساعت snapshot حدود 04:26 UTC در ۴ نوامبر بود؛ برنامهٔ Daily امروز برای 07:00 UTC هنوز موعدش نرسیده بود. در فهرست Actions هیچ Daily run جدیدتری از #37 دیده نشد. پس نتیجهٔ Daily امروز **هنوز UNKNOWN/اجرا نشده در زمان بررسی** است؛ نه شکست و نه موفقیت گزارش نمی‌شود. [فهرست runها](https://github.com/hosseinasgari898989-dev/rubika-agent/actions).

---

## 5) ورودی صف و علت کم بودن TikTokهای queued

**FACT — آخرین discovery کامل:** Termux run #112 در ۳ نوامبر ساعت 23:12 UTC انجام شد و SUCCESS بود: [run #112](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/41737248839). شش حساب × حداکثر 8 مورد = 48 مورد discovery. از آن‌ها 41 مورد از فیلتر اولیه عبور کردند؛ 7 مورد به علت required_keyword_missing رد شدند: 6 مورد از smyxae و 1 مورد از saylex.aep812.

| آمار intake در #112 | تعداد | تفسیر |
|---|---:|---|
| metadata candidates | 48 | 6 حساب × 8 مورد |
| eligible پس از فیلتر keyword/source | 41 | 7 مورد بدون keyword حذف شدند |
| duplicate داخل همان report | 0 | ID تکراری داخل همین 48 مورد ثبت نشد |
| skipped_existing_count | 40 | همان TikTok video ID پیش‌تر در queue inventory یا seen_index/ledger وجود داشت |
| API duplicate_count / invalid_count | 0 / 0 | مورد تازه‌ای از API به‌عنوان duplicate یا invalid برنگشت |
| inserted_count | 1 | ویدئوی تازهٔ meloaep_ وارد صف شد |
| سقف ورود جدید | 4 | فقط 1 جایگاه از سقف چهار مورد پر شد |

**FACT:** پس از intake، D1 دو TikTok queued نشان می‌داد: یک item تازهٔ #112 و یک item قبلی. **FACT:** D1 در همان snapshot تعداد 56 failed، 22 sent و 7 skipped هم داشت. این شمارش‌ها متعلق به جدول automation_queue هستند؛ جدول items lifecycle مستقل خودش را دارد.

**FACT:** اجرای Termux #113 در ۴ نوامبر 03:09 UTC فقط gate پنج‌ساعته را با موفقیت اجرا کرد؛ job جمع‌آوری واقعی و queue intake آن skipped شد. بنابراین #113 مورد جدیدی به صف اضافه نکرد. [run #113](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/41765363917).

**FACT:** 40 مورد skipped_existing به معنی «40 فایل بایت‌به‌بایت یکسان» نیست. معیار این مرحله ID عددی TikTok است که قبلاً در queue/seen index دیده شده. duplicate بر اساس SHA-256 فقط بعد از دانلود بررسی می‌شود. در discovery اخیر هیچ duplicate داخل report و هیچ duplicate پاسخ API ثبت نشد. **UNKNOWN:** تعداد SHA-256 duplicate در 24 ساعت اخیر از گزارش intake قابل استخراج نیست؛ intake هنوز رسانه دانلود نمی‌کند. هیچ perceptual hash در این مسیر مشاهده نشد.

**FACT — D1 SELECT snapshot:** TikTok queue: 56 failed، 2 queued، 22 sent، 7 skipped. TikTok items: 43 ready، 29 sent، 1 uncertain. آخرین item موفق همان زمان 23 اکتبر است. query مربوط به failed_media برای source=tiktok هیچ ردیفی برنگرداند. تمام queryها فقط SELECT بودند؛ Cloudflare پاسخ داد rows_written=0 و changed_db=false. هیچ D1 write انجام نشد.

---

## 6) مشکلات باز و ریسک‌ها

### Blockers

- **FACT:** هنوز هیچ Daily طبیعی بعد از #167 اجرا نشده است؛ آخرین run #37 پیش از merge آن بود. اثبات end-to-end پس از اصلاح بصری هنوز وجود ندارد.
- **FACT:** چهار مورد از ده تلاش #31–#37 به music_probability_ambiguous رسیده‌اند. threshold ثابت است؛ بدون probability و audio diagnostics قدیمی، دربارهٔ محتوای واقعی صدا نمی‌توان نتیجه گرفت.
- **FACT:** مقصد فعلی Secret بررسی نشده است. guard در #37 مقصد را TEST تشخیص داد، اما همان مشاهده وضعیت امروز را ثابت نمی‌کند.
- **FACT:** در هر دو جدول D1 هنوز TikTok backlog/ready و موارد failed وجود دارند؛ retry خودکار برای ردهای محتوایی انجام نمی‌شود.

### Non-blockers / ریسک مهندسی

- **FACT:** flag پیکربندی tiktok_to_rubika_enabled=true است، اما کد TikTok آن را به‌عنوان guard اجرا نمی‌کند. این یک ناسازگاری قابل تصمیم‌گیری است؛ در این گزارش اصلاح نشده است.
- **FACT:** PUBLISHING_ENABLED=false در Worker و publishing_enabled=false در D1 وجود دارند، اما Python queue sender این مقدارها را نمی‌خواند. pin مقصد TEST guard مستقیم sender است؛ نباید مقدارهای غیرمرتبط را مجوز انتشار تلقی کرد.
- **FACT:** workflow زمان‌بندی‌شده hash مقصد را همانند مسیر دستی مقایسه نمی‌کند؛ فقط get_me/get_chat را پیش از pipeline انجام می‌دهد. guard Python همچنان پیش از پردازش و ارسال مقصد را pin می‌کند.
- **FACT:** require_poppy_relevance_check=true در شش ردیف config باقی مانده، ولی پس از #167 برای TikTok دیگر is_poppy_playtime را وارد final check نمی‌کند. مقدار tiktok_max_results=60 هم با collector فعلی 48 هماهنگ نیست؛ مقدار اجرایی collector همان 48 است.
- **FACT:** یک Cobalt HTTP 400 در #35 ثبت شده است؛ علت upstream آن در artifact مشخص نیست.
- **FACT:** D1 snapshot برای failed_media با source=tiktok نتیجهٔ خالی داشت؛ این query فقط وضعیت همان جدول/منبع در زمان بررسی را نشان می‌دهد، نه اینکه قابلیت کد وجود ندارد.
- **FACT:** MAIN قفل است؛ پاسخ صریح مالک در ۳ نوامبر NO بوده است. هیچ توصیه‌ای برای دورزدن guard یا پایین آوردن آستانه وجود ندارد.

### پرسش‌های تصمیم برای مالک

1. **تصمیم جداگانه:** آیا tiktok_to_rubika_enabled باید در مسیر TikTok به‌عنوان delivery gate واقعی خوانده شود، یا فعلاً همین تنظیم مستند اما بلااستفاده باقی بماند؟
2. **تصمیم جداگانه:** آیا flag قدیمی require_poppy_relevance_check از شش source config پاک شود، یا برای سازگاری/ردیابی legacy نگه داشته شود؟ این کار، اگر تأیید شود، PR جداگانه می‌خواهد.
3. **تأیید مشاهده‌ای:** آیا اولین Daily طبیعی پس از #167 را فقط در TEST برای تأیید end-to-end بررسی کنیم؟ پیش‌شرط نتیجه‌گیری، receipt موفق و log همان run است.
4. **بدون تغییر خودکار:** آیا آستانه‌های 0.70 و 0.15 و رفتار fail-closed بدون تغییر باقی بمانند؟ هر تغییر آستانه فقط پس از دادهٔ تشخیصی کافی و تأیید صریح مالک قابل طرح است.

---

## وضعیت تغییرات در این ITEM

**FACT:** rubika-agent/main در SHA بالا بدون تغییر باقی ماند. هیچ code/config/secret/variable/gate/quiet_hours/migration، D1 write، manual dispatch، publishing یا تغییر مقصد انجام نشد. D1 فقط SELECT شد و rows_written=0 / changed_db=false گزارش شد. کانال MAIN قفل است. PRهای #17، #23 و #166 و فایل‌های subtitle دست‌نخورده‌اند. این سند فقط گزارشی است؛ هیچ شرط انتشار را تغییر نمی‌دهد.


---

## 7) ITEM 16B — تفکیک شمارنده‌ها، وضعیت همهٔ صف‌ها و خطاهای قدیمی

**تاریخ snapshot:** 2026-11-04  
**نسخهٔ rubika-agent/main:** c6e5035c343135b97f5ee7766d2cf8b0e3757dbe  
**روش:** SELECT مستقیم روی D1؛ هیچ endpoint تغییر‌دهنده‌ای فراخوانی نشد.

### 7.1 چرا «pending=32» با «۲ TikTok queued» فرق دارد؟

**FACT:** شمارنده‌های صف پنل از جدول automation_queue می‌آیند. panel_screens.js وضعیت‌های واقعی را queued / processing / sent / failed / uncertain / skipped می‌خواند و به‌ترتیب برچسب‌های «در انتظار / در حال پردازش / ارسال‌شده / ناموفق / نامشخص / ردشده» را نمایش می‌دهد. homeSummary نیز queued و failed را مستقیم از همین جدول می‌گیرد. در نتیجه واژهٔ «pending» در گزارش مالک، معادل وضعیت دیتابیسی queued است؛ وضعیت مستقلی به نام pending در automation_queue وجود ندارد.  
شاهد: [panel_screens.js L4-L5](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/src/panel_screens.js#L4-L5)، [L45-L70](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/src/panel_screens.js#L45-L70)، [L105-L127](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/src/panel_screens.js#L105-L127).

| شمارندهٔ پنل | status واقعی در D1 | تعداد |
|---|---|---:|
| pending / در انتظار | automation_queue.queued | 32 |
| processing | automation_queue.processing | 0 |
| sent | automation_queue.sent | 22 |
| failed | automation_queue.failed | 56 |
| unknown / نامشخص | automation_queue.uncertain | 0 |
| rejected / ردشده | automation_queue.skipped | 7 |
| **کل صف** | **تمام statusهای automation_queue** | **117** |

**FACT:** از ۳۲ مورد در انتظار، فقط ۲ مورد TikTok هستند و ۳۰ مورد YouTube. پس گزارش ITEM 16 که «۲ TikTok queued» گفت درست بود، اما آن عدد کل صف نبود.  
**FACT:** «rejected=7» در این شمارنده نام نمایشی skipped است، نه console_jobs.status='rejected'. در جدول مستقل console_jobs دقیقاً ۲ job از نوع mob_x_link با status=rejected ثبت شده؛ آن دو داخل ۱۱۷ مورد صف نیستند.

### 7.2 تعداد به تفکیک جدول، پلتفرم و منبع

**FACT — automation_queue؛ شمارندهٔ اصلی پنل:**

| TikTok source_key | queued | processing | sent | failed | uncertain | skipped | کل |
|---|---:|---:|---:|---:|---:|---:|---:|
| meloaep_ | 1 | 0 | 1 | 17 | 0 | 3 | 22 |
| menezxl9 | 0 | 0 | 5 | 8 | 0 | 0 | 13 |
| poppytv_ae | 1 | 0 | 5 | 15 | 0 | 0 | 21 |
| rickkzn_ | 0 | 0 | 5 | 7 | 0 | 1 | 13 |
| saylex.aep812 | 0 | 0 | 5 | 8 | 0 | 0 | 13 |
| smyxae | 0 | 0 | 1 | 1 | 0 | 3 | 5 |
| **TikTok** | **2** | **0** | **22** | **56** | **0** | **7** | **87** |
| **YouTube** | **30** | **0** | **0** | **0** | **0** | **0** | **30** |
| **Mob X / mob_official** | **0** | **0** | **0** | **0** | **0** | **0** | **0** |
| **Instagram / سایر پلتفرم‌ها** | **0** | **0** | **0** | **0** | **0** | **0** | **0** |
| **کل automation_queue** | **32** | **0** | **22** | **56** | **0** | **7** | **117** |

**FACT:** هر ۳۰ مورد YouTube فقط queued هستند. queue worker فعلی فقط PLATFORMS=("tiktok") را پردازش می‌کند؛ بنابراین این ردیف‌های قدیمی YouTube در مسیر فعلی TikTok قرار نمی‌گیرند و در صف باقی می‌مانند.  
شاهد: [queue_worker.py L20-L25](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/queue_worker.py#L20-L25).

**FACT — جدول مستقل items؛ این وضعیت‌ها را با automation_queue جمع نکنید:**

| platform / source_key | ready | sent | uncertain | کل |
|---|---:|---:|---:|---:|
| TikTok meloaep_ | 10 | 4 | 0 | 14 |
| TikTok menezxl9 | 6 | 5 | 0 | 11 |
| TikTok poppytv_ae | 11 | 5 | 0 | 16 |
| TikTok rickkzn_ | 7 | 5 | 0 | 12 |
| TikTok saylex.aep812 | 8 | 5 | 0 | 13 |
| TikTok smyxae | 1 | 1 | 0 | 2 |
| TikTok s2gryder (legacy/غیر از شش منبع فعلی) | 0 | 4 | 1 | 5 |
| **TikTok items** | **43** | **29** | **1** | **73** |
| Mob X mobgamesstudios | 8 | 11 | 0 | 19 |
| Mob X x.com/mobgamesstudios | 0 | 6 | 0 | 6 |
| Mob X mobentertainment.com | 0 | 1 | 0 | 1 |
| **Mob X items** | **8** | **18** | **0** | **26** |
| **کل items** | **51** | **47** | **1** | **99** |

**FACT:** در snapshot فعلی، items هیچ ردیف YouTube و هیچ status=failed ندارد. ۵۱ ready در این جدول با ۳۲ queued در جدول صف یکی نیست. ۴ مورد sent و ۱ مورد uncertain برای s2gryder نیز در دادهٔ موجود باقی‌اند؛ این handle جزو شش منبع فعلی config نیست.

**FACT — Mob X jobها، جدول سوم و مستقل:**

| console_jobs.type=mob_x_link | تعداد |
|---|---:|
| failed | 11 |
| ready_for_review | 6 |
| rejected | 2 |
| sent | 11 |
| **کل jobها** | **30** |

این ۳۰ job نیز جزو شمارندهٔ ۱۱۷تایی automation_queue نیستند.

### 7.3 گروه‌بندی ۵۶ failed و ۷ skipped بر اساس error/reject code

بازه‌ها UTC هستند و از automation_queue.updated_at گرفته شده‌اند؛ یعنی بازهٔ آخرین به‌روزرسانی همان گروه، نه ادعای دقیق زمان وقوع خطا. تمام ۵۶ ردیف failed در snapshot مقدار attempt_count=1 دارند.

| status / code | تعداد | منبع‌ها | اولین updated_at | آخرین updated_at |
|---|---:|---|---|---|
| failed — required_visual_or_music_check_not_passed | 43 | poppytv_ae 11، meloaep_ 10، saylex.aep812 8، rickkzn_ 7، menezxl9 6، smyxae 1 | 2026-10-24 11:53:56 | 2026-11-03 14:54:40 |
| failed — music_probability_ambiguous | 8 | meloaep_ 5، poppytv_ae 3 | 2026-10-24 11:54:12 | 2026-11-03 14:54:29 |
| failed — pipeline_error_cobalt_http_400 | 3 | menezxl9 2، poppytv_ae 1 | 2026-10-20 00:17:07 | 2026-11-01 14:06:55 |
| failed — music_not_detected | 1 | meloaep_ 1 | 2026-10-25 12:27:34 | 2026-10-25 12:27:34 |
| failed — staging_failed | 1 | meloaep_ 1 | 2026-10-14 17:02:21 | 2026-10-14 17:02:21 |
| **جمع failed** | **56** | **شش source منتخب TikTok** | **2026-10-14** | **2026-11-03** |
| skipped — already_seen_or_unavailable | 7 | meloaep_ 3، smyxae 3، rickkzn_ 1 | 2026-10-14 17:02:22 | 2026-10-20 00:17:28 |

**FACT:** علت دقیق ردیف staging_failed از D1 قابل بازسازی نیست. ردیف متناظر در items و رخداد brain_events با همان item ID یافت نشد؛ فقط یک run قدیمی با staged=0 و skipped=1 ثبت شده است. بنابراین نسبت دادن آن به FFmpeg **UNKNOWN** است.

### 7.4 Retry، دو اصلاح قبلی و requeue

**FACT — visual-field / PR #167:** هر ۴۳ مورد required_visual_or_music_check_not_passed آخرین بار حداکثر تا ۳ نوامبر ساعت 14:54:40 UTC ثبت شده‌اند؛ PR [#167](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/167) ساعت 18:44:04 UTC همان روز merge شد و شرط بصری مانده را از gate نهایی TikTok حذف کرد. پس این ۴۳ ردیف با guard قدیمی شکست خورده‌اند. **GUESS:** اگر مالک بعداً همان ردیف‌ها را دستی requeue کند، این خطای قدیمی دیگر به‌تنهایی مانع نیست؛ اما هر ردیف همچنان باید keyword، audio stream و music=true را از guard فعلی بگذراند. موفقیت ارسال تضمین نمی‌شود.

**FACT — FFmpeg / PR #155:** PR [#155](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/155) در ۳ نوامبر ساعت 08:24:38 UTC merge شد. علتش FileNotFoundError: ffmpeg در تست‌های کامل workflow کشف Termux بود؛ مرحلهٔ test suite پیش از اجرای Docker discovery، report push و queue intake شکست می‌خورد. اصلاح، نصب ffmpeg/ffprobe و بررسی libass قبل از اجرای تست‌ها بود. این تغییر، خطای خود pipeline صف را تعمیر یا ردیف failed را requeue نمی‌کند. **FACT:** هیچ‌یک از ۵۶ error code فعلی نام FFmpeg ندارد و query روی brain_events نیز موردی با code/details حاوی ffmpeg پیدا نکرد. بنابراین هیچ‌یک از ۵۶ مورد را نمی‌توان به bug رفع‌شدهٔ #155 نسبت داد. staging_failed قدیمی همچنان **UNKNOWN** می‌ماند.

**FACT — retry داخلی:** recovery فقط خطاهای موقتِ allow-list مانند control_api_unreachable، برخی control_api_http_*، music_api_timeout و music_transcode_timeout را حداکثر یک بار retry می‌کند. هیچ‌کدام از پنج کد failure جدول بالا در این allow-list نیستند؛ در نتیجه هیچ‌کدام از ۵۶ ردیف در وضعیت فعلی retry خودکارِ داخلی ندارند. music_probability_ambiguous و music_not_detected رد محتوایی‌اند؛ cobalt_http_400 در allow-list نیست؛ staging_failed هم علت نامعلوم و بدون retry خودکار است.  
شاهد: [recovery.py L14-L64](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/recovery.py#L14-L64).

**FACT — intake عادی:** _existing_queue_inventory تمام statusهای صف، از جمله failed و skipped را می‌خواند و ID موجود را به فهرست شناسه‌های قبلی اضافه می‌کند. سپس ingest با قید یکتای (platform, external_id) و ON CONFLICT DO NOTHING انجام می‌شود. بنابراین discovery عادی فقط IDهای تازه را وارد می‌کند؛ failedها به‌طور خودکار دوباره وارد صف نمی‌شوند.  
شاهد: [queue_intake.py](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/queue_intake.py)، [src/index.js L1236-L1303](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/src/index.js#L1236-L1303).

**FACT — requeue دستی:** کنسول API عملیات جداگانهٔ requeue دارد که یک رکورد موجود با status=failed، uncertain یا skipped را دوباره queued می‌کند و last_error_code را پاک می‌کند. این کار دستی و صریح است؛ retry_now پشتیبانی نمی‌شود. در ITEM 16B این عملیات اجرا نشد و هیچ ردیفی تغییر نکرد.  
شاهد: [console_api.js L227-L245](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/src/console_api.js#L227-L245).

### 7.5 محدودیت و صحت snapshot

**FACT:** جمع ۱۱۷تایی D1 دقیقاً با شمارنده‌های اعلام‌شدهٔ پنل برابر است: queued=32, processing=0, sent=22, failed=56, uncertain=0, skipped=7. این آمار از automation_queue است؛ وضعیت‌های items و console_jobs جدا هستند.  
**FACT:** همهٔ queryها SELECT مستقیم بودند؛ پاسخ‌های D1 همگی rows_written=0 و changed_db=false داشتند. هیچ retry، requeue، حذف، تغییر کد/config، dispatch یا publishing انجام نشد. MAIN همچنان locked.


---

## 8) ITEM 17 — ممیزی YouTube queue، حذف کنسول، Daily و discovery gate (READ-ONLY)

**Snapshot:** 2026-11-04، پس از اجرای Termux شمارهٔ 113 و پیش از Daily ساعت 07:00 UTC  
**نسخه:** `rubika-agent/main = c6e5035c343135b97f5ee7766d2cf8b0e3757dbe`  
**روش:** فقط SELECT مستقیم روی D1؛ تمام پاسخ‌های query دارای `rows_written=0` و `changed_db=false` بودند.

### 8.1 منشأ ۳۰ ردیف YouTube و روش استخراج

**FACT — وضعیت فعلی D1:** دقیقاً ۳۰ ردیف `automation_queue` برای YouTube وجود دارد؛ هر ۳۰ مورد `queued`، با `attempt_count=0` هستند. هر ۳۰ مورد `method=termux_ytdlp_search_metadata`، `published_at=NULL` و `freshness_verified=0` دارند. در جدول lifecycle مستقل `items` هیچ ردیف YouTube وجود ندارد. پس این‌ها metadataهای ورودی صف‌اند، نه ویدئوهایی که دانلود/پردازش شده باشند.

**FACT — سه ورودی اولیه در ۱۴ اکتبر ۲۰۲۶ (UTC):**
- [Queue intake run #1](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/38559494829)، ساعت 11:51:06Z: گزارش ۴۹ مورد را دریافت و ۴۹ مورد را وارد کرد؛ D1 برای همین دقیقه ۲۵ ردیف YouTube ساخته‌شده دارد.
- [Queue intake run #2](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/38564609616)، ساعت 12:32:40Z: از ۴۹ مورد دریافتی، ۴ مورد جدید وارد و ۴۵ مورد تکراری شناخته شدند؛ D1 در 12:33 چهار ردیف YouTube دارد.
- [Queue intake run #3](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/38570071776)، ساعت 13:13:56Z: از ۴۹ مورد، ۲ مورد جدید وارد و ۴۷ مورد تکراری شناخته شدند؛ D1 در 13:14 یک ردیف YouTube دارد. ردیف دوم آن اجرا YouTube نیست.
- جمع D1: ۲۵ + ۴ + ۱ = **۳۰ YouTube**. هر سه اجرا مربوط به workflow قدیمی `.github/workflows/queue-intake.yml` بودند که روی push گزارش به شاخهٔ آزمایشی `agent-brain-mega-v1` اجرا می‌شد؛ نه workflow جدید Termux در نوامبر.

**FACT — queryها و source_keyهای فعلی (source_key نام سازنده/کانال ثبت‌شده در metadata است):**

| عبارت جست‌وجو | تعداد | کانال‌ها |
|---|---:|---|
| `CatNap Poppy Playtime edit short` | 9 | Lazzy Playtime، LOPI_Cc and ZedroxX، Cougar macdowall Va، GH'S، RayoxzxOFC، ABeast، Horror Skunx، Mob Entertainment، Lupus |
| `Huggy Wuggy edit shorts` | 12 | MemeEver، Poppy Boy، Temmmu، Lime360VR، Xeyal2010 and Kairo، GH'S، HanzMiaRZ، Coram Edith، Multo0641، SirAlienGaming، Efping، M2 ANIMATION |
| `Poppy Playtime edit shorts` | 9 | Efping، RNX87، GH'S، Dangeist، Tusker، Shadow milkᯓ★ |

**FACT — روش واقعی اجراشده:** کد تاریخی `agent/discovery/termux_sources.py` تابع `discover_youtube_metadata` را به‌صورت metadata-only تعریف می‌کند: `ytsearch`، `extract_info(..., download=False)`، گزینه‌های `skip_download=true` و `extract_flat=true`. docstring همان فایل صریحاً می‌گوید cookies ورود، proxy، عبور از browser challenge و دانلود رسانه استفاده نمی‌شود. گزارش Termux هم mode را `metadata_only_no_download_no_publish` ثبت می‌کرد. شواهد: [کد تاریخی YouTube metadata discovery](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/5defc843516f749414e65a73ffe8029dbc63cfdb/agent/discovery/termux_sources.py#L812-L864)، [workflow قدیمی queue-intake](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/5defc843516f749414e65a73ffe8029dbc63cfdb/.github/workflows/queue-intake.yml).

**FACT / GUESS دربارهٔ sign-in:** روش موفق برای این ۳۰ مورد فقط استخراج metadata با yt-dlp بوده، نه دانلود ویدئو. هیچ مدرکی وجود ندارد که Cobalt، relay یا cookies برای این ۳۰ مورد دانلودی انجام داده باشند. Cobalt در downloader فعلی YouTube را پشتیبانی نمی‌کند؛ مسیر دانلود واقعی YouTube در `e2e_from_report.py` از `YouTubeRelayDownloader` می‌گذرد، اما قبل از آن `delivery_enabled("youtube")` بررسی می‌شود. در `config/tiktok_source_policy.json` فقط `tiktok_to_rubika_enabled=true` وجود دارد و `delivery_enabled("youtube")` مقدار false می‌دهد؛ در نتیجه مسیر فعلی با `youtube_delivery_paused_by_owner` متوقف می‌شود. شواهد: [source_policy.py](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/source_policy.py)، [config](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/config/tiktok_source_policy.json)، [YouTube delivery guard و relay](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/e2e_from_report.py#L346-L398)، [Cobalt platform guard](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/downloaders.py#L83-L114).  
**GUESS / توضیح محتمل sign-in:** چون آن jobها فقط جست‌وجوی عمومی metadata کردند و `download=False` بود، اصلاً به درخواست media stream که می‌توانست sign-in بخواهد نرسیدند. این شاهدی برای رفع محدودیت sign-in در دانلود واقعی نیست. توانایی دانلود این ویدئوها بدون احراز هویت از این داده‌ها **UNKNOWN** است.

### 8.2 حذف از Web console: حذف واقعی یا تغییر status؟

**FACT — مقایسهٔ شمارنده‌ها:** در snapshot قبلی ITEM 16B تعداد YouTube برابر ۳۰ queued بود؛ SELECT فعلی نیز ۳۰ queued و صفر در statusهای دیگر نشان می‌دهد. تمام ۳۰ ردیف فعلی همان بازهٔ ایجاد ۱۴ اکتبر را دارند. در `brain_events` نیز هیچ رویداد `console_queue_delete` پیدا نشد. بنابراین شواهد D1 هیچ حذف موفق/کاهش خالصی از این ۳۰ ردیف را نشان نمی‌دهد؛ نمی‌توان از روی این داده دانست مالک روی چه دکمه‌ای کلیک کرده یا درخواستش ناموفق/لغو شده است.

**FACT — رفتار کد:** Web console در `docs/console/v2.html` برای دکمهٔ حذف، `POST /api/console/queue/{id}/action` با body برابر `{action:"delete"}` می‌فرستد ([L253-L257](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/docs/console/v2.html#L253-L257)). Worker آن را در `src/console_api.js` در route `POST /queue/{id}/action` می‌گیرد و در حالت delete دقیقاً `DELETE FROM automation_queue WHERE id=? AND status IN ('queued','failed','skipped')` اجرا می‌کند ([L227-L252](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/src/console_api.js#L227-L252)). پس اگر درخواست موفق شود، ردیف واقعاً از جدول حذف می‌شود؛ فقط status عوض نمی‌شود. سپس باید رویداد `console_queue_delete` در `brain_events` ثبت شود؛ این snapshot رویدادی با آن code ندارد.

### 8.3 آیا Daily ساعت 07:00 به YouTube دست می‌زند؟

**FACT:** schedule فایل `daily-queue-processor.yml` برابر `0 7 * * *` UTC است و worker با سقف ۲۰ candidate attempt اجرا می‌شود ([L3-L5](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/.github/workflows/daily-queue-processor.yml#L3-L5)، [L190-L198](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/.github/workflows/daily-queue-processor.yml#L190-L198)).

**FACT — ترتیب واقعی claim:** `CloudState.get_automation_queue(status="queued", limit=100)` همهٔ پلتفرم‌ها را از `GET /api/brain/automation-queue?status=queued` می‌خواند؛ SQL Worker فقط status را فیلتر می‌کند و platform filter ندارد ([cloud_state.py L420-L433](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/cloud_state.py#L420-L433)، [index.js L1324-L1351](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/src/index.js#L1324-L1351)). اما `queue_worker.py` فقط `PLATFORMS=("tiktok",)` را پشتیبانی می‌کند، TikTok را اول sort می‌کند، و هر پلتفرم دیگر را قبل از API claim با `platform_paused` و `queue_state=left_queued` deferred می‌کند؛ status و attempt_count آن‌ها تغییر نمی‌کند ([L21-L25](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/queue_worker.py#L21-L25)، [L217-L268](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/queue_worker.py#L217-L268)). شمارندهٔ ۲۰ attempt هم فقط بعد از فراخوانی `run_one` زیاد می‌شود ([L299-L306](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/agent/brain/queue_worker.py#L299-L306)). بنابراین **در Daily فعلی YouTubeها claim نمی‌شوند، قبل از TikTok قرار نمی‌گیرند و attempt cap را مصرف نمی‌کنند**؛ بعد از TikTok فقط به‌صورت deferred گزارش می‌شوند و در صف می‌مانند.

**ریسک دفاع لایهٔ Worker:** endpoint عمومی‌تر `POST /api/brain/automation-queue/{id}/state` در `src/index.js` transition از queued به processing را برای هر platform می‌پذیرد و attempt_count را افزایش می‌دهد؛ در این لایه allow-list فقط TikTok enforce نشده است ([L1357-L1397](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/src/index.js#L1357-L1397)، [route L1499-L1503](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/src/index.js#L1499-L1503)). این خطر در Daily فعلی رخ نمی‌دهد، چون Python worker پیش از claim YouTube را deferred می‌کند؛ اما caller دیگری می‌تواند مستقیماً YouTube را به processing ببرد. **پیشنهاد، اجرا نشده:** کوچک‌ترین hardening این است که Worker قبل از transition، برای `nextStatus==="processing"` هر platform غیر از `tiktok` را با 409 رد کند. اگر در آینده YT به‌اشتباه از فیلتر Python عبور کند، guard مستقل `delivery_enabled("youtube")=false` باعث خطای `youtube_delivery_paused_by_owner` پیش از انتخاب downloader می‌شود؛ اما چون این خطا پس از claim می‌رسد، مسیر فعلی Worker باید همان allow-list را نیز enforce کند.

### 8.4 Termux discovery از 23:12 UTC و نوبت کامل بعدی

- **#112 — intake کامل:** [run 41737248839](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/41737248839)، schedule در 2026-11-03 23:12:10Z؛ gate در 23:12:15Z مقدار `five_hour_interval_elapsed` و `should_run=true` داد. discovery در 23:14:33Z تمام شد. خلاصه: ۴۸ TikTok candidate، ۴۱ eligible، ۷ رد keyword، ۴۰ مورد از قبل موجود؛ D1 فقط **۱ مورد جدید** وارد کرد (TikTok)، `duplicate_count=0`. media_downloads=0 و rubika_posts=0.
- **#113 — gate skip:** [run 41765363917](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/41765363917)، schedule در 2026-11-04 03:09:47Z؛ تصمیم gate در 03:09:52Z برابر `five_hour_interval_not_elapsed`، `elapsed_seconds=14110` و `should_run=false` بود؛ job جمع‌آوری واقعی skipped شد و **۰ ردیف** وارد شد.
- state آخرین intake موفق را `2026-11-03T23:14:42Z` ثبت می‌کند؛ پنج ساعت بعد `2026-11-04T04:14:42Z` است. cron هر ساعت در دقیقه‌های 07 و 37 اجرا می‌شود. در Actions بعد از #113 و تا snapshot حدود 06:00 UTC اجرای Termux دیگری دیده نشد؛ بنابراین **نوبت زمان‌بندی‌شدهٔ بعدی 06:07 UTC** اولین تیک بعدی است و gate اکنون باز است؛ full intake در صورت تحویل cron باید در همان تیک اجرا شود. شاهد gate/state: [termux workflow L7-L53](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/.github/workflows/termux-docker-discovery.yml#L7-L53)، [collection state](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/artifacts/termux_collection_state.json).

### 8.5 اثبات quiet_hours قبل و از 07:00 UTC

**FACT — D1 rules:** `quiet_hours.start=00:30` و `quiet_hours.end=07:00` است؛ `enabled` مقدار false ندارد، پس gate فعال است. Worker `Date` را بر مبنای UTC به دقیقه تبدیل می‌کند و برای claim تیک‌تاک شرط `current >= start && current < end` را می‌سنجد؛ در این بازه پاسخ 409 با `quiet_hours` می‌دهد و UPDATE/افزایش attempt انجام نمی‌شود ([src/index.js L1373-L1397](https://github.com/hosseinasgari898989-dev/rubika-agent/blob/main/src/index.js#L1373-L1397)). بنابراین بازهٔ دقیق مسدود **00:30 تا 06:59:59 UTC** است؛ پیش از 00:30 مشمول این quiet window نیست. در **07:00 UTC دقیقاً** شرط `current < end` false است، پس claim مجاز می‌شود و در transition موفق attempt_count یک واحد افزایش می‌یابد. این quiet-hours check فقط برای TikTok است.

**نتیجه:** Daily برنامه‌ریزی‌شده در 07:00 UTC در مرز پایان quiet_hours قرار دارد؛ claimهای TikTok از همان 07:00 مجازند. هیچ مقدار quiet_hours در این ITEM تغییر نکرد.

**محدودیت‌ها و عدم تغییر:** بررسی D1 فقط SELECT بود؛ هیچ retry/requeue/delete، code/config/secret/variable/gate/migration/quiet_hours update، workflow dispatch یا publishing انجام نشد. این ITEM فقط شواهد را ثبت می‌کند؛ پیشنهاد hardening Worker اجرا نشده و MAIN همچنان locked است.
