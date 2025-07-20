عالی، سؤال بسیار مهمیه! در **Prometheus** دو نوع اصلی **Rule (قانون)** وجود داره که برای تحلیل، هشداردهی و پردازش داده‌ها استفاده می‌شن:

---

## 🔰 انواع Rule در Prometheus:

### 1. **Recording Rules** (قوانین ضبط)

### 2. **Alerting Rules** (قوانین هشدار)

---

## 1️⃣ Recording Rules – قوانین ضبط داده

📌 این نوع rule برای **محاسبه و ذخیره نتیجه یک query** استفاده می‌شه، تا در آینده سریع‌تر و با بار کمتر Query بشه.

### 🔧 مثال:

```yaml
groups:
  - name: recording_rules
    rules:
      - record: job:http_inprogress_requests:avg
        expr: avg_over_time(http_inprogress_requests[5m])
```

🔍 در اینجا:

* `record`: اسم متریک جدیدیه که ذخیره می‌شه.
* `expr`: بیان (expression) PromQL که محاسبه می‌شه.

✅ نتیجه‌ی این قانون به‌صورت یک متریک جدید در پایگاه داده ذخیره می‌شه.

📦 مزیت: کاهش هزینه پردازش در هنگام نمایش گراف‌ها یا اجرای Queryهای پیچیده.

---

## 2️⃣ Alerting Rules – قوانین هشدار

📌 این rule برای **تولید هشدار** (Alert) در صورتی که یک شرط خاص برقرار باشه، استفاده می‌شه.

### 🔧 مثال:

```yaml
groups:
  - name: alerting_rules
    rules:
      - alert: HighCPUUsage
        expr: avg(rate(cpu_usage_seconds_total[5m])) > 0.9
        for: 2m
        labels:
          severity: critical
        annotations:
          summary: "CPU usage is above 90% for 2 minutes"
```

🔍 اجزای اصلی:

* `alert`: اسم هشدار
* `expr`: شرطی که اگر برقرار باشه، هشدار فعال می‌شه
* `for`: مدت زمانی که شرط باید برقرار بمونه تا هشدار فعال شه
* `labels`: تگ‌هایی که به alert اضافه می‌شن
* `annotations`: توضیحات و پیام برای alert (مثلاً برای نمایش در Alertmanager)

🛎️ این هشدارها به Alertmanager فرستاده می‌شن و از اون‌جا می‌تونن به ایمیل، Slack، PagerDuty، و ... برن.

---

## 🔄 ترکیب Recording + Alerting

خیلی وقت‌ها اول یک **Recording Rule** می‌نویسن که یه Query سنگین رو تبدیل به متریک جدید کنه، بعد همون متریک رو توی **Alerting Rule** استفاده می‌کنن:

```yaml
# recording rule
- record: instance:cpu_usage:avg5m
  expr: avg(rate(cpu_usage_seconds_total[5m])) by (instance)

# alerting rule
- alert: CPUHigh
  expr: instance:cpu_usage:avg5m > 0.9
  for: 5m
```

---

اگر خواستی، می‌تونم یه نمونه کامل `rules.yml` برای پروژه واقعی برات آماده کنم. دوست داری؟
