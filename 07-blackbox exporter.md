## blackbox Exporter

برای مانیتور کردن سایت `cisco.com` با استفاده از `Prometheus` و `Blackbox Exporter`، می‌توانید مراحل زیر را دنبال کنید. این مراحل شامل پیکربندی `Blackbox Exporter` برای پروب کردن سایت و سپس تنظیم `Prometheus` برای جمع‌آوری و نمایش متریک‌های مرتبط است.

### مراحل انجام کار:

1. **اجرای `Blackbox Exporter`:**

   ابتدا باید `Blackbox Exporter` را اجرا کنید. اگر از داکر استفاده می‌کنید، می‌توانید آن را به راحتی با دستور زیر اجرا کنید:

   ```bash
   docker run -d -p 9115:9115 --name=blackbox_exporter prom/blackbox-exporter
   ```

2. **پیکربندی `Prometheus` برای مانیتور کردن `cisco.com`:**

   حالا باید فایل پیکربندی `Prometheus` (به نام `prometheus.yml`) را ویرایش کنید تا از `Blackbox Exporter` برای مانیتور کردن `cisco.com` استفاده کند.

   در این مثال، فرض می‌کنیم `Blackbox Exporter` در همان سروری که `Prometheus` در حال اجراست با پورت `9115` اجرا می‌شود:

   ```yaml
     - job_name: "blackbox"
    metrics_path: /probe
    params:
      module: [http_2xx]  # ماژولی که برای پروب HTTP استفاده می‌شود
    static_configs:
      - targets:
        - https://www.cisco.com  # آدرس سایت مورد نظر برای مانیتورینگ
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - target_label: __address__
        replacement: 127.0.0.1:9115  # آدرس Blackbox Exporter
   ```

   توضیحات:
   - **job_name:** یک نام برای کار (job) مانیتورینگ مشخص می‌کند.
   - **metrics_path:** مسیری که `Prometheus` برای جمع‌آوری متریک‌ها استفاده می‌کند.
   - **params.module:** ماژولی که مشخص می‌کند چه نوع پروبی انجام شود (در اینجا `http_2xx`).
   - **static_configs.targets:** آدرس سایتی که می‌خواهید مانیتور کنید.
   - **relabel_configs:** این بخش اطمینان حاصل می‌کند که `Prometheus` به درستی با `Blackbox Exporter` ارتباط برقرار می‌کند.

3. **ریلود کردن پیکربندی `Prometheus`:**

   پس از ویرایش فایل `prometheus.yml`، باید `Prometheus` را ریلود کنید تا پیکربندی جدید اعمال شود. برای این کار می‌توانید از طریق رابط وب یا با استفاده از سیگنال `SIGHUP` این کار را انجام دهید:

   ```bash
   kill -HUP <prometheus_pid>
   ```

4. **مشاهده متریک‌ها در `Prometheus`:**

   پس از راه‌اندازی `Prometheus` و اعمال پیکربندی جدید، می‌توانید در داشبورد `Prometheus` به متریک‌های مربوط به پروب سایت `cisco.com` دسترسی پیدا کنید. به عنوان مثال:

   - بررسی موفقیت‌آمیز بودن پروب:

     ```promql
     probe_success{job="blackbox_cisco"}
     ```

     اگر مقدار این متریک `1` باشد، پروب موفقیت‌آمیز بوده است. اگر `0` باشد، پروب با خطا مواجه شده است.

   - اندازه‌گیری زمان پاسخگویی سایت:

     ```promql
     probe_duration_seconds{job="blackbox_cisco"}
     ```

     این متریک زمان لازم برای تکمیل پروب را نشان می‌دهد.

### جمع‌بندی:
با این پیکربندی، `Prometheus` از `Blackbox Exporter` برای بررسی در دسترس بودن و زمان پاسخگویی سایت `cisco.com` استفاده می‌کند. به این ترتیب، می‌توانید به راحتی سایت‌های دیگر را نیز مانیتور کنید و از سلامت و کارایی آن‌ها مطمئن شوید.
