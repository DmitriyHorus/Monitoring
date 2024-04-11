# Monitoring-VM
### Настройка правил в Firewall:
```
firewall-cmd --permanent --add-port={3000,9090}/tcp && firewall-cmd --reload
```
### Создаем каталоги для хранения:
```
mkdir -p /opt/prometheus-vm/{prometheus,grafana,alertmanager,blackbox,pg-data,pgadmin-data}
```
### Создаём файл конфигурации:

```
vim prometheus/prometheus.yml
```
```
scrape_configs:
  - job_name: CentOS-VM
    scrape_interval: 5s
    static_configs:
    - targets: ['node-exporter:9100']
...

rule_files:
  - 'alert.rules'

alerting:
  alertmanagers:
  - scheme: http
    static_configs:
    - targets:
      - "alertmanager:9093"
```
### Переходим в каталог:
```
cd /opt/prometheus-vm
```
### Запускаем контейнеры:
```
docker-compose up -d
```
### Создаем файл с правилами оповещения:

```
vim prometheus/alert.rules
```
```
groups: 
- name: test
  rules:
  - alert: PrometheusTargetMissing
    expr: up == 0
    for: 0m
    labels:
      severity: critical
    annotations:
      summary: "Prometheus target missing (instance {{ $labels.instance }})"
      description: "A Prometheus target has disappeared. An exporter might be crashed. VALUE = {{ $value }}  LABELS: {{ $labels }}"
```
