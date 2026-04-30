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