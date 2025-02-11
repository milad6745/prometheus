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

   این دستور تمام اجزای لازم مانند Prometheus، Alertmanager و Grafana را در کلاستر Kubernetes شما نصب می‌کند.

2. **دسترسی به Prometheus و Grafana:**

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

3. **نظارت و جمع‌آوری داده‌ها:**

   پس از نصب Prometheus، شما می‌توانید از طریق آن داده‌های مربوط به عملکرد و وضعیت کلاستر Kubernetes خود را مشاهده و بررسی کنید. Prometheus به طور خودکار متریک‌ها را از منابع مختلف در Kubernetes جمع‌آوری خواهد کرد.

   برای مشاهده متریک‌ها، به قسمت **Status > Targets** در Prometheus بروید تا بررسی کنید که آیا تمام اجزا به درستی جمع‌آوری می‌شوند یا نه.

4. **تنظیم داشبوردهای Grafana:**

   در Grafana، شما می‌توانید داشبوردهایی را برای مشاهده متریک‌های مختلف (مثل CPU، Memory، Pods و Node) بسازید. Grafana معمولاً داشبوردهای پیش‌ساخته‌ای برای Kubernetes دارد که می‌توانید آن‌ها را از طریق **Explore** یا **Dashboards > Manage** اضافه کنید.

این مراحل به شما کمک خواهد کرد که بتوانید کلاستر Kubernetes خود را با استفاده از Prometheus مانیتور کنید. اگر سوال یا مشکلی داشتید، خوشحال می‌شوم کمک کنم!
