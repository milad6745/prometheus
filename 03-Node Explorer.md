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
```
