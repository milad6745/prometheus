برای مانیتورینگ کانتینرهای داکری با استفاده از Prometheus، شما می‌توانید از `cAdvisor` (Container Advisor) استفاده کنید. `cAdvisor` یک ابزار گوگل است که به طور خاص برای جمع‌آوری، پردازش و ذخیره‌سازی اطلاعات عملکردی در مورد کانتینرها طراحی شده است. سپس Prometheus می‌تواند این اطلاعات را از `cAdvisor` جمع‌آوری و ذخیره کند.

### مراحل راه‌اندازی:

1. **راه‌اندازی `cAdvisor`**:

   `cAdvisor` را به عنوان یک کانتینر داکر اجرا کنید تا متریک‌های داکری را جمع‌آوری کند.

   ```bash
   docker run \
   --volume=/:/rootfs:ro \
   --volume=/var/run:/var/run:ro \
   --volume=/sys:/sys:ro \
   --volume=/var/lib/docker/:/var/lib/docker:ro \
   --volume=/dev/disk/:/dev/disk:ro \
   --publish=8080:8080 \
   --detach=true \
   --name=cadvisor \
   google/cadvisor:latest
   ```

   - این کانتینر `cAdvisor` را در پورت `8080` اجرا می‌کند و به `/var/lib/docker/` و سایر دایرکتوری‌های سیستمی دسترسی می‌دهد تا بتواند اطلاعات عملکردی کانتینرها را جمع‌آوری کند.

2. **پیکربندی `Prometheus` برای جمع‌آوری متریک‌های `cAdvisor`:**

   شما باید `Prometheus` را به گونه‌ای پیکربندی کنید که متریک‌های `cAdvisor` را اسکرپ کند. فایل پیکربندی `prometheus.yml` را به‌روزرسانی کنید:

   ```yaml
   scrape_configs:
     - job_name: 'cadvisor'
       static_configs:
         - targets: ['<cAdvisor_IP>:8080']
   ```

   - `<cAdvisor_IP>` را با آدرس IP سروری که `cAdvisor` در آن اجرا می‌شود جایگزین کنید. اگر `Prometheus` و `cAdvisor` روی یک سرور قرار دارند، می‌توانید از `localhost` استفاده کنید.

3. **اجرای `Prometheus`**:

   پس از اعمال تنظیمات، `Prometheus` را راه‌اندازی کنید تا پیکربندی جدید را اعمال کند:

   - مطمئن شوید که مسیر `/path/to/prometheus.yml` به درستی به فایل `prometheus.yml` اشاره می‌کند.

4. **مشاهده متریک‌ها در Prometheus**:

   پس از راه‌اندازی `Prometheus` و `cAdvisor`، می‌توانید به داشبورد `Prometheus` در `http://<Prometheus_IP>:9090` بروید و کوئری‌های مختلفی برای مشاهده متریک‌های جمع‌آوری‌شده از کانتینرهای داکری اجرا کنید.

   به عنوان مثال، کوئری زیر اطلاعاتی درباره استفاده از CPU توسط کانتینرهای داکری می‌دهد:

   ```promql
   rate(container_cpu_usage_seconds_total[1m])
   ```

### اختیاری: استفاده از Grafana برای داشبوردهای گرافیکی:

اگر می‌خواهید داشبوردهای گرافیکی زیبا و کاربردی داشته باشید، می‌توانید `Grafana` را نصب کرده و آن را به `Prometheus` متصل کنید. `Grafana` می‌تواند به عنوان یک سرویس‌دهنده `Prometheus` عمل کرده و داشبوردهای از پیش ساخته شده را برای نمایش متریک‌های کانتینرهای داکری نمایش دهد.

### جمع‌بندی:

1. اجرای `cAdvisor` برای جمع‌آوری متریک‌های کانتینرهای داکری.
2. پیکربندی `Prometheus` برای اسکرپ متریک‌های `cAdvisor`.
3. استفاده از کوئری‌های `PromQL` در `Prometheus` برای مشاهده متریک‌ها.
4. (اختیاری) استفاده از `Grafana` برای داشبوردهای گرافیکی.
