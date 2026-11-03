# ITEM 13 — ممیزی رد شدن صدا در صف روزانهٔ TikTok

**تاریخ ممیزی:** 2026-11-03  
**بازه:** Daily queue runs #31 تا آخرین اجرای موجود #37.  
**مبنای بررسی:** artifactهای automation-daily-queue-report.json هر هفت اجرا، artifactهای discovery/metadata از Termux runهای #110 و #111، لاگ jobهای Daily Queue، و مسیرهای زندهٔ ffprobe/Cobalt/music moderation روی rubika-agent/main با SHA اولیهٔ ممیزی ea6fa127035504f8260e1d318eff1e18f3b913eb.

## جدول تمام ویدئوهای ردشده

مدت و source handle از metadata discovery آمده‌اند، نه از ffprobe فایل دانلودشده. در ستون audio، «بله — استنباط از مسیر» یعنی candidate به شاخه‌ای رسیده که فقط با وجود stream صوتی می‌تواند به مرحلهٔ AI یا stage موفق برسد؛ خروجی خام JSON از ffprobe در artifactها ذخیره نشده است.

| Daily run | ویدئو | منبع | مدت metadata | audio stream در فایل دانلودشده | music_probability | قاعدهٔ رد | reject code |
|---|---|---|---:|---|---:|---|---|
| [#31](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/40700363624) | 7701171449205247238 | meloaep_ | 47s | بله — مسیر AI اجرا شده؛ ffprobe خام ذخیره نیست | not stored | probability مبهم، 0.15 < p < 0.70 | music_probability_ambiguous |
| [#32](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/40886826417) | 7701736317968715028 | poppytv_ae | 16s | بله — D1 snapshot قبلی video/audio stream معتبر ثبت کرده | 0.8628 | music قبول شد؛ شرط بصری باقی‌مانده مقدار نداشت | required_visual_or_music_check_not_passed |
| [#32](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/40886826417) | 7701511937586187538 | meloaep_ | 23s | بله — مسیر AI اجرا شده؛ ffprobe خام ذخیره نیست | not stored | probability مبهم، 0.15 < p < 0.70 | music_probability_ambiguous |
| [#33](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/41066926726) | 7701939105051004168 | meloaep_ | 21s | بله — D1 snapshot قبلی video/audio stream معتبر ثبت کرده | 0.9474 | music قبول شد؛ شرط بصری باقی‌مانده مقدار نداشت | required_visual_or_music_check_not_passed |
| [#34](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/41205322518) | 7702518409187118356 | poppytv_ae | 15s | بله — مسیر AI اجرا شده؛ ffprobe خام ذخیره نیست | not stored | probability مبهم، 0.15 < p < 0.70 | music_probability_ambiguous |
| [#35](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/41314434060) | 7702922378208169224 | meloaep_ | 29s | بله — D1 snapshot قبلی video/audio stream معتبر ثبت کرده | 0.9269 | music قبول شد؛ شرط بصری باقی‌مانده مقدار نداشت | required_visual_or_music_check_not_passed |
| [#35](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/41314434060) | 7702848466682137908 | poppytv_ae | 16s | UNKNOWN — download قبل از inspect شکست خورد | not stored | خطای Cobalt HTTP 400؛ فایل قابل ffprobe در دسترس نبود | pipeline_error_cobalt_http_400 |
| [#36](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/41485619799) | 7703251923641715989 | poppytv_ae | 15s | بله — D1 snapshot قبلی video/audio stream معتبر ثبت کرده | 0.9236 | music قبول شد؛ شرط بصری باقی‌مانده مقدار نداشت | required_visual_or_music_check_not_passed |
| [#37](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/41658070960) | 7703481766677368084 | meloaep_ | 29s | بله — مسیر AI اجرا شده؛ ffprobe خام ذخیره نیست | not stored | probability مبهم، 0.15 < p < 0.70 | music_probability_ambiguous |
| [#37](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/41658070960) | 7703594468858989845 | rickkzn_ | 11s | بله — stage تا guard نهایی موسیقی را پذیرفته؛ ffprobe خام ذخیره نیست | not stored | music از stage عبور کرد؛ شرط بصری باقی‌مانده مقدار نداشت | required_visual_or_music_check_not_passed |

## پاسخ‌های مستقیم

**(a) صامت در برابر دارای صدا**

- **FACT:** از 9 ویدئویی که دانلود/inspect و moderation یا stage را طی کردند، 0 مورد با no_audio یا source_has_no_audio_stream رد نشده است.
- **FACT:** 4 مورد دارای مسیر اجرای AI با music_probability_ambiguous رد شده‌اند؛ وجود stream صوتی از همین مسیر استنباط می‌شود، چون نبود stream در pipeline پیش از AI به‌صورت قطعی reject می‌شود.
- **FACT:** 5 ویدئو به مرحلهٔ نهایی رسیدند و music قبول شده بود؛ رد آن‌ها visual-field mismatch بود، نه موسیقی. چهار موردشان در D1 snapshot قبلی هم has_music=true و audio/video stream معتبر داشتند.
- **UNKNOWN:** یک مورد با Cobalt HTTP 400 پیش از دانلود کامل/ffprobe متوقف شد؛ audio stream آن قابل تعیین نیست.
- **نتیجه:** برای این نمونه، 0 رد به‌علت نبود audio stream در برابر 4 رد به‌علت احتمال موسیقی مبهم با stream صوتی؛ 5 رد دیگر music rejection نبودند.

**(b) محتوای صوتی موارد مبهم**

**UNKNOWN برای هر چهار مورد.** artifact و log، speech، game sound، quiet music یا نسبت آن‌ها را ثبت نکرده‌اند. متن عنوان‌هایی مانند SONG: SPOOKY 3 صرفاً metadata است و اثبات نمی‌کند فایل دانلودشده چه صدایی داشته است. پرامپت Worker می‌گوید speech و sound effect به‌تنهایی music نیستند و در ابهام، مدل باید uncertainty اعلام کند؛ اما این چهار صوت در artifact ذخیره نشده‌اند.

**(c) احتمال muted/re-encoded شدن توسط Cobalt**

- **FACT:** دانلودر محلی Cobalt پاسخ API را می‌گیرد و media URL برگشتی را به فایل محلی stream می‌کند؛ در مسیر Python هیچ re-encode صوت/ویدئویی انجام نمی‌دهد.
- **UNKNOWN:** سرویس Cobalt یا منبع upstream ممکن است rendition دیگری تحویل دهد؛ در دادهٔ موجود hash/stream فایل اصلی TikTok با فایل دریافت‌شده مقایسه نشده است.
- **FACT:** 9 مورد پردازش‌شده در مسیر pipeline دارای audio stream قابل‌تشخیص بوده‌اند؛ این موضوع وجود stream را ثابت می‌کند، نه موسیقی/صدای قابل‌شنیدن. برای مورد HTTP 400 فایلی وجود نداشت که بررسی شود.
- **نتیجه:** احتمال فنی muted/re-encoded شدن منتفی نیست، اما هیچ مدرک مثبت یا منفی برای re-encode در این 10 item وجود ندارد.

**(d) آیا classifier بیش از حد سخت‌گیر یا آسان‌گیر است؟**

**UNKNOWN.** چهار مورد در منطقهٔ مبهم مانده‌اند، ولی probability دقیق و محتوای موج صوتی نداریم. پنج مورد دیگر به خاطر music رد نشدند. این شواهد برای قضاوت دربارهٔ precision/recall یا سخت‌گیری واقعی classifier کافی نیست. رفتار فعلی محافظه‌کارانه و fail-closed است؛ در این task هیچ threshold یا قاعده‌ای تغییر داده نمی‌شود.

## شواهد و مسیر کد

- Daily Queue #31–#37: artifactهای گزارش هر run فقط شناسه، status، reject reason و summary را نگه می‌دارند؛ فیلدهای music_probability و music_decision_reason برای این ردیف‌ها در report وجود ندارند.
- Termux metadata artifacts از runهای [#110](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/41615475983) و [#111](https://github.com/hosseinasgari898989-dev/rubika-agent/actions/runs/41686769479) source handle و duration metadata را دارند؛ فایل رسانه‌ای را دانلود نمی‌کنند.
- agent/brain/inspector.py: ffprobe وجود stream از نوع audio را تعیین می‌کند و InspectionResult.has_audio را می‌سازد. stdout JSON خام ffprobe در daily artifact/log ذخیره نمی‌شود.
- agent/brain/pipeline.py: اگر has_audio is False باشد بدون تماس AI مقدار has_music=False و reason no_audio تولید می‌شود؛ در غیر این صورت music moderator با has_audio=True وارد می‌شود. false، missing یا ambiguous موسیقی موجب eligible نشدن خواهد شد.
- agent/brain/vision_moderator.py: فقط audio را به Worker می‌فرستد؛ اگر stream صوتی نباشد، reason source_has_no_audio_stream می‌دهد.
- src/index.js: clefProbability مقدار احتمال را از پاسخ صوتی استخراج می‌کند؛ conservativeDecision با true threshold برابر 0.70 و false threshold برابر 0.15 تصمیم می‌گیرد. مقادیر بین این دو null و reason music_probability_ambiguous هستند. این قاعده در این task تغییر نمی‌کند.
- agent/brain/downloaders.py: Cobalt downloader فایل برگشتی را stream می‌کند و خودش کد encode/transcode ندارد.

## پیشنهاد تشخیصی کوچک — فقط پیشنهاد، اجرا نشده

برای report روزانهٔ sanitized، یک فیلد has_audio_stream از InspectionResult.has_audio و music_probability از پاسخ moderator در report شکست ذخیره شود. کمترین مسیر لازم:

1. هنگام ساخت skipped_row در pipeline.py، فقط boolean has_audio_stream را کنار probability موجود اضافه کن.
2. در _stage_failure_summary در e2e_from_report.py فقط boolean و probability عددی محدودشده به بازهٔ 0..1 را عبور بده.
3. در _safe_failure_diagnostics در queue_worker.py همان دو مقدار را با type-check در automation-daily-report.json نگه دار.

هیچ فایل صوتی، payload provider، URL خصوصی، متن خام مدل یا credential ذخیره نشود. این تغییر در ITEM 13 پیاده‌سازی نشده است.

**دامنهٔ ممیزی:** فقط READ-ONLY. هیچ threshold، moderation rule، code، Worker، تنظیم، D1، Secret، workflow، queue item یا مقصد انتشار در این بخش تغییر نکرد.
