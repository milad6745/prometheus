برای مانیتور کردن کلاستر Kubernetes خود با Prometheus، می‌توانید مراحل زیر را دنبال کنید:

1. **نصب Prometheus در Kubernetes با استفاده از Helm:**

   - ابتدا باید Helm را نصب کرده باشید. اگر Helm را نصب نکردید، دستور زیر را برای نصب آن استفاده کنید:

     ```bash
     curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
     ```

   - حالا باید Prometheus را از Helm charts نصب کنید. برای این کار، ابتدا باید مخزن `prometheus-community` را اضافه کنید:

     ```bash
     helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
     helm repo update
     ```

   - سپس برای نصب Prometheus، از دستور زیر استفاده کنید:

     ```bash
     helm install prometheus prometheus-community/kube-prometheus-stack
     ```

- چک کردن فایل values.yml v

```bash
     helm show values prometheus prometheus-community/kube-prometheus-stack > values.yml
```
سپس تغییرات مورد نظر را در values.yml  میدهیم و سیم میکنیم  و مجدد اینستال میکنیم.



     ```bash
     helm install prometheus prometheus-community/kube-prometheus-stack -f values.yml
     ```

   این دستور تمام اجزای لازم مانند Prometheus، Alertmanager و Grafana را در کلاستر Kubernetes شما نصب می‌کند.

1. **دسترسی به Prometheus و Grafana:**

   - بعد از نصب، می‌توانید با استفاده از دستور زیر به سرویس‌ها دسترسی پیدا کنید:

     ```bash
     kubectl port-forward svc/prometheus-kube-prometheus-prometheus 9090:9090
     ```

     سپس می‌توانید از طریق مرورگر خود به Prometheus در آدرس `http://localhost:9090` دسترسی پیدا کنید.

   - برای دسترسی به Grafana، دستور زیر را وارد کنید:

     ```bash
     kubectl port-forward svc/prometheus-grafana 3000:80
     ```

     به این ترتیب می‌توانید به Grafana از طریق آدرس `http://localhost:3000` دسترسی پیدا کنید. اعتبار ورود پیش‌فرض برای Grafana معمولاً `admin/admin` است.

2. **نظارت و جمع‌آوری داده‌ها:**

   پس از نصب Prometheus، شما می‌توانید از طریق آن داده‌های مربوط به عملکرد و وضعیت کلاستر Kubernetes خود را مشاهده و بررسی کنید. Prometheus به طور خودکار متریک‌ها را از منابع مختلف در Kubernetes جمع‌آوری خواهد کرد.

   برای مشاهده متریک‌ها، به قسمت **Status > Targets** در Prometheus بروید تا بررسی کنید که آیا تمام اجزا به درستی جمع‌آوری می‌شوند یا نه.

3. **تنظیم داشبوردهای Grafana:**

   در Grafana، شما می‌توانید داشبوردهایی را برای مشاهده متریک‌های مختلف (مثل CPU، Memory، Pods و Node) بسازید. Grafana معمولاً داشبوردهای پیش‌ساخته‌ای برای Kubernetes دارد که می‌توانید آن‌ها را از طریق **Explore** یا **Dashboards > Manage** اضافه کنید.

این مراحل به شما کمک خواهد کرد که بتوانید کلاستر Kubernetes خود را با استفاده از Prometheus مانیتور کنید. اگر سوال یا مشکلی داشتید، خوشحال می‌شوم کمک کنم!



---

### ۱. پیدا کردن آدرس دسترسی به Grafana

چون با Helm نصب کردی و تنظیمات پیش‌فرضه، احتمالاً Grafana یک `Service` داره. این دستور رو بزن تا ببینی:

```bash
kubectl get svc
```

دنبال سرویسی بگرد که اسمش چیزی مثل `prometheus-grafana` باشه.

- اگه `Type: ClusterIP` بود، یعنی فقط داخل کلاستر قابل دسترسیه.
- اگه `Type: NodePort` یا `LoadBalancer` بود، میشه از بیرون هم وصل شد.

**اگه ClusterIP بود** و میخوای راحت تست کنی، می‌تونی پورتش رو به سیستم خودت فوروارد کنی:

```bash
kubectl port-forward svc/prometheus-grafana 3000:80
```

حالا برو تو مرورگرت و باز کن:

```
http://localhost:3000
```

---

### ۲. لاگین به Grafana

یوزرنیم و پسورد پیش‌فرض معمولاً اینه:

- **Username:** `admin`
- **Password:** `prom-operator` یا `admin`

اگر پسورد رو نمی‌دونی، می‌تونی از Kubernetes Secret اینجوری دربیاری:

```bash
kubectl get secret prometheus-grafana -o jsonpath="{.data.admin-password}" | base64 --decode
```

---

### ۳. Prometheus توی Grafana

حالا تو Grafana به احتمال زیاد **Prometheus** به صورت خودکار به عنوان Datasource اضافه شده.  
ولی اگه نبود، راحت میتونی Datasource اضافه کنی:

- از منوی کناری > **Connections** > **Data Sources** > **Add data source** > **Prometheus** انتخاب کن.
- آدرس Prometheus رو بده. معمولاً چیزی شبیه اینه:

```
http://prometheus-kube-prometheus-prometheus:9090
```
(این اسم سرویس Prometheus درون کلاستر هست)

---

### ۴. باز کردن خود Prometheus

اگه میخوای مستقیم خود Prometheus رو ببینی:

```bash
kubectl port-forward svc/prometheus-kube-prometheus-prometheus 9090
```

بعد تو مرورگر برو به:

```
http://localhost:9090
```

---


