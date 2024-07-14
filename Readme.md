# Описание
Cистема мониторинга на основе Prometheus – PAVG (Prometheus, Alertmanager, VictoriaMetrics, Grafana

# Подготовка
```
sudo apt install net-tools
```
Узнаем свой ip-адрес
```
ifconfig
```
Получаю 192.168.1.103  и проверяю что он постоянный

# Prometheus
```
cd ~/Prometheus
```
# Копируем конфиг
```
cp prometheus.yml ~/prometheus-2.53.1.linux-amd64$
```
# Запускаем
```
cd ~/prometheus-2.53.1.linux-amd64
./prometheus --config.file=prometheus.yml
```
# Проверяем
http://192.168.1.103:9090/metrics
http://192.168.1.103:9090/graph 
Вводим в строку поиска ```prometheus_target_interval_length_seconds``` и нажимаем Execute

# Exporters
```
cd Exporters
```

## Копируем
```
sudo cp node-exporter.service /etc/systemd/system
sudo cp blackbox-exporter.service /etc/systemd/system
sudo cp cadvisor.service /etc/systemd/system
```
## Стартуем сервисы
```
sudo systemctl daemon-reload
sudo systemctl start node-exporter cadvisor blackbox-exporter
sudo systemctl status node-exporter cadvisor blackbox-exporter
sudo systemctl enable node-exporter cadvisor blackbox-exporter
```
## Проверяем работу
http://192.168.1.103:9100/metrics (node)
http://192.168.1.103:8080/metrics (cadvisor)
http://192.168.1.103:9115/metrics (blackbox)
http://192.168.1.103:9115/probe?target=github.com&module=http_2xx
http://192.168.1.103:9115/probe?target=github.com:443&module=tcp_connect

# Alertmanager
```
cd Alertmanager
```
## Копируем
```
mkdir /etc/alertmanager/
sudo cp alertmanager.yml /etc/alertmanager
sudo cp alertmanager.service /etc/systemd/system
```

## Стартуем
```
sudo systemctl daemon-reload
sudo systemctl start alertmanager
sudo systemctl status alertmanager
sudo systemctl enable alertmanager
```

## Проверяем работу
```
http://192.168.1.103:9093
http://192.168.1.103:9093/#/alerts
http://192.168.1.103:9093/#/status
```

# VictoriaMetrics
```
cd VictoriaMetrics
```
## Подготовить каталог для хранения данных
```
sudo mkdir -p /data/victoriametrics
```
## Копируем
```
sudo cp victoriametrics.service /etc/systemd/system
```
## Стартуем
```
sudo systemctl daemon-reload
sudo systemctl start victoriametrics
sudo systemctl status victoriametrics
sudo systemctl enable victoriametrics
```
## Проверяем работу
```
http://192.168.1.103:8428
```

# Развертывание Prometheus
```
cd Prometheus
```
## Создать пользователя и подготовить каталоги для конфигурационных файлов и хранения данных
```
sudo useradd -M -u 1101 -s /bin/false prometheus
sudo mkdir -p /etc/prometheus/rule_files # каталог конфигурации
sudo mkdir -p /data/prometheus # каталог данных
sudo chown -R prometheus /etc/prometheus /data/prometheus
```

## Копируем
```
sudo cp prometheus.yml  /etc/prometheus
sudo cp main.yml /etc/prometheus/rule_files
sudo cp prometheus.service /etc/systemd/system
```

## Запускаем
```
sudo systemctl daemon-reload
sudo systemctl start prometheus
sudo systemctl status prometheus
sudo systemctl enable prometheus
```

## Проверяем
```
http://192.168.1.103:9090
```
- Status → Configuration
- Status → Rules
- Status → Targets 

# Развертывание Grafana
```
cd Grafana
```
## Создать пользователя и подготовить каталоги для конфигурационных файлов и хранения данных
```
sudo useradd -M -u 1102 -s /bin/false grafana
sudo mkdir -p /etc/grafana/provisioning/datasources # каталог декларативного описания источников данных
sudo mkdir /etc/grafana/provisioning/dashboards # каталог декларативного описания дашбордов
sudo mkdir -p /data/grafana/dashboards # каталог данных
sudo chown -R grafana /etc/grafana/ /data/grafana
```

## Копируем
```
sudo cp datasources/main.yml /etc/grafana/provisioning/datasources
sudo cp dashboards/main.yml /etc/grafana/provisioning/dashboards/
sudo cp grafana.service /etc/systemd/system
```

## Добавить дашборд Node Exporter Full в каталог /data/grafana/dashboards
```
cd ~/ && git clone https://github.com/rfmoz/grafana-dashboards
sudo cp grafana-dashboards/prometheus/node-exporter-full.json /data/grafana/dashboards/
```

## Запускаем
```
sudo systemctl daemon-reload
sudo systemctl start grafana
sudo systemctl status grafana
sudo systemctl enable grafana
```

## Проверяем
http://192.168.1.103:3000

admin/admin - > меняю на стандартный только числа

Configuration → Data sources
Explore → Metric → up → Run query
Dashboards → Browse → General → Node Exporter Full