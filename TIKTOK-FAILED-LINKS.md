# لینک‌های ۵۶ ویدئوی ناموفق TikTok

**Snapshot:** 2026-11-04 (UTC)  
**مبنای استخراج:** تمام ردیف‌های `automation_queue` با `platform=tiktok` و `status=failed`. تاریخ ستون از `updated_at` و به UTC استخراج شده است. ۵۶ لینک یکتا هستند و همگی شکل canonical عمومی TikTok دارند.  
**music_probability:** در هیچ‌یک از ۵۶ رکورد، این کلید در هیچ سطحی از `metadata_json` ذخیره نشده است؛ بنابراین برای همه «ذخیره نشده» درج شده است.

## A) خطای gate بصری قدیمی — ۴۳ مورد

**ارزیابی دستی:** احتمال requeue پس از اصلاح gate در PR #167 وجود دارد، اما نه به‌صورت دسته‌جمعی. کد `required_visual_or_music_check_not_passed` یکپارچه است و به‌تنهایی ثابت نمی‌کند علت تک‌تک ردیف‌ها فقط بخش بصری بوده است. مالک پیش از هر requeue، فعال‌بودن ویدئو، وجود کلیدواژهٔ Poppy/Playtime در caption/hashtags، stream صوتی و عبور از شرط موسیقی را بررسی کند.

| تاریخ شکست (UTC) | Handle | لینک عمومی TikTok | کد خطا | music_probability |
|---|---|---|---|---|
| 2026-10-24 | `saylex.aep812` | [https://www.tiktok.com/@saylex.aep812/video/7694281183424695566](https://www.tiktok.com/@saylex.aep812/video/7694281183424695566) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-24 | `poppytv_ae` | [https://www.tiktok.com/@poppytv_ae/video/7692118289916366100](https://www.tiktok.com/@poppytv_ae/video/7692118289916366100) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-24 | `menezxl9` | [https://www.tiktok.com/@menezxl9/video/7693269088096800020](https://www.tiktok.com/@menezxl9/video/7693269088096800020) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-24 | `saylex.aep812` | [https://www.tiktok.com/@saylex.aep812/video/7696947046560828686](https://www.tiktok.com/@saylex.aep812/video/7696947046560828686) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-24 | `menezxl9` | [https://www.tiktok.com/@menezxl9/video/7691730352603139329](https://www.tiktok.com/@menezxl9/video/7691730352603139329) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-24 | `saylex.aep812` | [https://www.tiktok.com/@saylex.aep812/video/7693538721173441806](https://www.tiktok.com/@saylex.aep812/video/7693538721173441806) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-24 | `rickkzn_` | [https://www.tiktok.com/@rickkzn_/video/7685506230663564562](https://www.tiktok.com/@rickkzn_/video/7685506230663564562) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-24 | `meloaep_` | [https://www.tiktok.com/@meloaep_/video/7696815102422535445](https://www.tiktok.com/@meloaep_/video/7696815102422535445) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-24 | `poppytv_ae` | [https://www.tiktok.com/@poppytv_ae/video/7691346934396636468](https://www.tiktok.com/@poppytv_ae/video/7691346934396636468) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-24 | `rickkzn_` | [https://www.tiktok.com/@rickkzn_/video/7685044831130799367](https://www.tiktok.com/@rickkzn_/video/7685044831130799367) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-24 | `poppytv_ae` | [https://www.tiktok.com/@poppytv_ae/video/7690974075333774612](https://www.tiktok.com/@poppytv_ae/video/7690974075333774612) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-24 | `meloaep_` | [https://www.tiktok.com/@meloaep_/video/7691225380782443782](https://www.tiktok.com/@meloaep_/video/7691225380782443782) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-24 | `menezxl9` | [https://www.tiktok.com/@menezxl9/video/7697687196685470996](https://www.tiktok.com/@menezxl9/video/7697687196685470996) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-24 | `saylex.aep812` | [https://www.tiktok.com/@saylex.aep812/video/7697977500944715021](https://www.tiktok.com/@saylex.aep812/video/7697977500944715021) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-24 | `poppytv_ae` | [https://www.tiktok.com/@poppytv_ae/video/7698040313272716564](https://www.tiktok.com/@poppytv_ae/video/7698040313272716564) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-24 | `meloaep_` | [https://www.tiktok.com/@meloaep_/video/7697725297369615624](https://www.tiktok.com/@meloaep_/video/7697725297369615624) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-24 | `rickkzn_` | [https://www.tiktok.com/@rickkzn_/video/7698153421584485652](https://www.tiktok.com/@rickkzn_/video/7698153421584485652) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-24 | `saylex.aep812` | [https://www.tiktok.com/@saylex.aep812/video/7697671267813510414](https://www.tiktok.com/@saylex.aep812/video/7697671267813510414) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-24 | `poppytv_ae` | [https://www.tiktok.com/@poppytv_ae/video/7697296424328908052](https://www.tiktok.com/@poppytv_ae/video/7697296424328908052) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-25 | `saylex.aep812` | [https://www.tiktok.com/@saylex.aep812/video/7697436822854569229](https://www.tiktok.com/@saylex.aep812/video/7697436822854569229) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-25 | `saylex.aep812` | [https://www.tiktok.com/@saylex.aep812/video/7697296799689673998](https://www.tiktok.com/@saylex.aep812/video/7697296799689673998) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-25 | `poppytv_ae` | [https://www.tiktok.com/@poppytv_ae/video/7698402908974468404](https://www.tiktok.com/@poppytv_ae/video/7698402908974468404) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-25 | `meloaep_` | [https://www.tiktok.com/@meloaep_/video/7698209038206635272](https://www.tiktok.com/@meloaep_/video/7698209038206635272) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-25 | `smyxae` | [https://www.tiktok.com/@smyxae/video/7698432890459163924](https://www.tiktok.com/@smyxae/video/7698432890459163924) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-25 | `rickkzn_` | [https://www.tiktok.com/@rickkzn_/video/7698527822419086613](https://www.tiktok.com/@rickkzn_/video/7698527822419086613) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-25 | `saylex.aep812` | [https://www.tiktok.com/@saylex.aep812/video/7698787158676507917](https://www.tiktok.com/@saylex.aep812/video/7698787158676507917) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-25 | `meloaep_` | [https://www.tiktok.com/@meloaep_/video/7698885851409042709](https://www.tiktok.com/@meloaep_/video/7698885851409042709) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-25 | `rickkzn_` | [https://www.tiktok.com/@rickkzn_/video/7698879317366951189](https://www.tiktok.com/@rickkzn_/video/7698879317366951189) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-25 | `poppytv_ae` | [https://www.tiktok.com/@poppytv_ae/video/7699139055509916948](https://www.tiktok.com/@poppytv_ae/video/7699139055509916948) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-25 | `meloaep_` | [https://www.tiktok.com/@meloaep_/video/7699229181837708551](https://www.tiktok.com/@meloaep_/video/7699229181837708551) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-25 | `menezxl9` | [https://www.tiktok.com/@menezxl9/video/7699152613102652693](https://www.tiktok.com/@menezxl9/video/7699152613102652693) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-25 | `poppytv_ae` | [https://www.tiktok.com/@poppytv_ae/video/7699525584312896788](https://www.tiktok.com/@poppytv_ae/video/7699525584312896788) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-25 | `meloaep_` | [https://www.tiktok.com/@meloaep_/video/7699589755071237383](https://www.tiktok.com/@meloaep_/video/7699589755071237383) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-25 | `poppytv_ae` | [https://www.tiktok.com/@poppytv_ae/video/7699891421825781013](https://www.tiktok.com/@poppytv_ae/video/7699891421825781013) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-25 | `menezxl9` | [https://www.tiktok.com/@menezxl9/video/7699571350867840277](https://www.tiktok.com/@menezxl9/video/7699571350867840277) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-25 | `menezxl9` | [https://www.tiktok.com/@menezxl9/video/7698428662835645716](https://www.tiktok.com/@menezxl9/video/7698428662835645716) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-26 | `meloaep_` | [https://www.tiktok.com/@meloaep_/video/7700477011994692865](https://www.tiktok.com/@meloaep_/video/7700477011994692865) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-27 | `rickkzn_` | [https://www.tiktok.com/@rickkzn_/video/7701033777455320341](https://www.tiktok.com/@rickkzn_/video/7701033777455320341) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-29 | `poppytv_ae` | [https://www.tiktok.com/@poppytv_ae/video/7701736317968715028](https://www.tiktok.com/@poppytv_ae/video/7701736317968715028) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-10-30 | `meloaep_` | [https://www.tiktok.com/@meloaep_/video/7701939105051004168](https://www.tiktok.com/@meloaep_/video/7701939105051004168) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-11-01 | `meloaep_` | [https://www.tiktok.com/@meloaep_/video/7702922378208169224](https://www.tiktok.com/@meloaep_/video/7702922378208169224) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-11-02 | `poppytv_ae` | [https://www.tiktok.com/@poppytv_ae/video/7703251923641715989](https://www.tiktok.com/@poppytv_ae/video/7703251923641715989) | `required_visual_or_music_check_not_passed` | ذخیره نشده |
| 2026-11-03 | `rickkzn_` | [https://www.tiktok.com/@rickkzn_/video/7703594468858989845](https://www.tiktok.com/@rickkzn_/video/7703594468858989845) | `required_visual_or_music_check_not_passed` | ذخیره نشده |

## B) موسیقی مبهم — ۸ مورد

**ارزیابی دستی:** فعلاً requeue محتمل نیست مگر پس از تأیید دستی وجود موسیقی واقعی و عبور از guard فعلی. مالک صدای ویدئو و علت ابهام را بررسی کند؛ خود مقدار احتمال در D1 ذخیره نشده است.

| تاریخ شکست (UTC) | Handle | لینک عمومی TikTok | کد خطا | music_probability |
|---|---|---|---|---|
| 2026-10-24 | `meloaep_` | [https://www.tiktok.com/@meloaep_/video/7692305726496050484](https://www.tiktok.com/@meloaep_/video/7692305726496050484) | `music_probability_ambiguous` | ذخیره نشده |
| 2026-10-25 | `poppytv_ae` | [https://www.tiktok.com/@poppytv_ae/video/7698782975357390101](https://www.tiktok.com/@poppytv_ae/video/7698782975357390101) | `music_probability_ambiguous` | ذخیره نشده |
| 2026-10-25 | `poppytv_ae` | [https://www.tiktok.com/@poppytv_ae/video/7700270826423192852](https://www.tiktok.com/@poppytv_ae/video/7700270826423192852) | `music_probability_ambiguous` | ذخیره نشده |
| 2026-10-26 | `meloaep_` | [https://www.tiktok.com/@meloaep_/video/7700078049198361874](https://www.tiktok.com/@meloaep_/video/7700078049198361874) | `music_probability_ambiguous` | ذخیره نشده |
| 2026-10-28 | `meloaep_` | [https://www.tiktok.com/@meloaep_/video/7701171449205247238](https://www.tiktok.com/@meloaep_/video/7701171449205247238) | `music_probability_ambiguous` | ذخیره نشده |
| 2026-10-29 | `meloaep_` | [https://www.tiktok.com/@meloaep_/video/7701511937586187538](https://www.tiktok.com/@meloaep_/video/7701511937586187538) | `music_probability_ambiguous` | ذخیره نشده |
| 2026-10-31 | `poppytv_ae` | [https://www.tiktok.com/@poppytv_ae/video/7702518409187118356](https://www.tiktok.com/@poppytv_ae/video/7702518409187118356) | `music_probability_ambiguous` | ذخیره نشده |
| 2026-11-03 | `meloaep_` | [https://www.tiktok.com/@meloaep_/video/7703481766677368084](https://www.tiktok.com/@meloaep_/video/7703481766677368084) | `music_probability_ambiguous` | ذخیره نشده |

## C) خطای Cobalt HTTP 400 — ۳ مورد

**ارزیابی دستی:** requeue موردی ممکن است، پس از اطمینان از عمومی و در دسترس بودن ویدئو و بررسی سازگاری/علت پاسخ 400 از Cobalt. این خطا در فهرست retry خودکار نیست؛ مالک از requeue گروهی بدون بررسی خودداری کند.

| تاریخ شکست (UTC) | Handle | لینک عمومی TikTok | کد خطا | music_probability |
|---|---|---|---|---|
| 2026-10-20 | `menezxl9` | [https://www.tiktok.com/@menezxl9/video/7695483963384679700](https://www.tiktok.com/@menezxl9/video/7695483963384679700) | `pipeline_error_cobalt_http_400` | ذخیره نشده |
| 2026-10-26 | `menezxl9` | [https://www.tiktok.com/@menezxl9/video/7700325125488495893](https://www.tiktok.com/@menezxl9/video/7700325125488495893) | `pipeline_error_cobalt_http_400` | ذخیره نشده |
| 2026-11-01 | `poppytv_ae` | [https://www.tiktok.com/@poppytv_ae/video/7702848466682137908](https://www.tiktok.com/@poppytv_ae/video/7702848466682137908) | `pipeline_error_cobalt_http_400` | ذخیره نشده |

## D) سایر خطاها — ۲ مورد

**ارزیابی دستی:** یکجا requeue نشوند. `music_not_detected` فقط در صورت تأیید دستی وجود موسیقی قابل بررسی مجدد است؛ علت `staging_failed` از رکورد موجود روشن نیست و باید ابتدا گزارش job و وضعیت staging همان مورد بررسی شود.

| تاریخ شکست (UTC) | Handle | لینک عمومی TikTok | کد خطا | music_probability |
|---|---|---|---|---|
| 2026-10-14 | `meloaep_` | [https://www.tiktok.com/@meloaep_/video/7693633790253567253](https://www.tiktok.com/@meloaep_/video/7693633790253567253) | `staging_failed` | ذخیره نشده |
| 2026-10-25 | `meloaep_` | [https://www.tiktok.com/@meloaep_/video/7699973252977921288](https://www.tiktok.com/@meloaep_/video/7699973252977921288) | `music_not_detected` | ذخیره نشده |

---

**وضعیت اقدام:** این سند فقط فهرست بررسی دستی است. هیچ retry، requeue، delete، تغییر شمارنده یا اقدام صف انجام نشده است.
