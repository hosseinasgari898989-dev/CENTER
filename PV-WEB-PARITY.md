# مقایسهٔ قابلیت‌های PV و کنسول وب

تاریخ بررسی: 2026-11-03  
مبنای بررسی: کد زندهٔ شاخهٔ `main` در `rubika-agent`؛ فایل قدیمی `docs/console/PARITY.md` مورخ 2026-10-27 فقط نقطهٔ شروع بود و چند وضعیت آن دیگر معتبر نیست.

**معنی وضعیت‌ها:** «هست» = قابلیت فعال و قابل استفاده؛ «ناقص» = بخشی از جریان یا محدودیت دارد؛ «نیست» = در آن رابط پیاده‌سازی نشده. وجود endpoint داخلی Actions به‌تنهایی به معنی وجود قابلیت قابل استفاده برای مالک در وب نیست.

| دستور یا قابلیت | PV | endpoint / مسیر PV | وب | endpoint / مسیر وب |
|---|---|---|---|---|
| منوی اصلی و شروع `/start`، `/menu`، «منو» | هست | دکمه‌ها و پیام‌های `POST /panel/hook/{hook}/ReceiveUpdate` | هست | ناوبری کنسول `docs/console/v2.html` |
| راهنما `/help` | هست | callback `p_help` در hook پنل | ناقص | صفحهٔ راهنمای معادل ندارد؛ توضیح‌ها در همان صفحات هستند |
| وضعیت `/status` | هست | `GET /api/status` از داخل PV | هست | داشبورد؛ `GET /api/status` |
| صف `/queue` | هست | `GET /api/brain/owner-console/queue-summary`؛ فهرست PV از D1 | هست | `POST /api/console/d1/query` (فقط SELECT) و `POST /api/console/queue/:id/action` |
| گزارش کامل `/report` | هست | ترکیب `/api/status`، `/api/brain/owner-console/queue-summary` و وضعیت AI | ناقص | داشبورد و تاریخچه جدا هستند؛ گزارش یکپارچهٔ معادل ندارد |
| هوش مصنوعی `/ai` و `/ai-status` | هست | `GET /api/brain/ai-provider/status` | هست | `GET /api/brain/ai-provider/status` و `GET /api/console/ai/calls` |
| خلاصهٔ ۲۴ ساعت `/digest` | هست | خواندن مستقیم `owner_digest_state` و `owner_digest_events` در PV | ناقص | تاریخچه و رخدادها موجودند؛ صفحهٔ خلاصهٔ هم‌ارز ۲۴ ساعته ندارد |
| خطاها `/errors` | هست | خواندن `owner_digest_events` و `brain_events` در PV | هست | `GET /api/console/events` و `GET /api/console/brain-events` |
| اجراها `/runs` | هست | خواندن جدول `runs` در PV | هست | `GET /api/console/runs` |
| تاریخچهٔ تماس AI `/ailog` | هست | خواندن `ai_provider_calls` در PV | هست | `GET /api/console/ai/calls` |
| عیب‌یابی `/debug` | هست | callback `p_debug` در hook پنل | نیست | endpoint یا صفحهٔ عیب‌یابی هم‌ارز ندارد |
| فهرست و ویرایش آیتم‌ها | نیست | — | هست | `GET /api/console/items` و `PATCH /api/console/items/:id`؛ وضعیت‌های قفل‌شده قابل ویرایش نیستند |
| عملیات صف: بازگردانی، رد، حذف | ناقص | PV فهرست و خلاصهٔ صف را می‌خواند؛ عملیات عمومی این سه‌گانه در PV نیست | هست | `POST /api/console/queue/:id/action`؛ `retry_now` پشتیبانی نمی‌شود و 501 می‌دهد |
| افزودن لینک رسمی Mob X | هست | متن در حالت `waiting_link`؛ ثبت در `panel_links` از hook پنل | هست | صفحهٔ «حالت فوری»؛ `POST /api/console/links` |
| تأیید یا لغو لینک قبل از پردازش | هست | callbackهای `p_mob_confirm` و `p_mob_cancel` در hook پنل | هست | `POST /api/console/links/:id/confirm` و `POST /api/console/links/:id/cancel` |
| تأیید نهایی مالک برای انتشار خبر Mob X | هست | `p_mob_publish_job_*`؛ ثبت تأیید و dispatch پردازش انتشار به کانال تست | نیست | دکمهٔ مالک در وب وجود ندارد؛ `POST /api/console/mob-x/publish-claim` فقط endpoint داخلی runner است |
| ویرایش یا رد پیش‌نمایش Mob X | هست | `p_mob_edit_job_*`، متن کپشن در حالت ویرایش، و `p_mob_reject_job_*` | نیست | وب فقط جزئیات job را می‌خواند؛ endpointهای `preview/transition` برای runner هستند |
| تأیید دستی نتیجهٔ ارسال نامشخص Mob X | هست | `p_mob_manualconfirm_job_*`؛ فقط وضعیت را ثبت می‌کند و ارسال تازه انجام نمی‌دهد | نیست | دکمه یا endpoint مالک در وب ندارد |
| پیش‌نمایش ویدیوی جریان Mob X | هست | فایل پیش‌نمایش در گفت‌وگوی PV با Rubika ارسال می‌شود | نیست | جزئیات Job فقط وضعیت پیش‌نمایش را نشان می‌دهد؛ preview وب برای این جریان وجود ندارد |
| مشاهدهٔ جزئیات Job | ناقص | `p_jobs` فهرست خلاصهٔ کارها را نشان می‌دهد | هست | `GET /api/console/jobs` و `GET /api/console/jobs/:id`؛ جزئیات Mob X فقط‌خواندنی است |
| ایجاد Job عمومی | نیست | — | ناقص | `POST /api/console/jobs`؛ فعلاً رکورد ثبت می‌شود و موتور اجرای عمومی ندارد |
| تلاش مجدد Jobهای ناموفق Mob X | هست | `p_fail`، `p_failgo_*`، `p_failall`؛ حداکثر ۵۰ مورد | هست | `POST /api/console/jobs/retry`؛ انتخابی یا همهٔ ناموفق‌ها، حداکثر ۵۰ مورد |
| انتخاب و حذف گروهی لینک‌های غیرفعال | هست | `p_lsel` و `p_lseldel_*`؛ فقط لینک‌های غیرفعال | هست | `POST /api/console/links/bulk` با `action=delete`؛ فقط غیرفعال‌ها |
| پاک‌سازی لینک‌های غیرفعال با تأیید | هست | `p_links_clear` و `p_links_clear_yes` | هست | `POST /api/console/links/clear`؛ تأیید سمت سرور |
| ثبت منبع و مدیریت منابع | نیست | — | هست | `GET/POST /api/console/sources` و `PATCH/DELETE /api/console/sources/:id`؛ TikTok و X (نگاشت به `mob_official`) |
| مدیریت مالک‌ها و دعوت | ناقص | `/claim <code>` برای عضویت در PV؛ مدیریت کامل فهرست مالک‌ها در PV نیست | هست | `GET/POST /api/console/owners`، `POST /api/console/owners/invite` و `DELETE /api/console/owners/:id` |
| تنظیمات سقف روزانه | هست | `p_set` و `p_set_cap_*`؛ فقط `daily_success_cap` | هست | `GET/PUT /api/console/rules`؛ فیلدهای بیشتری قابل ویرایش‌اند |
| مشاهدهٔ ساعت سکوت و تبدیل UTC/تهران | هست | صفحهٔ `p_set`؛ نمایش فقط‌خواندنی و تبدیل UTC+03:30 | هست | صفحهٔ قوانین؛ نمایش UTC و تهران. مقدارهای ساعت سکوت در فرم فعلی قابل ویرایش نیستند |
| کانال‌ها و قفل کانال اصلی | ناقص | نمایش مقصد کانال تست و قفل بودن اصلی؛ مدیریت CRUD ندارد | ناقص | `GET/POST /api/console/channels` و `DELETE /api/console/channels/:id`؛ کانال تولیدی سمت Worker غیرفعال می‌ماند و toggle فعال‌سازی ندارد |
| کاوشگر D1 | نیست | — | هست | `GET /api/console/d1/tables` و `POST /api/console/d1/query`؛ فقط SELECT |
| ارسال‌نشده‌ها: فهرست، دانلود و پیش‌نمایش Mega | نیست | — | هست | `POST /api/console/failed-media/list`؛ UI در `v2-unsent.js` |
| حذف تکی/گروهی فایل‌های ارسال‌نشده | نیست | — | هست | `POST /api/console/failed-media/delete-request`؛ چالش HMAC پنج‌دقیقه‌ای، عبارت دقیق `DELETE` و سقف ۵۰ مورد در هر مرحله |
| گزارش رخداد و ممیزی | ناقص | `/debug` و صفحه‌های خطا؛ همهٔ تاریخچهٔ ممیزی وب را ندارد | هست | `GET /api/console/audit`، `/events`، `/brain-events` و `/delivery-attempts` |
| ثبت دوبارهٔ webhook با `/register` | هست | فرمان مالک در PV؛ `GET /panel/setup` برای setup/ثبت webhook | نیست | مسیر وب معادل ندارد |
| تغییر حالت همگام‌سازی `/panelmode atomic|inplace` | هست | فرمان مالک در PV؛ ذخیره در `panel_state` | نیست | endpoint یا کنترل وب ندارد |

## ملاحظات امنیت و اجرا

- Web console بعد از PR #150 برای خواندن و endpointهای runner از `AGENT_API_TOKEN` و برای تمام درخواست‌های تغییردهندهٔ کنسول از `OWNER_WEB_TOKEN` جدا استفاده می‌کند. endpointهای اختصاصی Actions از این جداسازی مستثنا هستند و همچنان AGENT token می‌خواهند.
- فیلد توکن مالک در صفحهٔ «اتصال» جداست. مالک باید مقدار Secret از پیش تنظیم‌شده را خودش در همین فیلد مرورگر ذخیره کند؛ مقدار Secret در این بررسی خوانده یا چاپ نشده است.
- وجود `/mob-x/publish-claim` به معنی امکان تأیید انتشار از وب نیست؛ این endpoint فقط برای runner است. تأیید نهایی، ویرایش، رد، تأیید دستی وضعیت نامشخص و preview ویدیوی Mob X همچنان در PV انجام می‌شوند.
- هر دو رابط با قفل Worker به کانال تست محدودند؛ کانال اصلی از کنسول قابل بازکردن نیست.
- در این مقایسه هیچ درخواست تغییردهنده، عملیات D1، dispatch یا انتشار اجرا نشد؛ بررسی فقط از روی کد زنده انجام شده است.
