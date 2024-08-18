## Node explorer

go to prometheus web site
```
promethus.io
wget https://github.com/prometheus/node_exporter/releases/download/v1.8.2/node_exporter-1.8.2.linux-amd64.tar.gz
tar xvf node_exporter-1.8.2.linux-amd64.tar.gz
cd node_exporter-1.8.2.linux-amd64.tar.gz
./node_exporter
```
حالا داخل تنظیمات پرومتوس میشویم و پورت نود را اضافه میکنیم .

```
  - job_name: "prometheus"

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ["localhost:9090"]

  - job_name: "Node"

    static_configs:
      - targets: ["192.168.72.18:9100"]
```

go to website
status > target

![image](https://github.com/user-attachments/assets/85b16b6d-66a0-4541-ac9c-53af015d9dfc)

حالا میتوانیم یک گراف جدید تعریف کنیم .

![image](https://github.com/user-attachments/assets/5345e4bb-4110-4b95-937b-db4437f880dc)

مدلی دیگر از گراف

![image](https://github.com/user-attachments/assets/62bb0748-d870-4c18-97c3-dc04349c3f95)
