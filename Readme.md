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
localhost:9090/metrics
http://localhost:9090/graph 
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
http://localhost:9100/metrics (node)
http://localhost:8080/metrics (cadvisor)
http://localhost:9115/metrics (blackbox)
http://localhost:9115/probe?target=github.com&module=http_2xx
http://localhost:9115/probe?target=github.com:443&module=tcp_connect

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
http://localhost:9093
http://localhost:9093/#/alerts
http://localhost:9093/#/status
```