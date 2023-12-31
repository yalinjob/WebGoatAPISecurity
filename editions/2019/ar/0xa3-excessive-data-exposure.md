# API3:2019 خلل في استعراض البيانات

| عوامل التهديد/ الاستغلال                                                                                                                                                                                                  | نقاط الضعف	                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | التأثير	                                                                   |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------|
| خصائص API : قابلية الاستغلال **3**	                                                                                                                                                                                             | الانتشار **2** : قابلية الاكتشاف  **2**	                                                                                                                                                                                                                                                                                                                                                                                                                                                                         | التأثر التقني **2** : تأثر الأعمال                                            |
| عادة ما يكون الكشف الغير مصرح به عن المعلومات او البيانات من خلال مراقبة حركة مرور البيانات او الطلبات وتحليل جميع الردود القادمة من API، وذلك للبحث عن أي بيانات حساسة يتم استعادتها وغير مصرح للمستخدم بالاطلاع عليها.	 | تعتمد واجهات التطبيقات API على ان تكون عوامل التصفية من جانب المستخدم حيث ان API عادة ما يتم استخدامه كمصدر للبيانات وفي بعض الأحيان يقوم المطورون بتنصيب API بشكل عام وافتراضي من غير التفكير في طرق التعامل مع البيانات الحساسة. حيث ان أدوات الفحص واكتشاف الثغرات الأمنية تستطيع رصد مثل تلك الثغرات والتي يصعب على API معرفة إذا كان هذا الطلب لأغراض الاستخدام الصحيح والقانوني او لأغراض الاطلاع وتسريب البيانات الحساسة. لذلك يجب ان نقوم بتصنيف البيانات الحساسة والفهم العميق لألية الطلب لها.	 | عادة ما يودي الكشف عن البيانات الى الاطلاع غير المصرح به او تسريب البيانات |

   



## هل أنا معرض لهذه الثغرة؟

<p dir='rtl' align='right'> تقوم واجهة برمجة التطبيقات  بإرجاع البيانات الحساسة إلى العميل حسب التصميم والطلب . عادة ما يتم تصفية هذه البيانات من جانب العميل قبل تقديمها للمستخدم. يمكن للمهاجم بسهولة اعتراض حركة المرور ورؤية البيانات الحساسة.


## امثلة على سيناريوهات الهجوم: 

### السيناريو الاول: 

 يقوم مطورين تطبيق الهواتف الذكية باستخدام `/api/articles/{articleId}/comments/{commentId}` كمصدر للبيانات وذلك بهدف عرض المقالات وبعض البيانات الوصفية الخاصة بها. وهنا يقوم المهاجم باعتراض حركة مرور البيانات الصادرة من هذه التطبيق وقراءة تلك البيانات الوصفية والتي قد تقوم بتسريب بعض البيانات الحساسة مثل بيانات كاتبين التعليقات وبعض بيانات تحديد الشخصية كـ PII، حيث ان مصدر البيانات تم تنصيبه بشكل افتراضي على هيئة (JSON) ومبنية على عامل التصفية لدى المستخدم.

### السيناريو الثاني : 
 يسمح نظام المراقبة المبني على أنظمة IOT او انترنت الأشياء لمدير النظام بانشاء حسابات  للمستخدمين بمختلف الصلاحيات، حيث قام مدير النظام بانشاء حساب لاحد حراس الامن والذي مصرح له بالاطلاع على بعض المباني و المواقع. وعندما قام الحارس باستخدام هاتفه للاطلاع على النظام يقوم نظام API باستدعاء لوحة أنظمة المراقبة المتاحة له من خلال /api/sites/111/cameras والتي تسمح له بمعرفة عدد الكاميرات المتاحة الاطلاع عليها من قبل حارس الامن حيث ان بعد عملية الطلب تم استقبال الرد من الخادم ببعض المعلومات التفصيلية على سبيل المثال `{"id":"xxx","live_access_token":"xxxx-bbbbb","building_id":"yyy"}`  والتي لا تظهر على لوحة المراقبة الخاصة بالحارس ( الواجهة الرسومية ) بل في تفاصيل الطلب فقط والتي تحتوي على جميع الكاميرات والمباني.

### كيف أمنع هذه الثغرة؟ 

*  لا تثق ابداُ في عوامل التصفية لدى العميل او المستخدم في حال كانت هناك بيانات حساسة
*  دائماً قم بمراجعة الطلبات والردود من مصادر البيانات للتاكد من ان جميع البيانات المتوفرة هي بيانات غير حساسة ومنطقية
*  يجب على مهندسي التطبيقات الداخلية و مسؤولي الانظمة السؤال بشكل دائم من هم مستخدمي تلك البيانات قبل البدء بتنصيب API جديدة على النظام.
*  تجنب استخدام الإعدادات العامة مثل to_json() و To_string() واستبدلها بخصائص معينة ومحددة مطلوب استرجاعها. 
*   قم بتصنيف المعلومات الحساسة و المعلومات المرتبطة بالهوية الشخصية (PII) التي يخزنها تطبيقك ويعمل معها ، مع مراجعة جميع الطلبات الخاصة بواجهة برمجة التطبيقاتAPI   والردود المتوقعة منها ومعرفة الاشكاليات الامنية التي قد يتم رصدها بتلك الردود  
*  قم باستخدام آليات التحقق مثل `(schema-based response validation mechanism)` وحدد ماهي البيانات التي يتم ارجاعها مع الطلبات بما في ذلك الاخطاء والمعلومات المتوفرة بها.


### المراجع : 
### المصادر الخارجية :
* [CWE-213: Intentional Information Exposure][1]


[1]: https://cwe.mitre.org/data/definitions/213.html
