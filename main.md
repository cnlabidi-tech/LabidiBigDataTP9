# 🧪 TP: Apache Spark Structured Streaming

## 🔗 البيئة
تم تنفيذ هذا العمل باستخدام:
- Apache Spark (Structured Streaming)
- Docker
- Netcat (nc -lk 9999)
- Scala (spark-shell)

---

## 📌 الهدف
الهدف من هذا العمل هو:
- فهم مفهوم **Streaming Data**
- استخدام **Spark Structured Streaming**
- تحليل البيانات القادمة في الوقت الحقيقي (Real-time)

---

## 🧱 الخطوة 1: إنشاء مصدر البيانات (Socket)

قمنا بتشغيل مصدر بيانات باستخدام:

```bash
nc -lk 9999

ثم بدأنا بإرسال بيانات على الشكل:

200 /home 100
404 /login 200
200 /home 500

📌 كل سطر يمثل:

status (كود HTTP)
url (المسار)
size (حجم الاستجابة)
🧱 الخطوة 2: قراءة البيانات في Spark
val lines = spark.readStream
  .format("socket")
  .option("host", "localhost")
  .option("port", 9999)
  .load()

📌 النتيجة:

DataFrame يحتوي على عمود واحد: value
🧱 الخطوة 3: Parsing البيانات

قمنا بتقسيم السطر إلى أعمدة:

import spark.implicits._

val parsed = lines.select(
  split($"value", " ").getItem(0).cast("int").alias("status"),
  split($"value", " ").getItem(1).alias("url"),
  split($"value", " ").getItem(2).cast("int").alias("size")
)

📌 النتيجة:

DataFrame يحتوي على:
status
url
size
🧱 الخطوة 4: حساب عدد الطلبات لكل URL
val urlCount = parsed.groupBy("url").count()
عرض النتائج:
val q1 = urlCount.writeStream
  .outputMode("complete")
  .format("console")
  .start()

📊 مثال:

url     | count
/home   | 449
/login  | 223
🧱 الخطوة 5: حساب مجموع الحجم لكل Status
val statusSize = parsed.groupBy("status").sum("size")
عرض النتائج:
val q2 = statusSize.writeStream
  .outputMode("complete")
  .format("console")
  .start()

📊 مثال:

status | total_size
200    | 85500
404    | 28600
🧱 الخطوة 6: تحليل الأخطاء فقط (Bonus)

قمنا بتصفية البيانات:

val errors = parsed.filter($"status" =!= 200)

ثم:

val errorCount = errors.groupBy("status").count()
عرض النتائج:
val q3 = errorCount.writeStream
  .outputMode("complete")
  .format("console")
  .start()

📊 مثال:

status | count
404    | 111
🧱 الخطوة 7: تشغيل جميع الـ Streams
spark.streams.awaitAnyTermination()
📊 ملاحظات مهمة
✔ Batch 0 فارغ
لأن Spark ينشئ أول batch قبل وصول البيانات
✔ النتائج cumulative
كل Batch يحتوي على النتائج الكلية منذ البداية
السبب: استخدام outputMode("complete")
✔ سلوك النظام
كلما أدخلنا بيانات جديدة:
القيم تزداد
لا يتم إعادة التصفير
⚠️ ملاحظات تقنية
socket لا يصلح للإنتاج لأنه:
لا يدعم recovery
فقط للتجارب
🧠 المفاهيم المستعملة
Structured Streaming
Micro-batching
DataFrame API
Aggregation (groupBy)
Filtering
Real-time processing
🏁 الخلاصة

في هذا العمل قمنا بـ:

✔ قراءة بيانات streaming من socket
✔ تحويل البيانات (parsing)
✔ تحليل البيانات بثلاث طرق:

count per URL
sum(size) per status
count errors فقط

✔ فهم سلوك الـ batches
✔ تطبيق streaming بشكل عملي

🏆 النتيجة

تم إنجاز TP بالكامل + Bonus بنجاح ✔✔✔