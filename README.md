# Tamweel — Default Risk and Review Prioritization
# تمويل — توقع التعثر وترتيب أولوية المراجعة

[@SDAIAAcademy](https://github.com/SDAIAAcademy) — SDAIA Academy / أكاديمية سدايا

An end-to-end machine-learning capstone for predicting synthetic 90-day default, comparing models, controlling leakage, interpreting predictions, calibrating probabilities, and applying an auditable review policy under a 12% capacity limit.

مشروع متكامل في تعلم الآلة لتوقع التعثر خلال 90 يومًا باستخدام بيانات اصطناعية، ومقارنة النماذج، ومنع تسرب المعلومات، وتفسير التنبؤات، ومعايرة الاحتمالات، وتطبيق سياسة مراجعة قابلة للتدقيق بسقف سعة 12%.

| Item / البند | Details / التفاصيل |
|---|---|
| Project type / نوع المشروع | Individual capstone / مشروع نهائي فردي |
| Course / الدورة | SDA-DSC-211 — Advanced Machine Learning Methods / أساليب تعلم الآلة المتقدمة |
| Final model / النموذج النهائي | Logistic Regression / الانحدار اللوجستي |
| Final decision / القرار النهائي | KEEP SINGLE / الإبقاء على نموذج واحد |
| Execution / التشغيل | Google Colab CPU, FAST mode, seed 211 / معالج CPU على Colab، الوضع السريع، البذرة 211 |

[View the executed notebook / عرض دفتر المشروع المنفذ](./Copy_of_Tamweel_%20%281%29.ipynb)

Upload this README and `Copy_of_Tamweel_ (1).ipynb` to the same repository folder so the link works. If the notebook is renamed, update the link.

ارفع هذا الملف ودفتر `Copy_of_Tamweel_ (1).ipynb` في المجلد نفسه داخل المستودع ليعمل الرابط. إذا تغير اسم الدفتر، حدّث الرابط.

## Project overview / نظرة عامة على المشروع
Tamweel studies whether information available at application time can predict `default_within_90d` and support simulated review prioritization. A review flag is an educational priority indicator, not automatic rejection.

يدرس المشروع إمكانية استخدام المعلومات المتاحة وقت تقديم طلب التمويل لتوقع `default_within_90d` وترتيب أولوية المراجعة بصورة تعليمية. إشارة المراجعة تعني رفع الأولوية، ولا تعني رفض الطلب تلقائيًا.

The five stages are combined in one executed notebook: baseline comparison; honest validation and tuning; cost-sensitive decisions; interpretation and calibration; and final integration. Following the instructor's updated hand-in instructions, submission consists of the integrated notebook and README rather than separate daily submissions.

جُمعت المراحل الخمس في دفتر منفذ واحد: مقارنة نماذج الأساس؛ التحقق الصحيح والضبط؛ القرارات الحساسة للتكلفة؛ التفسير والمعايرة؛ والتكامل النهائي. وبحسب تحديث تعليمات المدرب، يكون التسليم للدفتر المتكامل وREADME مرة واحدة، بدل تسليم كل لاب على حدة.

The project uses synthetic course data and Logistic Regression, XGBoost, and LightGBM. Reported results come from the notebook's captured execution; they are not independent Challenge scores.

يستخدم المشروع بيانات الدورة الاصطناعية ونماذج Logistic Regression وXGBoost وLightGBM. الأرقام الواردة مأخوذة من مخرجات التنفيذ المحفوظة في الدفتر، وليست نتائج تقييم مستقلة لبيانات Challenge.



### System architecture / معمارية النظام

The integrated architecture separates model development from final inference. Forward OOF supports comparison and policy selection; a reserved calibration set learns the sigmoid mapping. The frozen exported components then predict the unlabeled Challenge batch, followed by thresholding and the full-batch capacity cap.

تفصل المعمارية المتكاملة بين تطوير النموذج والاستدلال النهائي. تدعم تنبؤات OOF الأمامية مقارنة النماذج واختيار السياسة، وتتعلم عينة معايرة محجوزة تحويل Sigmoid. بعد تثبيت المكونات المصدرة، تُنتج احتمالات دفعة Challenge غير المعلّمة، ثم تطبق العتبة وسقف سعة الدفعة كاملة.

| Layer / الطبقة | Role / الدور | Output / المخرج |
|---|---|---|
| Data and validation / البيانات والتحقق | Application-time features; time/customer separation / خصائص وقت الطلب وفصل الوقت والعملاء | Audited folds and roles / طيات وأدوار مدققة |
| Model development / تطوير النموذج | Training-only preprocessing; nested OOF / معالجة من التدريب فقط وOOF متداخل | Candidate scores and comparison / درجات البدائل ومقارنتها |
| Selection and calibration / الاختيار والمعايرة | KEEP SINGLE; reserved sigmoid fitting / اختيار نموذج واحد ومعايرة محجوزة | Frozen Logistic and calibrator / Logistic ومعاير مجمدان |
| Decision policy / سياسة القرار | OOF loss/capacity threshold; transported mapping / عتبة تكلفة وسعة على OOF ونقلها عبر المعايرة | Threshold and batch cap / عتبة وسقف للدفعة |
| Inference and audit / الاستدلال والتدقيق | Full-batch probabilities, flags, and replay / احتمالات الدفعة وإشاراتها وإعادة إنتاجها | Submission and provenance / ملف التنبؤات ومصدر التنفيذ |

## Key results / النتائج الرئيسية
### Final model and ensemble comparison / مقارنة النموذج النهائي والتجميع

Day 5 compared six candidates across three forward outer OOF periods: 2023Q1, 2023Q3, and 2024Q1. The comparison contains 2,155 OOF rows; warm-up rows do not have outer OOF predictions.

قارن اليوم الخامس ستة بدائل عبر ثلاث فترات تحقق خارجية أمامية: 2023Q1 و2023Q3 و2024Q1. شملت المقارنة 2,155 صفًا بتنبؤات OOF، بينما لا تملك صفوف التهيئة تنبؤات OOF خارجية.

| Candidate / البديل | Mean AP / متوسط AP | Fold SD / انحراف الطيات | Mean Brier / متوسط Brier | Mean ECE / متوسط ECE | AP difference vs. best single / فرق AP عن أفضل نموذج فردي |
|---|---:|---:|---:|---:|---:|
| LightGBM | 0.34549 | 0.04348 | 0.06608 | 0.02311 | -0.04617 |
| XGBoost | 0.35263 | 0.02904 | 0.06566 | 0.02276 | -0.03903 |
| **Logistic** | **0.39166** | **0.02981** | **0.06327** | **0.01882** | **0.00000** |
| Equal / تجميع متساوي الأوزان | 0.37170 | 0.03258 | 0.06435 | 0.02038 | -0.01996 |
| Weighted / تجميع موزون | 0.38942 | 0.02906 | 0.06332 | 0.01772 | -0.00224 |
| Stack / تجميع تكديسي | 0.38314 | 0.02949 | 0.06603 | 0.03106 | -0.00852 |

**Decision: KEEP SINGLE / Logistic.** No ensemble achieved higher mean AP than Logistic or passed the documented ensemble gate. Fold SD is descriptive variation, not a confidence interval or significance test. OOF results informed selection and therefore represent development evidence, not an untouched final test.

**القرار: KEEP SINGLE / Logistic.** لم يحقق أي تجميع متوسط AP أعلى من Logistic، ولم يجتز أي تجميع بوابة الجدوى الموثقة. انحراف الطيات يصف التفاوت ولا يمثل فترة ثقة أو اختبار دلالة. استُخدمت نتائج OOF في الاختيار، ولذلك تُعد أدلة تطوير وليست اختبارًا نهائيًا مستقلًا.

### Decision Card: threshold, loss, and capacity / بطاقة القرار: العتبة والخسارة والسعة

The educational loss is `10 × FN + 1 × FP`. Threshold selection uses OOF predictions and requires capacity compliance in every validation period.

الخسارة التعليمية هي `10 × FN + 1 × FP`؛ تكلفة تفويت حالة تعثر تساوي عشر وحدات، مقابل وحدة لإشارة مراجعة خاطئة. اختيرت العتبة باستخدام OOF مع شرط الالتزام بالسعة في كل فترة تحقق.

| Day 5 OOF metric / مقياس اليوم الخامس على OOF | Result / النتيجة |
|---|---:|
| Raw threshold / العتبة الخام | ≈ 0.168922 |
| Review flags / إشارات المراجعة | 245 / 2,155 |
| Review fraction / نسبة المراجعة | 11.369% |
| Maximum period fraction / أعلى نسبة في فترة | 11.749% |
| TP / FP — صحيح موجب / موجب خاطئ | 84 / 161 |
| FN / TN — سالب خاطئ / صحيح سالب | 95 / 1,815 |
| Recall / الاستدعاء | 0.46927 |
| Precision / دقة الإشارات الموجبة | 0.34286 |
| Simulated loss / الخسارة المحاكاة | 1,111 educational units / وحدة تعليمية |

The threshold trades some recall for capacity compliance. These results describe the OOF rows used for policy selection, and loss units are not currency.

توازن العتبة بين اكتشاف حالات التعثر وحدود السعة، وتفوت بعض الحالات مقابل الالتزام بالسقف. تخص هذه النتائج صفوف OOF المستخدمة لاختيار السياسة، ووحدات الخسارة ليست مبالغ مالية.

### Challenge batch policy / سياسة دفعة Challenge

The raw threshold was transported through sigmoid calibration to `0.12225843144286948`. After probability generation, the policy thresholds the full batch and applies a descending-score capacity cap. Equal-score blocks stay intact; a boundary block is dropped if retaining it would exceed capacity.

نُقلت العتبة الخام عبر معايرة Sigmoid إلى `0.12225843144286948`. بعد توليد الاحتمالات، تُطبق العتبة على الدفعة كاملة ثم سقف السعة وفق ترتيب الاحتمالات تنازليًا. لا تُقسم مجموعات الدرجات المتساوية؛ تُستبعد المجموعة الحدّية إذا كان الاحتفاظ بها يتجاوز السعة.

| Batch metric / مقياس الدفعة | Result / النتيجة |
|---|---:|
| Applications / الطلبات | 2,500 |
| Threshold-eligible / المؤهلة بالعتبة | 330 |
| Capacity / السعة | 300 |
| Final flags / الإشارات النهائية | 300 (12%) |
| Removed by cap / المستبعدة بسبب السعة | 30 |

**Challenge labels are unavailable.** AP, recall, realized loss, calibration quality, and regional FPR cannot be measured for this batch. Capacity compliance alone establishes neither predictive quality nor fairness.

**تسميات Challenge غير متاحة.** لذلك لا يمكن قياس AP أو Recall أو الخسارة الفعلية أو جودة المعايرة أو FPR للمناطق في هذه الدفعة. الالتزام بالسعة وحده لا يثبت جودة التنبؤ أو العدالة.



### Validation evidence / أدلة التحقق

| Control / الضابط | Evidence and interpretation / الدليل والتفسير |
|---|---|
| Teaching baseline / خط الأساس التعليمي | Day 1 uses a random split with 1,226 shared customers; it is not the final validation design. / يستخدم اليوم الأول تقسيمًا عشوائيًا مع 1,226 عميلًا مشتركًا، ولا يمثل تصميم التحقق النهائي. |
| Time and customer separation / فصل الوقت والعملاء | Later stages use forward validation, exclude customer overlap, and require 90-day label maturity. / تستخدم المراحل اللاحقة تحققًا أماميًا، وتستبعد تداخل العملاء، وتشترط نضج النتائج بعد 90 يومًا. |
| Training-only transformations / تعلم التحويلات من التدريب فقط | Imputation, preprocessing, weighting, and oversampling use applicable training rows only. / يُتعلم التعويض والمعالجة والأوزان وإعادة أخذ العينات من صفوف التدريب الخاصة بكل مرحلة فقط. |
| Leakage controls / ضوابط التسرب | Post-application fields such as `days_past_due_60` and `collection_calls` are excluded; negative controls are not used for decisions. / تُستبعد خصائص ما بعد الطلب مثل الحقول المذكورة، ولا تُستخدم التجارب الضابطة غير الآمنة لاتخاذ القرار. |
| Bounded tuning / الضبط المحدود | Eight live Optuna trials completed. Honest fixed mean AP was 0.3153 versus tuned 0.3133. / اكتملت ثماني تجارب فعلية؛ لم يحسن الضبط متوسط AP مقارنة بالنموذج الثابت. |
| OOF coverage / تغطية OOF | Day 2/3 covers all 5,039 eligible rows: 50.39% of training data, with 4,961 warm-up rows. / تغطي الأيام 2 و3 جميع الصفوف المؤهلة وعددها 5,039، مع 4,961 صف تهيئة. |
| Final separation / الفصل النهائي | Day 5 uses nested OOF and a separate calibration set. Challenge labels are never used. / يستخدم اليوم الخامس OOF متداخلًا وعينة معايرة منفصلة، دون استخدام تسميات التحدي. |

Different days evaluate different models, populations, and roles. Their metrics are not directly interchangeable.

تختلف النماذج والعينات وأدوار البيانات بين الأيام، فلا تُقارن مقاييسها باعتبارها تقييمًا واحدًا متطابقًا.

## Additional analysis: interpretation and calibration / تحليل إضافي: التفسير والمعايرة
Day 4 explains a weighted LightGBM model. Permutation importance identifies `bureau_score` and `dti` as the strongest groups, with mean AP drops of 0.1270 and 0.0690. Global SHAP ranks them first, with mean absolute contributions of 0.9042 and 0.5437 in raw log-odds units.

يخص تفسير اليوم الرابع نموذج LightGBM موزونًا. أظهر Permutation Importance أن `bureau_score` و`dti` أهم مجموعتين، بانخفاض متوسط AP قدره 0.1270 و0.0690 عند تبديلهما. تصدرا أيضًا Global SHAP بمساهمات مطلقة متوسطة قدرها 0.9042 و0.5437 بوحدة raw log-odds.

For synthetic application `TR-009585`, the largest positive local contributions are `bureau_score`, `dti`, and `loan_amount_sar`. A ±1 bureau-score perturbation preserved those three leading reasons, showing stability for that small tested change only.

في الطلب الاصطناعي `TR-009585` كانت أكبر المساهمات المحلية الموجبة من `bureau_score` و`dti` و`loan_amount_sar`. حافظ تغيير درجة bureau_score بمقدار ±1 على الأسباب الثلاثة الرئيسية، وهو دليل استقرار لهذا التغيير الصغير المختبر فقط.

**Day 4 SHAP does not explain the final Logistic model.** SHAP describes model behavior, not causal effects, fairness, or legal compliance. Final-model explanations must be recomputed.

**SHAP من اليوم الرابع لا يفسر النموذج النهائي Logistic.** يصف SHAP سلوك النموذج ولا يثبت السببية أو العدالة أو الامتثال النظامي. يجب إعادة حساب التفسيرات للنموذج النهائي.




#### Day 4 independent evaluation / تقييم اليوم الرابع المستقل

| Metric on 1,733 evaluation rows / المقياس على 1,733 صف تقييم | Raw LightGBM / قبل المعايرة | Sigmoid / بعد المعايرة |
|---|---:|---:|
| Brier | 0.113027 | 0.067112 |
| ECE | 0.146871 | 0.022486 |
| Log loss | 0.357993 | 0.246749 |
| AP | 0.258677 | 0.258677 |

Calibration improved probability-quality metrics on the separate Day 4 evaluation set. However, the frozen policy exceeded risk-flag capacity in 2024Q4: 109 flags for capacity 107. The broader candidate review exceeded capacity in both periods, and the notebook reports `CAPACITY_REVIEW_REQUIRED`. Do not retune that policy on evaluation data.

حسنت المعايرة مقاييس جودة الاحتمالات في عينة تقييم مستقلة لليوم الرابع. لكن السياسة المجمدة تجاوزت سعة إشارات الخطر في 2024Q4: عدد الإشارات 109 مقابل سعة 107. كما تجاوزت المراجعة الموسعة السعة في الفترتين، فظهر `CAPACITY_REVIEW_REQUIRED`. لا يجوز إعادة ضبط هذه السياسة باستخدام بيانات التقييم.

#### Final Logistic calibration diagnostics / تشخيص معايرة Logistic النهائي

| Metric on 836 calibration-fitting rows / المقياس على 836 صف تعلم للمعاير | Raw Logistic / قبل المعايرة | Sigmoid / بعد المعايرة |
|---|---:|---:|
| Brier | 0.076473 | 0.078058 |
| ECE | 0.021121 | 0.034871 |
| Log loss | 0.266489 | 0.277296 |
| AP | 0.287803 | 0.287803 |

These are diagnostics on the same rows used to fit the calibrator. Sigmoid did not improve the displayed metrics. Day 4's improvement cannot be generalized to the final model; fresh labeled evaluation is needed.

هذه مقاييس تشخيصية على الصفوف نفسها المستخدمة لتعلم المعاير. لم تحسن Sigmoid المقاييس المعروضة. لا يمكن تعميم تحسن اليوم الرابع على النموذج النهائي، ويلزم تقييم جديد ببيانات معلّمة.

## Deployment and export / التشغيل والتصدير
The saved run reports `REPLAY_MATCH`: exported components reproduced saved probabilities and decisions within the implemented checks. Bundle byte verification passed for 48 files. This README documents captured execution and does not claim a fresh independent rerun.

أظهر التنفيذ المحفوظ `REPLAY_MATCH`، أي أن المكونات المصدرة أعادت إنتاج الاحتمالات والقرارات المحفوظة وفق الفحوص المنفذة. نجح أيضًا التحقق من بايتات الحزمة التي تضم 48 ملفًا. يوثق هذا README التنفيذ المحفوظ ولا يدّعي إعادة تشغيل مستقلة جديدة.

The legacy completeness check reports `PROJECT_WORK_REQUIRED`. It expects separate daily evidence folders, five named notebooks, and a presentation PDF. The notebook follows the updated integrated submission format. Daily ZIPs are saved in `artifacts`, while the import loop searches the project root, so earlier evidence was not imported into that bundle. Replay success does not mean the complete legacy check passed.

أظهر فحص الاكتمال السابق `PROJECT_WORK_REQUIRED` لأنه يبحث عن أدلة يومية منفصلة وخمسة دفاتر بأسماء محددة وعرض PDF. يتبع الدفتر صيغة التسليم المتكامل المحدثة. كما تُحفظ ملفات ZIP اليومية في `artifacts` بينما يبحث كود الاستيراد عنها في جذر المشروع، لذلك لم تُدمج الأدلة السابقة في تلك الحزمة. نجاح إعادة التنبؤ لا يعني نجاح فحص الاكتمال السابق بالكامل.



### Direct Python inference / الاستدلال المباشر باستخدام Python


After completing the final stage in the same runtime / بعد إكمال المرحلة النهائية في الجلسة نفسها:

```python
from inference import predict

probabilities = predict(challenge, ARTIFACTS / "final_model")
submission, batch_audit = apply_batch_policy(
    probabilities, calibrated_threshold, policy
)
```

Inference returns IDs and probabilities. Apply policy once to the complete batch, and keep the exported model and calibrator frozen together.

يعيد الاستدلال المعرفات والاحتمالات. طبّق السياسة مرة واحدة على الدفعة كاملة، وثبّت النموذج والمعاير معًا بعد التصدير.

## Technical pipeline / مراحل العمل التقنية
| Step / الخطوة | English | العربية |
|---:|---|---|
| 1 | Verify environment, files, hashes, and data contracts. | التحقق من البيئة والملفات والبصمات وعقد البيانات. |
| 2 | Load synthetic data and application-time features. | تحميل البيانات الاصطناعية وخصائص وقت الطلب. |
| 3 | Compare Logistic, XGBoost, and LightGBM baselines. | مقارنة نماذج الأساس الثلاثة. |
| 4 | Audit leakage, validate by time/customer, and tune on a reserved pool. | تدقيق التسرب والتحقق بالوقت والعملاء والضبط على حوض محجوز. |
| 5 | Generate OOF and compare imbalance treatments. | توليد OOF ومقارنة معالجة عدم التوازن. |
| 6 | Select threshold under cost and period-capacity constraints. | اختيار العتبة وفق التكلفة وسعة كل فترة. |
| 7 | Study SHAP, permutation importance, calibration, and stability. | دراسة التفسير والمعايرة والاستقرار. |
| 8 | Compare single and ensemble candidates with nested OOF. | مقارنة النماذج الفردية والتجميع باستخدام OOF متداخل. |
| 9 | Fit the chosen model, calibrate separately, and transport threshold. | تدريب النموذج المختار ومعايرته منفصلًا ونقل العتبة. |
| 10 | Predict the full batch, cap reviews, export, and replay. | التنبؤ بالدفعة كاملة وتطبيق السعة والتصدير وإعادة الإنتاج. |

## Repository structure / بنية المستودع
| Submitted file / الملف المسلّم | Purpose / الغرض |
|---|---|
| `Copy_of_Tamweel_ (1).ipynb` | Five-stage code, explanations, tables, and figures / كود المراحل الخمس وشروحاتها وجداولها ورسومها |
| `README.md` | Bilingual project documentation / توثيق المشروع بالإنجليزية والعربية |

Running the notebook generates additional files under `/content/tamweel` in Colab. These runtime files are not automatically embedded in the notebook or uploaded to GitHub.

ينشئ تشغيل الدفتر ملفات إضافية تحت `/content/tamweel` في Colab. لا تُدمج ملفات الجلسة تلقائيًا داخل الدفتر ولا تُرفع تلقائيًا إلى GitHub.

| Generated path / المسار الناتج | Contents / المحتوى |
|---|---|
| `artifacts/environment.json` | Environment / البيئة |
| `artifacts/ensemble_comparison.csv` | Candidate comparison / مقارنة البدائل |
| `artifacts/final_model/` | Model and required components / النموذج ومكوناته |
| `artifacts/final_policy.json` | Frozen policy / السياسة المجمدة |
| `artifacts/final_metrics.json` | Selection metrics, diagnostics, and batch audit / المقاييس والتشخيص وتدقيق الدفعة |
| `artifacts/day5_final_provenance.json` | Fit provenance / مصدر تنفيذ التدريب |
| `reports/MODEL_CARD.md` | Generated Model Card / بطاقة النموذج الناتجة |
| `reports/ENSEMBLE_DECISION.md` | Ensemble rationale / مبررات قرار التجميع |
| `submission/submission.csv` | ID, probability, decision / المعرف والاحتمال والقرار |
| `submission/project_bundle.zip` | Evidence bundle / حزمة الأدلة |

Download needed outputs before closing Colab. Saving the notebook preserves displayed outputs but does not automatically retain separate model, CSV, JSON, or ZIP files.

نزّل المخرجات المطلوبة قبل إغلاق Colab. يحفظ الدفتر المخرجات المعروضة، لكنه لا يحتفظ تلقائيًا بملفات النموذج وCSV وJSON وZIP المنفصلة.

## Quick start / التشغيل السريع
### 1. Open in Colab / افتح الدفتر في Colab

Download the notebook linked above. Open [Google Colab](https://colab.research.google.com/), choose **File → Upload notebook**, and select the file. You can also open your own repository notebook through Colab's GitHub tab.

نزّل الدفتر من الرابط أعلاه، وافتح Google Colab، ثم اختر **File → Upload notebook** وحدد الملف. ويمكن فتح دفتر مستودعك عبر تبويب GitHub في Colab.

### 2. Run on CPU / شغّل على CPU

Use a fresh CPU runtime and run cells top to bottom. Setup downloads verified course support files, so internet is required. This Colab path requires no API key, paid service, GPU, or Drive mount.

استخدم جلسة CPU جديدة وشغّل الخلايا من الأعلى إلى الأسفل. يحتاج التجهيز إلى الإنترنت لتنزيل ملفات الدعم والتحقق منها. لا يتطلب هذا المسار مفتاح API أو خدمة مدفوعة أو GPU أو ربط Drive.

### 3. Inspect and save / راجع واحفظ

Check each stage's status, save the executed notebook, and download generated files from Colab's Files panel.

راجع حالة كل مرحلة، واحفظ الدفتر بعد التنفيذ، ونزّل الملفات الناتجة من لوحة Files.

## Dataset and model / البيانات والنموذج
| Item / البند | Recorded value / القيمة المسجلة |
|---|---:|
| Training applications / طلبات التدريب | 10,000 |
| Challenge applications / طلبات التحدي | 2,500 |
| Application-time predictors / خصائص متاحة وقت الطلب | 22 |
| Training positive rate / نسبة التعثر في التدريب | 7.89% |
| Target / الهدف | `default_within_90d` |
| Day 5 fit/selection pool / حوض التدريب والاختيار | 6,576 |
| Day 5 calibration rows / صفوف المعايرة | 836 |
| Calibration positives / الحالات الموجبة في المعايرة | 78 |

The target is a synthetic default event within 90 days. Identifiers, dates, and the target are excluded from predictive inputs. Challenge labels remain unavailable.

الهدف حدث تعثر اصطناعي خلال 90 يومًا. تُستبعد المعرفات والتواريخ والهدف من مدخلات التنبؤ، وتبقى تسميات Challenge غير متاحة.

Recorded settings are `FAST_MODE=True`, `FULL_MODE=False`, `SEED=211`, and `N_JOBS=2`. The captured environment includes Python 3.13.16, NumPy 2.1.3, pandas 2.2.3, scikit-learn 1.6.1, XGBoost 3.4.1, LightGBM 4.6.0, SHAP 0.52.0, and Optuna 4.5.0. Use the verified notebook setup to reproduce dependencies.

الإعدادات المسجلة هي `FAST_MODE=True` و`FULL_MODE=False` و`SEED=211` و`N_JOBS=2`. إصدارات المكتبات أعلاه تخص التنفيذ المحفوظ. لإعادة التشغيل، استخدم خلية تجهيز البيئة والتحقق من الإصدارات داخل الدفتر.

## Interpretation and limitations / التفسير والقيود
**Intended use:** Educational synthetic-default analysis, model comparison, and simulated review prioritization.

**الاستخدام المقصود:** تحليل التعثر الاصطناعي للتعلم، ومقارنة النماذج، ومحاكاة أولوية المراجعة.

**Non-use:** Real lending approval/rejection, pricing, credit limits, or decisions affecting actual customers. Synthetic regions do not represent real regional risk.

**الاستخدام غير المقصود:** قبول أو رفض التمويل الحقيقي، أو التسعير، أو حدود الائتمان، أو قرارات تؤثر في عملاء فعليين. المناطق الاصطناعية لا تمثل مخاطر المناطق الحقيقية.

### Regional diagnostics / تشخيص المناطق

| Region / المنطقة | Negative cases / الحالات السالبة | OOF FPR / معدل الإشارات الخاطئة |
|---|---:|---:|
| Central / الوسطى | 489 | ≈ 7.98% |
| Western / الغربية | 486 | ≈ 10.29% |
| Eastern / الشرقية | 507 | ≈ 6.31% |
| Other / أخرى | 494 | ≈ 8.10% |

The largest observed FPR gap is about 3.98 percentage points. These finite-sample diagnostics are neither fairness certification nor proof of discrimination.

أكبر فجوة FPR مرصودة نحو 3.98 نقطة مئوية. هذه مؤشرات من عينات محدودة، وليست شهادة عدالة أو إثبات تمييز.

**Limitations:** Synthetic data, only three final OOF periods, selection using development evidence, warm-up rows without OOF, calibration fit diagnostics, and absent Challenge labels limit generalization. AP is not accuracy, and a capacity cap does not prove reliability.

**القيود:** تحد البيانات الاصطناعية وثلاث فترات OOF فقط والاختيار باستخدام أدلة التطوير وصفوف التهيئة بلا OOF وتشخيص المعايرة وغياب تسميات Challenge من تعميم النتائج. AP ليس Accuracy، والالتزام بالسعة لا يثبت الموثوقية.

**Monitoring:** Track feature drift, missingness, scores, eligible requests, cap removals, and period/batch capacity. After 90-day outcomes mature, measure AP, ROC-AUC, Brier, ECE, log loss, and regional FPR/recall with denominators and uncertainty. Validate changes on fresh labeled data and recompute explanations after model changes.

**المتابعة:** راقب تغير الخصائص والقيم المفقودة والدرجات والطلبات المؤهلة والاستبعاد بسبب السعة وسعة كل فترة ودفعة. بعد نضج نتائج 90 يومًا، قِس المقاييس المذكورة وفروق المناطق مع أحجام العينات وعدم اليقين. اختبر التعديلات على بيانات معلّمة جديدة، وأعد التفسير عند تغيير النموذج.

## Training-program attribution / نسبة البرنامج التدريبي
Prepared for **SDA-DSC-211 — Advanced Machine Learning Methods**, in the SDAIA Academy training context.

أُعد المشروع ضمن السياق التدريبي لأكاديمية سدايا لدورة **SDA-DSC-211 — أساليب تعلم الآلة المتقدمة**.

**Trainer and course-material author / المدربة ومؤلفة مواد الدورة:** Meaad Al-Marri / ميعاد المري

- [Course learning portal / بوابة الدورة](https://almiyead-rgb.github.io/advanced-machine-learning-methods-sda-dsc-211/)
- [Official student template / قالب المتدرب الرسمي](https://github.com/almiyead-rgb/sda-dsc-211-student-template)
- [@SDAIAAcademy — SDAIA Academy on GitHub / أكاديمية سدايا على GitHub](https://github.com/SDAIAAcademy)

This is an individual learner project, not an official SDAIA repository.

هذا مشروع متدرب فردي، وليس مستودعًا رسميًا لأكاديمية سدايا.

#SDAIAAcademy


### Authorship and assistance / المساهمة والإفصاح عن المساعدة

The project uses the official course template and support scripts. The learner's submitted notebook contains captured execution, stage explanations, reflection responses, and interpretation of the observed decisions and limitations.

يستخدم المشروع القالب الرسمي وملفات دعم الدورة. يحتوي دفتر المتدرب على التنفيذ المحفوظ وشروحات المراحل وإجابات التأمل وتفسير القرارات والقيود المرصودة.

ChatGPT assisted with code/output explanation, reflection wording, submission-requirement review, and README preparation. Template code and support utilities remain attributed to their original source. This documentation does not claim instructor approval.

استُخدم ChatGPT للمساعدة في شرح الكود والمخرجات وصياغة إجابات التأمل ومراجعة متطلبات التسليم وإعداد README. تُنسب أكواد القالب وأدوات الدعم إلى مصدرها الأصلي، ولا يدّعي هذا التوثيق موافقة المدرب على التسليم.

