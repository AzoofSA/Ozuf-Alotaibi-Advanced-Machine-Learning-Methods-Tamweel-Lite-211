# استعد للدورة في 30–40 دقيقة

[افتح دفتر الاستعداد في Colab](https://colab.research.google.com/github/almiyead-rgb/sda-dsc-211-student-template/blob/main/notebooks/00_readiness_check.ipynb) · [اقرأ الدفتر في GitHub](notebooks/00_readiness_check.ipynb)

## 1. جهز حساباتك ونسخة مشروعك — 5 دقائق

سجل الدخول إلى Google وGitHub. افتح [قالب المشروع](https://github.com/almiyead-rgb/sda-dsc-211-student-template) واختر **Use this template → Create a new repository**. اختر اسمًا مثل `tamweel-project-01`، واحتفظ باسم الحساب والمستودع لتتأكد من وجهة الحفظ. لا تحتاج إلى بطاقة دفع أو اشتراك.

## 2. افتح دفتر الاستعداد وشغله — 10 دقائق

1. افتح رابط Colab أعلاه. عند عرض تحذير أن الدفتر مصدره GitHub، راجع اسم المستودع والشفرة قبل تشغيله.
2. من **Runtime → Change runtime type** اختر **CPU**، أو **None** إذا ظهر بهذا الاسم.
3. اختر **Runtime → Run all**. انتظر تثبيت الحزم المطلوبة وفحص البيانات.
4. تحقق من ظهور **Environment ready | البيئة جاهزة** و**READY** دون أخطاء.

لا تمنح الدفتر الوصول إلى Drive أو الأسرار. يحمل الملفات العامة من GitHub، ويتحقق من بصماتها. الخلية الأولى تحدد نسخة الملفات المطلوبة؛ لا تعدلها أثناء الاستعداد.

## 3. راجع المفاهيم الستة — 10 دقائق

اقرأ الفرق بين التصنيف والاحتمال والتقسيم ومصفوفة الالتباس. أجب عن الأسئلة الستة داخل قائمة `answers` في الدفتر، ثم أعد تشغيل خلية المراجعة وما بعدها. هذه مراجعة ذاتية **لا تدخل في الدرجة**؛ ترك الإجابات فارغة لا يعني أنك أجبت عنها بشكل صحيح.

## 4. احفظ الدفتر والمخرجات — 5–10 دقائق

من **File → Download → Download .ipynb** نزّل نسخة الدفتر. في GitHub افتح مجلد `notebooks/` في مستودعك ثم **Add file → Upload files** وارفع النسخة.

في الشريط الجانبي لـColab افتح **Files → tamweel → artifacts**. نزّل `readiness_artifacts.zip` وافك ضغطه على جهازك. ارفع ملفات JSON الأربعة إلى مجلد `artifacts/` في مستودعك:

- `environment.json`: الإصدارات ونسخة ملفات الدورة.
- `data_check.json`: أحجام البيانات ونتيجة فحصها.
- `runtime_checks.json`: توافق الأدوات على عينة تجريبية صغيرة.
- `readiness_report.json`: ملخص جاهزيتك ومدة التشغيل.

اكتب وصفًا واضحًا للحفظ مثل `Complete readiness check`. افتح الملف من GitHub بعد الرفع لتتأكد أنه في مستودعك. إذا كان **File → Save a copy in GitHub** متاحًا مسبقًا في حسابك، يمكنك استخدامه بدل تنزيل الدفتر؛ راجع Owner وRepository وFile path قبل الحفظ. مسار التنزيل والرفع لا يتطلب إدخال token.

## إذا لم يكتمل التشغيل

| الرسالة أو الحالة | ما تفعله |
|---|---|
| فشل تنزيل مؤقت | أعد خلية الإعداد؛ لا تستبدل البيانات بملف مجهول. |
| Checksum mismatch | أعد فتح الدفتر المعتمد واسترجع الملف الأصلي؛ لا تتجاهل الفحص. |
| إصدار مستورد مختلف | اختر Restart session ثم شغّل الإعداد أولًا. |
| انقطاع الشبكة مع ملفات محلية سليمة | يعيد الإعداد استخدام الملفات المطابقة. |
| لا تتوفر ملفات محلية والشبكة مستمرة بالفشل | من GitHub اختر Code → Download ZIP. افك المشروع وارفع `scripts/` و`data/` وملفي المتطلبات إلى `/content/tamweel/` مع الحفاظ على أسماء المجلدات؛ ثم أعد الإعداد. تحتاج الشبكة لتثبيت أي حزمة غير موجودة. |
| حد الاستخدام المجاني | احفظ عملك وأعد المحاولة لاحقًا. لا تشترِ GPU أو وحدات حوسبة. |

النسخة التي تُنسخ من جهازك تُفحص بالبصمات نفسها؛ ليست مخرجات نموذج بديلة أو تجاوزًا للتقييم.

**Ready when:** you see `Environment ready` and `READY`, review the six ungraded questions, and save the notebook plus four JSON files. Use the free CPU runtime. Keep your own copy; Colab sessions are temporary.

[سياسات توفر الموارد في Colab](https://research.google.com/colaboratory/faq.html) · [دليل البيانات](data/DATA_GUIDE.md)
