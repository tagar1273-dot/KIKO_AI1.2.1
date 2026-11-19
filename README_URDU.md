# KIKO AI — Android پروجیکٹ

یہ Android Studio/Gradle پروجیکٹ ہے۔ اس میں:
- اردو UI
- ٹائپ کے ذریعے Gemini AI چیٹ
- Android Speech Recognizer کے ذریعے آواز سے سوال
- Android Text-to-Speech کے ذریعے جواب سنانا
- فون پر Gemini API key محفوظ کرنے کی بنیادی سہولت

## APK بنانے کا آسان طریقہ (GitHub Actions)

1. GitHub پر نیا repository بنائیں، مثلاً `KIKO-AI`.
2. اس ZIP کو extract کریں اور تمام فائلیں repository میں upload کریں ( `.github` فولڈر سمیت)۔
3. GitHub میں **Actions** کھولیں۔
4. **Build KIKO AI APK** workflow منتخب کرکے **Run workflow** دبائیں۔
5. Build مکمل ہونے کے بعد workflow run کے نیچے **Artifacts** میں `KIKO-AI-debug-APK` ڈاؤن لوڈ کریں۔
6. ZIP کھولیں؛ اس کے اندر `app-debug.apk` ہوگا۔ Android پر انسٹال کرتے وقت فون کی اجازت مانگی جا سکتی ہے۔

## Gemini API key

Google AI Studio سے اپنی Gemini API key بنائیں۔ ایپ کھول کر **Gemini API Key سیٹ کریں** دبائیں اور key درج کریں۔
API key خفیہ رکھیں۔ یہ سادہ client-side نمونہ ہے؛ عوامی سطح پر تقسیم ہونے والی ایپ کے لیے API key کو محفوظ backend/proxy میں رکھنا بہتر ہے، کیونکہ موبائل ایپ کے اندر رکھی key نکالی جا سکتی ہے۔ Google کی موجودہ API شرائط، limits اور billing ضرور دیکھیں۔

## مطابقت اور حدود

- `minSdk 26` یعنی Android 8.0 یا بعد کے Android فونز کا ہدف ہے۔
- Samsung, Motorola, Xiaomi/Redmi, Vivo, Oppo, Realme, Tecno, Infinix وغیرہ پر چلنے کا ہدف ہے، مگر ہر ماڈل پر ضمانت نہیں؛ Android version، Google services، speech recognition اور manufacturer restrictions سے فرق پڑ سکتا ہے۔
- یہ iPhone/iOS ایپ نہیں ہے۔
- آواز کی شناخت کے لیے فون میں speech recognition service اور مناسب زبان کی سہولت درکار ہے۔ اردو TTS ہر فون میں دستیاب نہیں؛ ایسی صورت میں فون کی دستیاب آواز استعمال ہو سکتی ہے۔
- یہ ایپ عام Android intents استعمال کرتی ہے۔ یہ خاموشی سے فون unlock، خفیہ نگرانی، خودکار کال، یا بغیر اجازت apps install نہیں کرتی۔
- اس build میں خودکار update system شامل نہیں۔ اپڈیٹس فی الحال نئی APK بنا کر صارف کی تصدیق سے انسٹال ہوں گی۔
- یہ سورس پروجیکٹ ہے؛ یہاں تیار شدہ، signed production APK موجود نہیں۔ GitHub Actions سے debug APK بنے گی۔ Play Store/production کے لیے release signing، privacy policy، backend key protection اور مکمل device testing ضروری ہے۔

## Version
1.0.0 — ابتدائی KIKO AI Android build


## خودکار اپڈیٹ نظام (شامل کیا گیا)

ایپ میں **KIKO AI اپڈیٹ چیک کریں** بٹن شامل ہے:
1. ایپ HTTPS پر `update.json` پڑھتی ہے۔
2. اگر نیا `versionCode` ہو تو صارف سے ڈاؤن لوڈ کی اجازت مانگتی ہے۔
3. APK ڈاؤن لوڈ ہونے کے بعد `sha256` hash چیک کرتی ہے۔
4. تصدیق کامیاب ہو تو Android کا اپنا installer کھولتی ہے؛ صارف خود Install کی تصدیق کرتا ہے۔

### ایک مرتبہ سرور سیٹ اپ لازمی ہے
- `UpdateManager.kt` میں `MANIFEST_URL` کو اپنے GitHub raw `update.json` کے URL سے بدلیں۔
- `update.json.example` کو `update.json` نام دیں اور ہر ریلیز پر `versionCode`, `versionName`, `apkUrl`, `sha256` اپڈیٹ کریں۔
- APK کا SHA-256 نکال کر `sha256` میں ڈالیں۔ غلط hash ہونے پر ایپ انسٹال نہیں کرے گی۔
- نئی APK ہمیشہ اسی Android signing key سے sign ہونی چاہیے جس سے پہلے والی APK sign کی گئی تھی؛ ورنہ Android اپڈیٹ قبول نہیں کرے گا۔ اپنی keystore اور password محفوظ رکھیں۔

### اہم حفاظتی حدود
- Android عام ایپس کو خاموشی سے خود انسٹال کرنے نہیں دیتا۔ صارف کی اجازت اور Android installer confirmation ضروری ہے۔
- یہ updater ڈاؤن لوڈ اور hash verification کرتا ہے؛ rollback خودکار طور پر نہیں کرتا۔ پرانی APK دستی طور پر دوبارہ انسٹال کرنے کے لیے compatible signed release رکھیں۔
- یہ پروجیکٹ debug APK بنا سکتا ہے۔ حقیقی public release کے لیے release signing اور مستقل محفوظ keystore ضروری ہے۔
- GitHub `OWNER/REPO` placeholder بدلنا لازمی ہے؛ جب تک اپنا manifest host نہ کریں، اپڈیٹ چیک کام نہیں کرے گا۔
