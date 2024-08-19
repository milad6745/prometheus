## Github Exporter

برای فعال‌سازی و استفاده از GitHub Exporter در Prometheus به منظور مانیتورینگ داده‌های GitHub، می‌توانید مراحل زیر را دنبال کنید:

### 1. نصب GitHub Exporter

ابتدا باید GitHub Exporter را نصب کنید. شما می‌توانید از Docker برای اجرای GitHub Exporter استفاده کنید:

```bash
docker run -d --name=github_exporter -p 9171:9171 -e GITHUB_TOKEN=<your_github_token> pierrecdn/github_exporter
```

در اینجا:
- `9171` پورتی است که GitHub Exporter به آن گوش می‌دهد.
- `GITHUB_TOKEN` توکن GitHub شما است که برای دسترسی به API استفاده می‌شود. شما باید یک توکن GitHub شخصی (Personal Access Token) ایجاد کنید که حداقل دارای دسترسی خواندن (`read`) به مخازن عمومی و خصوصی شما باشد.

### 2. اضافه کردن پیکربندی GitHub Exporter به فایل `prometheus.yml`

سپس باید پیکربندی GitHub Exporter را به فایل `prometheus.yml` اضافه کنید:

```yaml
# my global config
global:
  scrape_interval: 15s
  evaluation_interval: 15s

scrape_configs:
  - job_name: 'github_exporter'
    metrics_path: /metrics
    static_configs:
      - targets: ['localhost:9171']
```

### 3. راه‌اندازی مجدد Prometheus

پس از افزودن پیکربندی، Prometheus را راه‌اندازی مجدد کنید تا تغییرات اعمال شوند:

```bash
sudo systemctl restart prometheus
```

یا اگر از Docker برای Prometheus استفاده می‌کنید:

```bash
docker restart prometheus
```

### 4. بررسی متریک‌ها

پس از راه‌اندازی مجدد Prometheus، می‌توانید به رابط وب Prometheus مراجعه کرده و متریک‌هایی که GitHub Exporter تولید می‌کند را بررسی کنید. به عنوان مثال، می‌توانید متریک‌های زیر را مشاهده کنید:

- `github_rate_limit`: این متریک نشان‌دهنده تعداد درخواست‌های باقی‌مانده برای GitHub API است.
- `github_repository_stars`: تعداد ستاره‌های یک مخزن خاص را نشان می‌دهد.

### 5. تعریف Alerting (اختیاری)

شما می‌توانید بر اساس متریک‌های GitHub Exporter هشدار‌هایی را تعریف کنید. به عنوان مثال، اگر تعداد درخواست‌های باقی‌مانده GitHub API کمتر از یک مقدار مشخص باشد، می‌توانید هشدار تعریف کنید.

```yaml
groups:
  - name: github_alerts
    rules:
      - alert: GitHubRateLimitLow
        expr: github_rate_limit_remaining < 100
        for: 5m
        labels:
          severity: warning
        annotations:
          summary: "GitHub API rate limit is low"
          description: "GitHub API rate limit is {{ $value }}. This may cause issues with fetching data from GitHub."
```

این پیکربندی به شما اجازه می‌دهد تا با استفاده از Prometheus داده‌های GitHub خود را مانیتور کرده و بر اساس نیازهای خود هشدار تعریف کنید.
