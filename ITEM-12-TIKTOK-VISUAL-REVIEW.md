# ITEM 12 — بررسی PR #166 و چک بصری TikTok

**تاریخ:** 2026-11-03  
**دامنه:** بررسی read-only مخزن rubika-agent روی main با SHA برابر 2c6d3ca971f19682f5a1a7ff85ce4e2777681a27. هیچ کد، تنظیم، D1، Secret، gate، workflow یا مقصد انتشاری تغییر نکرده است. این سند فقط ثبت بررسی و گزینه‌های تصمیم مالک است.

## 1) مقایسهٔ PR #166 با #165

### وضعیت و محدوده

- [PR #165](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/165) ادغام شده است. SHA نهایی آن 5fe33cfab5afea481008cda1d957bfe72e9f7fe2 و merge/main SHA برابر 2c6d3ca971f19682f5a1a7ff85ce4e2777681a27 است.
- [PR #166](https://github.com/hosseinasgari898989-dev/rubika-agent/pull/166) هنوز OPEN است؛ head برابر afbdc0a9c172bbb351ac90fdedcf77cb548ddcdb و base اولیهٔ آن 8196baef7c2481a3ad7daec85ecbe946c45050f3 بوده است.
- مقایسهٔ GitHub برای #166 نسبت به main وضعیت diverged، پنج commit جلو و یک commit عقب را نشان می‌دهد. GitHub مقدار mergeable=false گزارش می‌کند.
- فایل‌های #165: agent/brain/e2e_from_report.py، tests/test_failed_tiktok_preservation.py و .github/workflows/brain-ci.yml.
- فایل‌های #166: agent/brain/e2e_from_report.py، agent/brain/queue_worker.py، tests/test_e2e_from_report.py و tests/test_queue_worker.py.

### رفتار مشترک و میزان supersede

**FACT:** هر دو PR برای شکست/نامطمئن‌بودن ارسال TikTok تلاش می‌کنند فایل اصلی Cobalt را از فایل آمادهٔ انتشار جدا نگه دارند و آن را با helper موجود در Mega failed/ ذخیره و در Worker ثبت کنند. پس قابلیت اصلی preservation در #165 ادغام شده است.

**FACT:** #165 پوشش کامل‌تری از مسیرهای فعلی دارد: شکست delivery، فایل بالای 50 MiB، خطاهای staging پس از دانلود کامل، خطای sync، جلوگیری از نگهداری فایل در حالت sent و retry/idempotency. تست جدید در workflow مربوط به Agent Brain نیز صریحاً اجرا می‌شود.

**تفاوت مهم:** #166 علاوه بر helper و تست‌های مشابه، یک تغییر یکتا در queue_worker.py و تست یکتای tests/test_queue_worker.py دارد: افزودن وضعیت امن و sanitize‌شدهٔ failed_media به diagnostics گزارش صف.

اما این تغییر با خروجی #165 هم‌نام نیست:
- #166 در _safe_failure_diagnostics مقدار result["failed_media"] را می‌خواند.
- نسخهٔ فعلی run_one() در main پس از #165، وضعیت preservation را با کلید failed_media_preservation برمی‌گرداند.
- بنابراین کد diagnostics در #166 با قرارداد فعلی خروجی هماهنگ نیست و به شکل فعلی مقدار جدیدی را نشان نمی‌دهد. برای کارکرد باید با قرارداد فعلی تطبیق داده و جداگانه تست شود.

### نتیجه دربارهٔ بستن #166

**FACT:** #166 از نظر مسیر اصلی نگهداری فایل ناموفق، توسط #165 supersede شده است؛ ولی diffها یکسان نیستند و #166 دو فایل گزارش/تست یکتا دارد.

**FACT:** بستن #166، قابلیت فعال preservation را از main حذف نمی‌کند؛ آن قابلیت از قبل در #165 ادغام شده است. تنها کد گزارش diagnostics و تست همراهش که هنوز ادغام نشده‌اند از دست می‌روند؛ این کد در شکل فعلی نیز به‌دلیل mismatch نام فیلد کار نمی‌کند.

**GUESS:** علت محتمل mergeable=false، قدیمی‌بودن base و تغییر هم‌پوشان agent/brain/e2e_from_report.py در #165 است. API وجود non-mergeable را تأیید می‌کند، اما نوع دقیق conflict در پاسخ دریافت‌شده مشخص نشده است.

**اقدام:** هیچ تغییری در #166 انجام نشد؛ نه edit، نه close و نه merge. تصمیم بستن یا نگه‌داشتن آن با مالک است.

## 2) منشأ و علت mismatch در چک بصری

### تاریخچه

1. **15 اکتبر 2026 — FACT:** commit اولیهٔ [3a0ae601](https://github.com/hosseinasgari898989-dev/rubika-agent/commit/3a0ae601969381bbdf6d5b3b158b485763bcf6a5) همراه با اولین ادغام Agent Brain، فیلد is_poppy_playtime را در فهرست moderation قرار داد و در e2e_from_report.py برای source policy دارای require_poppy_relevance_check=true آن را شرط لازم کرد. توضیح بالای policy می‌گفت Workers AI باید visual و audio واقعی را بررسی کند و gate در نبود سیگنال قطعی fail-closed بماند.
2. **همان commit — FACT:** مسیر moderate_music() از ابتدا صریحاً audio-only بود و مستند کد می‌گفت هیچ سؤال بصری ارسال نمی‌شود. در policy قدیمی، مقدار is_poppy_playtime از metadata_relevant یعنی متن عنوان/کپشن/هشتگ محاسبه می‌شد؛ نه از یک طبقه‌بند تصویر. بنابراین از تاریخچه نمی‌توان نتیجه گرفت که vision model واقعی قبلاً در این مسیر اجرا شده است.
3. **23 اکتبر 2026 — FACT:** commit [97578088](https://github.com/hosseinasgari898989-dev/rubika-agent/commit/97578088cebcdc58fa6cf520e07fb38c84410b8c) policy را برای TikTok بازنویسی کرد. تابع required_moderation_fields() از آن پس فقط has_music را لازم می‌داند؛ پیاده‌سازی جدید TikTok فقط تطبیق کلمهٔ poppy یا playtime در caption/hashtags و بررسی‌های فنی را نگه می‌دارد و دیگر checks["is_poppy_playtime"] را تولید نمی‌کند.
4. **FACT:** guard نهایی e2e_from_report.py و flag اجباری در هر شش ردیف config/tiktok_source_policy.json هم‌زمان به‌روزرسانی نشدند. در نتیجه policy همچنان is_poppy_playtime=true می‌خواهد، ولی moderator فعلی در vision_moderator.py تنها MP3 می‌سازد و moderate_music() را فراخوانی می‌کند؛ هیچ فریم تصویری نمی‌فرستد و این فیلد را برنمی‌گرداند.
5. **GUESS:** mismatch قابل‌مشاهده احتمالاً از ناقص‌ماندن مهاجرت قرارداد moderation در تغییر 23 اکتبر ایجاد شده است. در commit/comment بررسی‌شده توضیحی پیدا نشد که نشان دهد مالک آگاهانه شرط بصری را حذف کرده باشد؛ بنابراین intent دقیق تغییر UNKNOWN است.

**نتیجهٔ عملی:** required_visual_or_music_check_not_passed یک guard واقعی و قطعی است، نه خطای تصادفی. تا وقتی سیگنال بصری معتبر ساخته نشود، عبور TikTok نباید مجاز شود. گزارش run #36 نیز صفر ارسال و 30 آیتم queued را ثبت کرده است؛ کد صف روزانه حداکثر 20 candidate را امتحان می‌کند و سقف چهار ارسال موفق، تعداد inferenceهای لازم را به چهار محدود نمی‌کند.

## 3) گزینه‌های چک بصری و برآورد مصرف

### فرض مشترک برای مقایسه

این فقط برآورد ظرفیت است، نه اندازه‌گیری واقعی: از هر ویدئو پنج فریم کم‌حجم در نقاط زمانی پخش‌شده استخراج شود. در سقف فعلی queue worker، حداکثر 20 candidate می‌توانند در یک نوبت روزانه بررسی شوند؛ backlog ثبت‌شده 30 مورد است. شمار فراخوانی‌ها بر اساس candidate بررسی‌شده است، نه تعداد ارسال‌های موفق.

### گزینهٔ A — ffmpeg frame sampling + vision model

| Provider | فراخوانی برای هر ویدئو | 20 تلاش candidate در روز | 30 مورد backlog | هزینه/محدودیت مهم |
|---|---:|---:|---:|---|
| Cloudflare Workers AI — @cf/meta/llama-3.2-11b-vision-instruct | 5 (یک فریم در هر درخواست، فرض محافظه‌کارانه) | 100 درخواست | 150 درخواست در کل | رایگان تا 10,000 Neurons در روز برای کل حساب Workers AI؛ تخمین مدل پایین‌تر آمده است. |
| Groq — qwen/qwen3.8-27b | 2 (حداکثر 3 تصویر در هر درخواست) | 40 درخواست | 60 درخواست در کل | سقف داخلی پروژه 200 درخواست در روز؛ هر تصویر 2,048 input tokens است. سهمیهٔ دقیق Free حساب Groq از این بررسی قابل اثبات نیست و در سطح سازمان/حساب تعیین می‌شود. |
| OpenRouter — مدل رایگان vision مانند nvidia/nemotron-3.5-super-vl:free | 5 (یک فریم در هر درخواست؛ فرض محافظه‌کارانه) | 100 درخواست | 150 درخواست در کل | سقف داخلی پروژه 50 درخواست در روز؛ نسخه‌های :free نیز 50 درخواست در روز و 20 درخواست در دقیقه دارند. در این فرض، 20 candidate/day از سقف بیشتر است. |

#### تخمین Neurons برای Cloudflare

قیمت اعلام‌شدهٔ مدل Llama 3.2 11B Vision برابر $0.0485 به ازای یک میلیون input token و $0.676 به ازای یک میلیون output token است. با نرخ $0.011 برای هر 1,000 Neurons، تقریباً 4.41 Neurons به ازای هر 1,000 input token و 61.45 Neurons به ازای هر 1,000 output token می‌شود.

**فرض GUESS برای برآورد:** هر فریم به‌علاوهٔ prompt حدود 1,200 تا 2,200 input token و خروجی کوتاه 100 token مصرف کند. این اعداد توکن واقعی تصویر این مدل نیستند؛ مستندات اندازه‌گیری tokenization فریم را در اختیار نمی‌گذارند.

- حدود 12 تا 16 Neurons برای هر فریم/درخواست.
- حدود 60 تا 80 Neurons برای هر ویدئو با 5 فریم.
- حدود 1,200 تا 1,600 Neurons برای 20 ویدئو در روز.
- حدود 1,800 تا 2,400 Neurons برای کل 30 ویدئو.

این برآورد تنها 12–16٪ سقف 10,000 روزانه است؛ اما سهمیه بین همهٔ درخواست‌های Workers AI حساب مشترک است و مصرف موجود music moderation نیز از آن استفاده می‌کند. headroom واقعی حساب و Neurons مصرفی برای هر فریم UNKNOWN است؛ باید پس از مجوز، از داشبورد اندازه‌گیری شود. استفاده از این مدل نیازمند پذیرش Meta License و Acceptable Use Policy با درخواست اولیهٔ prompt="agree" است؛ این پذیرش/درخواست در این بررسی انجام نشده است.

#### Groq و OpenRouter

- **Groq — FACT:** مستند qwen/qwen3.8-27b حداکثر سه تصویر در هر درخواست و 2,048 توکن ورودی برای هر تصویر را اعلام می‌کند. پنج فریم بنابراین دو درخواست برای هر ویدئو می‌شود: 40 درخواست برای 20 candidate و 60 درخواست برای backlog سی‌تایی. سقف 200/day در STATE قانون داخلی پروژه است، نه تضمین provider. محدودیت دقیق Free در حساب مالک و مصرف مشترک سایر قابلیت‌های Groq UNKNOWN است؛ مستند Groq می‌گوید rate limit دقیق در صفحهٔ سازمان/حساب دیده می‌شود.
- **OpenRouter — FACT:** مدل رایگان Nemotron 3.5 Super VL در صفحهٔ رسمی فعلی ورودی متن/تصویر/ویدئو را پشتیبانی می‌کند. برای مدل‌های :free محدودیت 20 درخواست در دقیقه و 50 درخواست در روز است و تلاش‌های ناموفق نیز شمرده می‌شوند. اگر هر فریم یک درخواست جدا باشد، 20 candidate به 100 درخواست و از سقف 50/day فراتر می‌رود؛ 30 مورد حداقل سه روز ظرفیت می‌خواهد. تجمیع چند فریم در یک درخواست ممکن است کارایی را بهتر کند، اما محدودیت تعداد تصویر/قالب نهایی برای مدل انتخابی در این بررسی اندازه‌گیری نشده است. صفحهٔ همین endpoint رایگان هشدار می‌دهد دادهٔ محرمانه یا شخصی، از جمله چهره و صدا، در آن ارسال نشود؛ برای ویدئوهای دارای افراد این محدودیت باید جدی گرفته شود.

### گزینهٔ B — guard حفظ شود و TikTok مسدود بماند

هزینهٔ جدید: صفر درخواست تصویری، صفر Neurons و صفر تغییر کد. نتیجه این است که ویدئوها تا ارائهٔ سیگنال بصری معتبر همچنان fail-closed می‌مانند و صف پیشرفت انتشار نخواهد داشت. این امن‌ترین وضعیت موقت است، اما mismatch فنی را حل نمی‌کند.

### گزینهٔ C — تغییر قاعده فقط با متن صریح مالک

مالک می‌تواند صریحاً تصمیم بگیرد که برای شش منبع منتخب، تطبیق کلمهٔ caption/hashtags به‌همراه موسیقی و checks فنی جایگزین visual verification شود. هزینهٔ vision صفر می‌شود؛ در عوض سیستم دیگر از تصویر خود ویدئو اثبات نمی‌گیرد و کپشن/هشتگ گمراه‌کننده می‌تواند عبور کند. این گزینه تغییر policy/guard و تست‌ها می‌خواهد و یک کاهش عمدی سطح کنترل است؛ بدون دستور مکتوب مالک مجاز نیست و در این کار پیاده‌سازی نشده است.

## 4) fail-closed لازم برای هر پیاده‌سازی آینده

هر مورد زیر باید رد/غیرقابل‌انتشار بماند؛ هیچ fallback به pass وجود نداشته باشد:

- ffmpeg، استخراج فریم، decode یا خواندن تصویر شکست بخورد؛ تعداد فریم‌های قابل‌خواندن کمتر از حد تعیین‌شده باشد.
- timeout، 429، quota، خطای provider، پاسخ خالی، JSON خراب، نبود فیلد boolean یا confidence ناکافی رخ دهد.
- پاسخ دربارهٔ تعلق تصویر به Poppy Playtime مبهم یا بین فریم‌ها متناقض باشد.
- مدل فقط از caption، hashtag، نام سازنده یا صدا نتیجه بگیرد؛ هیچ‌کدام جای اثبات تصویری را نمی‌گیرند.

تنها پاسخ بصری معتبر و مطابق thresholdی که مالک تصویب می‌کند می‌تواند is_poppy_playtime=true بسازد. در غیر این صورت مقدار false/null یا خطای مشخص برگردد و guard فعلی رد کند. هیچ threshold یا guardی در این کار تغییر نکرده است.

## 5) پیشنهاد و تصمیم‌های لازم از مالک

**پیشنهاد:** گزینهٔ A با Cloudflare Workers AI را برای یک پیاده‌سازی محدود و فقط در مسیر TEST انتخاب کنیم؛ پنج فریم، یک درخواست برای هر فریم، خروجی ساختاریافته و fail-closed. این مسیر به سهمیهٔ Workers AI موجود تکیه می‌کند، تعداد محاسبه‌شده زیر 10,000 Neurons/day است، و نیازی به ارسال فریم اشخاص به OpenRouter free endpoint ندارد. ابتدا مالک باید مدل و مجوز آن را بپذیرد؛ هزینهٔ واقعی tokenization و headroom نیز در یک تست مجاز اندازه‌گیری شود. تا آن زمان گزینهٔ B عملی باقی می‌ماند.

1. آیا مجوز می‌دهی visual classifier واقعی با پنج فریم ffmpeg و حفظ کامل guard فعلی ساخته شود؟
2. آیا مدل Cloudflare @cf/meta/llama-3.2-11b-vision-instruct را با پذیرش Meta License و سقف مشترک 10,000 Neurons/day تأیید می‌کنی؟
3. برای is_poppy_playtime=true حداقل چند فریم از پنج فریم باید شواهد روشن داشته باشند؛ یک فریم کافی است یا حداقل دو فریم؟
4. آیا هر خطای decode/provider/quota، پاسخ مبهم یا نتیجهٔ متناقض باید مثل امروز reject بماند و هیچ bypass دستی نداشته باشد؟

## منابع

- [Cloudflare Workers AI pricing — سهمیهٔ 10,000 Neurons/day و قیمت‌گذاری](https://developers.cloudflare.com/workers-ai/platform/pricing/)
- [Cloudflare Llama 3.2 11B Vision — مدل تصویری، قیمت token و نیاز به پذیرش مجوز](https://developers.cloudflare.com/workers-ai/models/llama-3.2-11b-vision-instruct/)
- [Groq Vision — حداکثر سه تصویر در درخواست و 2,048 tokens/image](https://console.groq.com/docs/vision)
- [Groq rate limits — محدودیت‌های سازمانی/حسابی](https://console.groq.com/docs/rate-limits)
- [OpenRouter Nemotron 3.5 Super VL free — ورودی image/video و هشدار دادهٔ شخصی](https://openrouter.ai/nvidia/nemotron-3.5-super-vl:free)
- [OpenRouter free model limits — 20 RPM و 50 RPD](https://openrouter.ai/collections/free-models/)

**وضعیت نهایی:** فقط بررسی و مستندسازی. PR #166 دست‌نخورده و باز است؛ rubika-agent/main روی SHA 2c6d3ca971f19682f5a1a7ff85ce4e2777681a27 تغییر نکرده؛ هیچ run، dispatch، D1 write، ارسال یا انتشار جدیدی انجام نشد.
