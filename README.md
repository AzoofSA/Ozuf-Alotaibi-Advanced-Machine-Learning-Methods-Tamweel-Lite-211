Tamweel Lite | مشروع التنبؤ بالتعثر
SDA-DSC-211 — Advanced Machine Learning Methods | أساليب تعلم الآلة المتقدمة
SDAIA Academy | أكاديمية سدايا
نبذة عن المشروع | Project overview
مشروع تعلم آلة يستخدم بيانات تمويل اصطناعية لتقدير احتمال حدوث تعثر خلال 90 يومًا بعد تقديم الطلب. يشمل تجهيز البيانات، ومقارنة الخوارزميات، والتحقق الزمني، وضبط المعاملات، والتعامل مع عدم توازن الفئات، وتفسير النماذج ومعايرتها، واختيار النموذج النهائي وسياسة المراجعة.
الهدف هو بناء نموذج قابل لإعادة التشغيل يوازن بين جودة التنبؤ، والخسارة الناتجة عن الأخطاء، وسعة المراجعة المحدودة. الإشارة إلى المراجعة استخدام تعليمي، وليست موافقة أو رفضًا لتمويل حقيقي.
This machine learning project estimates the probability of synthetic default within 90 days after a financing application. It covers preprocessing, model comparison, temporal validation, hyperparameter tuning, class imbalance, interpretation, calibration, ensemble assessment and final prediction.
The objective combines predictive quality, error cost and limited review capacity. All data are fictional, and review flags are intended for educational use only.
البيانات | Data
Dataset	Description / الوصف
tamweel_train.csv	10,000 طلب للتدريب، مع 22 خاصية تنبؤية والهدف / 10,000 training applications with 22 predictors and the target.
tamweel_dirty.csv	نسخة لتجربة كشف تسرب المعلومات / A training variant used to investigate information leakage.
tamweel_challenge.csv	2,500 طلب من فترة لاحقة، دون قيم الهدف / 2,500 later applications without target labels.


الهدف هو default_within_90d، وتبلغ نسبة الفئة الموجبة في التدريب 7.89%، أي 789 طلبًا. يعني الهدف حدوث تعثر خلال 90 يومًا بعد الطلب، ولا يعني وصول التأخر في السداد إلى 90 يومًا.
The target is default_within_90d, with 789 positive cases out of 10,000 (7.89%). It represents default occurring within the 90-day horizon after application.
تجهيز البيانات والتحقق | Preprocessing and validation
- استبعاد المعرفات والتاريخ والهدف من مدخلات النموذج، واستبعاد days_past_due_60 وcollection_calls لأنهما يحتويان معلومات لاحقة للقرار.
- تعلم تعويض القيم الناقصة داخل أجزاء التدريب، لتجنب تسرب المعلومات.
- استخدام تحقق زمني مع فصل العملاء والتأكد من اكتمال أفق الهدف البالغ 90 يومًا قبل فترة التحقق.
- استخدام تنبؤات خارج الطية OOF للمقارنة وتطوير سياسة القرار.
- تخصيص بيانات التحدي للتنبؤ النهائي، دون استخدامها لضبط المعاملات أو اختيار العتبة.
IDs, dates, the target and post-outcome features are excluded from predictors. Missing-value imputation is fitted within training partitions. Forward validation separates customers and respects label maturity. OOF predictions support model comparison and policy development; challenge data are reserved for final inference.
الخوارزميات | Algorithms
استخدم المشروع Logistic Regression وXGBoost وLightGBM، مع بحث محدود باستخدام Optuna. كما قارن النماذج المنفردة بطرق Equal Averaging وWeighted Averaging وStacking، لتحديد ما إذا كان التجميع يقدم فائدة تبرر زيادة التعقيد.
The project evaluates Logistic Regression, XGBoost and LightGBM, uses bounded Optuna tuning, and compares single models with equal averaging, weighted averaging and stacking.
نتائج اختيار النموذج | Model selection results
الجدول التالي يلخص المقارنة النهائية على التحقق الزمني. يعرض متوسط AP بين الطيات وتباينه الوصفي، بالإضافة إلى متوسط Brier وECE للاحتمالات الخام.
The table summarizes final forward-validation results. AP measures ranking quality; fold SD describes variability across folds. Brier and ECE summarize raw probability quality.
Model	Mean AP ↑	Fold SD	Mean Brier ↓	Mean ECE ↓
LightGBM	0.34549	0.04348	0.06608	0.02311
XGBoost	0.35263	0.02904	0.06566	0.02276
Logistic Regression	0.39166	0.02981	0.06327	0.01882
Equal Averaging	0.37170	0.03258	0.06435	0.02038
Weighted Averaging	0.38942	0.02906	0.06332	0.01772
Stacking	0.38314	0.02949	0.06603	0.03106


اختير Logistic Regression نموذجًا نهائيًا؛ حقق أعلى متوسط AP، ولم تجتز طرق التجميع بوابة الجدوى. القرار النهائي هو KEEP SINGLE.
Logistic Regression was selected as the final model. It achieved the highest mean AP, and no ensemble passed the Worth-It Gate. The final decision is KEEP SINGLE.
سياسة القرار | Decision policy
تعتمد السياسة على الخسارة التعليمية 10 × FN + FP، مع سقف مراجعة 12%. اختيرت العتبة من تنبؤات التطوير OOF، ثم جُمّدت السياسة قبل تطبيقها على بيانات التحدي.
The policy uses educational loss 10 × FN + FP and a 12% review-capacity limit. The threshold is selected from development OOF predictions and frozen before challenge inference.
Final development OOF result	Value
Applications / عدد الطلبات	2,155
TP / FP / FN / TN	84 / 161 / 95 / 1,815
Recall	0.46927
Precision	0.34286
Review flags / إشارات المراجعة	245 (11.369%)
Educational loss / الخسارة التعليمية	1,111


العتبة بعد التحويل باستخدام sigmoid هي 0.12225843144286948. تبدأ السياسة بالقاعدة probability >= threshold، ثم تطبق سقف السعة على الدفعة مع الحفاظ على كتل الدرجات المتعادلة. لذلك لا تعتمد قيمة decision على العتبة وحدها.
في بيانات التحدي، تجاوز العتبة 330 طلبًا. بعد تطبيق السعة، أصبحت إشارات المراجعة 300 من أصل 2,500 طلب (12%).
The sigmoid-transformed threshold is 0.12225843144286948. Threshold eligibility is followed by the frozen batch-capacity rule, preserving tied-score groups. On the challenge batch, 330 applications were eligible and 300 (12%) were ultimately flagged.
التفسير والمعايرة | Interpretation and calibration
استخدمت تجربة التفسير نموذج LightGBM موزونًا مع أهمية التبديل وSHAP. برزت خصائص مثل bureau_score وdti. تعبر قيم SHAP في هذه التجربة عن مساهمات بوحدة log-odds؛ وهي تفسير لسلوك النموذج وليست دليلًا سببيًا.
في تقييم LightGBM على 1,733 طلبًا، تحسن Brier بعد sigmoid من 0.113027 إلى 0.067112، وECE من 0.146871 إلى 0.022486. هذه النتائج تخص نموذج التفسير، ولا تنسب إلى Logistic النهائي.
بالنسبة إلى Logistic النهائي، أظهرت القياسات على عينة تعلم المعاير تدهور Brier من 0.076473 إلى 0.078058، وECE من 0.021121 إلى 0.034871. هذه قياسات تشخيصية على عينة التعلم وليست تقييمًا مستقلًا، ولا تثبت تحسن المعايرة.
The interpretation experiment uses weighted LightGBM with permutation importance and SHAP in log-odds units. Its evaluation Brier improved from 0.113027 to 0.067112, and ECE from 0.146871 to 0.022486 after sigmoid calibration.
These explanations and improvements do not describe the final Logistic model. For final Logistic, calibration-fit diagnostics worsened: Brier 0.076473 → 0.078058, and ECE 0.021121 → 0.034871. Independent calibration improvement is not claimed.
الملفات والمخرجات | Files and outputs
File	Purpose / الغرض
README.md	وصف المشروع والمنهج والنتائج / Project description, method and results.
[Copy_of_Tamweel_.ipynb](Copy_of_Tamweel_.ipynb)	كود المشروع الشامل والنتائج المحفوظة / Combined project code and saved outputs.


ينشئ تشغيل الدفتر النموذج النهائي والتقارير والرسوم وملفات JSON وملف التنبؤ submission.csv وحزمة المشروع داخل /content/tamweel. الملفات الناتجة أثناء التشغيل منفصلة عن المخرجات المعروضة المحفوظة داخل الدفتر.
The notebook generates the final model, reports, figures, JSON files, submission.csv and a project bundle under /content/tamweel. Runtime artifacts are separate from the displayed outputs saved in the notebook.
عقد ملف التنبؤ / Prediction columns:
application_id, probability, decision
يحفظ الملف معرفات الطلبات، واحتمالات بين 0 و1، وقرارات ثنائية وفق السياسة المجمدة.
The prediction file preserves application IDs, probabilities in [0,1] and binary decisions from the frozen policy.
طريقة التشغيل | How to run
1. افتح Copy_of_Tamweel_.ipynb في Google Colab.
2. استخدم جلسة CPU مع اتصال بالإنترنت لتنزيل المصادر والبيانات.
3. شغّل خلايا الدفتر بالترتيب من الإعداد إلى التصدير.
4. راجع النتائج واحفظ ملفات المخرجات التي ينشئها التشغيل.
Open the notebook in Google Colab with a CPU runtime and internet access. Run cells in order from setup through export, inspect the outputs and save the generated artifacts.
بيئة التشغيل المسجلة | Recorded environment
Component	Version / Setting
Python	3.13.16
NumPy / pandas	2.1.3 / 2.2.3
scikit-learn	1.6.1
XGBoost / LightGBM	3.4.1 / 4.6.0
SHAP / Optuna	0.52.0 / 4.5.0
Seed / Threads	211 / 2
Compute / Mode	CPU / FAST


حدود المشروع | Limitations
- البيانات اصطناعية؛ لا تعمم النتائج على عملاء حقيقيين.
- نتائج اختيار النموذج والعتبة أدلة تطوير، وليست اختبارًا نهائيًا مستقلًا.
- التحقق النهائي يغطي ثلاث فترات؛ انحراف الطيات ليس فترة ثقة.
- لا تتوفر أهداف بيانات التحدي، لذلك لا توجد مقاييس أداء فعلية للتحدي.
- بلغ فرق معدل الإنذار الخاطئ بين المناطق في OOF النهائي نحو 3.98 نقاط مئوية؛ وهو تشخيص على بيانات اصطناعية ولا يثبت العدالة.
- تحتاج المعايرة والسعة والأداء إلى متابعة عند تغير البيانات وبعد اكتمال رصد النتائج.
The data are synthetic. Model and threshold selection results are development evidence, not an untouched final test. Validation covers three periods, and fold SD is not a confidence interval. Challenge labels are unavailable, so challenge performance cannot be measured. The observed regional OOF false-positive-rate gap was approximately 3.98 percentage points, which does not establish fairness. Calibration, capacity and performance require monitoring as data change and outcomes mature.
بطاقة النموذج | Model Card
Purpose | الغرض
تقدير احتمال التعثر الاصطناعي خلال 90 يومًا، وتحويل الاحتمال إلى إشارة مراجعة وفق خسارة الأخطاء وسعة التشغيل. يدعم الاختيار النهائي متوسط AP البالغ 0.39166، بينما توضح خسارة OOF البالغة 1,111 المفاضلة عند تطبيق سياسة القرار.
Estimate synthetic 90-day default risk and produce capacity-aware review flags. Mean AP 0.39166 supports model selection; OOF policy loss 1,111 describes the observed decision tradeoff.
Intended use and non-use | الاستخدام المقصود وغير المقصود
الاستخدام المقصود هو تجربة تعليمية قابلة لإعادة التشغيل على بيانات المشروع الاصطناعية. لا يستخدم النموذج لاتخاذ قرارات تمويل حقيقية أو تقييم أشخاص أو تحديد أسعار وحدود ائتمانية. decision=1 تعني إشارة مراجعة، ولا تعني رفض الطلب.
Intended for reproducible educational experiments on the supplied synthetic data. It is not intended for real lending, personal assessment, pricing or credit limits. A positive decision is a review flag, not a rejection.
Synthetic data and target | البيانات الاصطناعية والهدف
التدريب يضم 10,000 طلب و789 حالة موجبة (7.89%)، والتحدي يضم 2,500 طلب بلا هدف. جميع السجلات خيالية. الهدف default_within_90d يرصد حدثًا خلال 90 يومًا بعد الطلب. التدريب يغطي 2022–2024، والتحدي من يناير إلى سبتمبر 2025، وتاريخ اكتمال الرصد هو 2026-01-01.
Training contains 10,000 applications, including 789 positives (7.89%). The challenge contains 2,500 unlabeled applications. Records are fictional; the target is default within 90 days after application. Training spans 2022–2024, challenge applications span January–September 2025, and observation completion is 2026-01-01.
Features and timing | الخصائص وتوقيت توفرها
تستخدم المدخلات خصائص متاحة وقت الطلب، مثل الدخل ومبلغ التمويل ومدته ودرجة السجل الائتماني وDTI. تستبعد application_id وcustomer_id وapplication_date والهدف. يستخدم التاريخ ومعرف العميل للتحقق والتتبع فقط. تستبعد خصائص ما بعد النتيجة days_past_due_60 وcollection_calls. تتعلم معالجة النقص من جزء التدريب داخل كل طية.
Predictors must be available at application time, including income, loan amount, tenor, bureau score and DTI. IDs, dates, the target and post-outcome fields are excluded from predictors. Dates and customer IDs support validation and traceability; imputation is learned within each training fold.
Validation and leakage controls | التحقق ومنع التسرب
يراعي التحقق ترتيب الزمن وفصل العملاء ونضج هدف الـ90 يومًا. التجربة النهائية تستخدم 6,576 طلبًا في مجموعة التدريب والاختيار، ودور معايرة منفصلًا يضم 836 طلبًا و78 موجبًا، مع استبعاد 2,588 صفًا وفق قواعد الأدوار. تغطي OOF النهائية 2,155 طلبًا عبر ثلاث فترات؛ تتعلم أوزان التجميع والتكديس داخل الأدوار الداخلية، وتستخدم OOF الخارجية للمقارنة واختيار العتبة. لذلك تظل المقاييس دليل تطوير، ولا تمثل اختبارًا مستقلًا بعد الاختيار. لا تستخدم بيانات التحدي لاختيار النموذج أو السياسة.
Validation respects time, customer separation and 90-day label maturity. Final roles include 6,576 fit/selection applications, 836 calibration applications with 78 positives, and 2,588 excluded rows. Final forward OOF covers 2,155 applications across three periods. Ensemble learning occurs in inner roles; outer OOF supports model and threshold selection. Selected metrics remain development evidence, and challenge data do not inform model or policy selection.
Models and selection | النماذج والاختيار
قارنت Logistic Regression وXGBoost وLightGBM والمتوسط البسيط والموزون والتكديس. اختير Logistic Regression بقرار KEEP SINGLE؛ متوسط AP 0.39166 أعلى من المتوسط الموزون 0.38942 والتكديس 0.38314، ولم يجتز أي تجميع بوابة الجدوى. يدعم ذلك النموذج الأبسط دون ادعاء تفوق مضمون على بيانات مستقبلية.
Logistic Regression was retained over boosting and ensemble candidates: mean AP 0.39166, versus weighted averaging 0.38942 and stacking 0.38314. No ensemble passed the Worth-It Gate. The KEEP SINGLE decision favors the simpler supported candidate without guaranteeing future superiority.
Metrics and calibration | المقاييس والمعايرة
في مقارنة Logistic النهائية، بلغ الانحراف بين الطيات 0.02981 ومتوسط Brier الخام 0.06327 وECE الخام 0.01882. عند السياسة المختارة، كان Recall 0.46927 وPrecision 0.34286. تعلمت sigmoid من دور المعايرة المنفصل، لكن قياسها على عينة تعلمها تشخيص فقط: تدهور Brier 0.076473 → 0.078058 وECE 0.021121 → 0.034871 وlog-loss 0.266489 → 0.277296. لا أدعي تحسن معايرة النموذج النهائي. تفسير SHAP ونتائج المعايرة السابقة تخص LightGBM الموزون، ولا أنسبها إلى Logistic.
Final Logistic comparison reports fold SD 0.02981, raw mean Brier 0.06327 and raw mean ECE 0.01882. Selected-policy Recall is 0.46927 and Precision 0.34286. Sigmoid is learned in the separate calibration role; measurements on its own fit sample are diagnostic only. Brier, ECE and log loss worsened to 0.078058, 0.034871 and 0.277296, respectively. Final calibration improvement is not claimed. Earlier LightGBM explanations and calibration results do not explain final Logistic.
Decision policy and capacity | سياسة القرار والسعة
الخسارة 10FN + FP بوحدات تعليمية. العتبة المنقولة 0.12225843144286948 وقاعدة >= تبدأان أهلية المراجعة، ثم تقيد سياسة الدفعة العدد بسقف 12% بعد التقريب لأسفل، مع حفظ كتل التعادل وعدم ملء السعة دون أهلية. في OOF كانت الإشارات 245/2,155 والخسارة 1,111؛ وعلى دفعة التحدي أصبحت 300/2,500 بعد تقليل 330 طلبًا مؤهلًا. تطبق السياسة على الدفعة الكاملة، ولا تقسم الدفعة اعتباطيًا لتطبيق السقف.
Educational loss is 10FN + FP. The transformed threshold 0.12225843144286948 uses >=, followed by a frozen 12% batch cap rounded down. Tied scores remain grouped and capacity is not filled without eligibility. Development OOF flags 245/2,155 with loss 1,111; challenge flags 300/2,500 after capping 330 eligible applications. Policy must operate on the complete batch.
Fairness and limitations | الفروق والحدود
كانت معدلات الإنذار الخاطئ الإقليمية في OOF النهائي نحو 7.98% للوسطى، و10.29% للغربية، و6.31% للشرقية، و8.10% للأخرى. أعداد الحالات السليمة المقابلة 489 و486 و507 و494؛ أكبر فجوة نحو 3.98 نقاط مئوية. هذه فروق مرصودة على بيانات اصطناعية ولا تثبت العدالة. محدودية الفترات، وإعادة استخدام دليل التطوير للاختيار، وغياب أهداف التحدي تحد من الاستنتاج. المتابعة تشمل نقص البيانات وتغير التوزيعات والسعة، ثم الأداء والمعايرة والفروق بعد نضج الهدف؛ تعديل النموذج أو السياسة يتطلب تحققًا جديدًا.
Regional OOF false-positive rates are approximately 7.98%, 10.29%, 6.31% and 8.10%, with negative denominators 489, 486, 507 and 494. The largest gap is approximately 3.98 percentage points, not fairness certification. Limited periods, development reuse and unavailable challenge labels restrict conclusions. Monitor missingness, distributions and capacity, then performance, calibration and regional gaps after label maturity. Changes require renewed validation.
Reproducibility and ownership | إعادة الإنتاج ومصدر العمل
يوثق الدفتر الكود والمخرجات المحفوظة، ويستخدم seed 211 وCPU وخيطين وإصدارات البيئة المذكورة أعلاه. تنزل خلايا الإعداد مصادر مثبتة وتتحقق من البصمات؛ مراجعة مصادر البيانات المسجلة هي fe0c0204e6076a7ac2139b7336485a097343fb8a وليست SHA خاصًا بهذا المستودع. يتطلب إعادة التشغيل اتصالًا بالمصادر وبيئة متوافقة، ولا تكفي صور النتائج لإعادة بناء ملفات النموذج. البيانات وأدوات الدعم مصدرها قالب المشروع، ولا تنسب الأدوات الجاهزة إلى كود أصلي للمشروع. الأرقام هنا من مخرجات الدفتر المحفوظة، واستُخدمت مساعدة الذكاء الاصطناعي في صياغة التوثيق. تمثل البطاقة النموذج النهائي وسياسة التشغيل الموصوفين، وأي تغيير عليهما يستلزم تحديث الأرقام والبطاقة.
The notebook records code and saved outputs, using seed 211, CPU, two threads and the documented environment. Setup downloads pinned sources and checks hashes. Recorded data-source revision fe0c0204e6076a7ac2139b7336485a097343fb8a identifies the source revision, not this repository's commit. Reproduction requires available sources and compatible dependencies; displayed outputs alone do not reconstruct model files. Data and support utilities come from the linked project template and are not claimed as original project code. Reported numbers come from saved notebook outputs; AI assisted documentation drafting. This card describes the final model and policy and must be updated if either changes.
