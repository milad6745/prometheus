## change config parameter

تغییر زمان نگهداری لاگ
```
./prometheus --storage.tsdb.retention.time=15d --config.file="/root/prometheus-2.54.0.linux-amd64/prometheus.yml" &
```

**go to webpage**

status > commane lines and flag

![image](https://github.com/user-attachments/assets/dac023a3-f1d4-430e-a35d-be43b4c994b4)



در Kubernetes، وقتی در مورد **TSDB** صحبت می‌کنیم، معمولاً منظور **Prometheus TSDB (Time Series Database)** هست، که برای ذخیره داده‌های مانیتورینگ به کار می‌ره.

حالا بیایم بررسی کنیم:

---

## 📦 TSDB در Prometheus

Prometheus از یک TSDB داخلی استفاده می‌کنه تا **داده‌های سری زمانی** (مثل metricها) رو ذخیره کنه. این داده‌ها معمولاً روی دیسک ذخیره می‌شن و قابل مدیریت هستند.

---

## 🔧 تنظیمات مهم مربوط به TSDB

### 1. ⏳ **Retention Time** (زمان نگهداری داده‌ها)

* مشخص می‌کنه Prometheus داده‌ها رو **چقدر نگه داره** قبل از حذف.
* پیش‌فرض: **15 روز**
* قابل تنظیم با فلگ:

```bash
--storage.tsdb.retention.time=30d
```

یا بر حسب ساعت یا سال:

```bash
--storage.tsdb.retention.time=90d    # 90 روز
--storage.tsdb.retention.time=6h     # 6 ساعت
--storage.tsdb.retention.time=1y     # 1 سال
```

---

### 2. 💾 **Retention Size** (حداکثر سایز دیتابیس روی دیسک)

* مشخص می‌کنه Prometheus حداکثر چقدر فضا می‌تونه استفاده کنه.
* **از Prometheus v2.7 به بعد اضافه شده**
* فلگ:

```bash
--storage.tsdb.retention.size=20GB
```

اگر یکی از `retention.time` یا `retention.size` به پایان برسه، Prometheus داده‌های قدیمی‌تر رو پاک می‌کنه.

---

### 3. 📁 **TSDB Path** (مسیر ذخیره‌سازی داده‌ها)

* مسیر دیسکی که Prometheus برای ذخیره سری‌های زمانی استفاده می‌کنه.
* پیش‌فرض: `/prometheus`
* قابل تغییر با:

```bash
--storage.tsdb.path=/data/prometheus
```

در Kubernetes معمولاً این مسیر به یک PVC (PersistentVolumeClaim) متصل می‌شه.

---

## 🧱 مثال کامل arg ها در Prometheus deployment:

```yaml
args:
  - "--storage.tsdb.retention.time=30d"
  - "--storage.tsdb.retention.size=50GB"
  - "--storage.tsdb.path=/prometheus"
```

---

## ✅ جمع‌بندی

| مورد              | فلگ مربوطه                             | توضیح                      |
| ----------------- | -------------------------------------- | -------------------------- |
| زمان نگهداری دیتا | `--storage.tsdb.retention.time=30d`    | چقدر داده نگه داشته بشه    |
| حداکثر حجم دیتا   | `--storage.tsdb.retention.size=50GB`   | حداکثر حجم دیتای روی دیسک  |
| مسیر ذخیره دیتا   | `--storage.tsdb.path=/data/prometheus` | محل ذخیره داده‌ها روی دیسک |

---

اگر داری از **Prometheus Operator** استفاده می‌کنی (مثل kube-prometheus-stack)، این تنظیمات معمولاً داخل `values.yaml` یا در CRD مربوط به Prometheus تعریف می‌شن. بگو از چی استفاده می‌کنی تا دقیق‌تر راهنمایی کنم.
