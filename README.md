 # Домашнее задание к занятию "3.4. Операционные системы, лекция 2"

## 1. На лекции мы познакомились с [node_exporter](https://github.com/prometheus/node_exporter/releases). В демонстрации его исполняемый файл запускался в background. Этого достаточно для демо, но не для настоящей production-системы, где процессы должны находиться под внешним управлением. Используя знания из лекции по systemd, создайте самостоятельно простой [unit-файл](https://www.freedesktop.org/software/systemd/man/systemd.service.html) для node_exporter:

    * поместите его в автозагрузку,
    * предусмотрите возможность добавления опций к запускаемому процессу через внешний файл (посмотрите, например, на `systemctl cat cron`),
    * удостоверьтесь, что с помощью systemctl процесс корректно стартует, завершается, а после перезагрузки автоматически поднимается.

### Решение:
1. Подготавливаем node_exporter
	Скачиваем архив wget https://github.com/prometheus/node_exporter/releases/download/v1.3.1/node_exporter-1.3.1.linux-amd64.tar.gz
	Распаковываем в текущую папку 
	создаем папку sudo mkdir /opt/node_exporter с содержимым архива sudo cp -r ~/node_exporter-1.3.1.linux-amd64 /opt/
  
2.Создаем файл конфигурации сервера 

	touch /etc/systemd/system/node_exporter.service 
	sudo nano /etc/systemd/system/node_exporter.service
	
[Unit]
Description=Node Exporter

[Service]
ExecStart=/opt/node_exporter-1.3.1.linux-amd64/node_exporter $MY_OPTS
EnvironmentFile=/etc/default/node_exporter

[Install]
WantedBy=multi-user.target


	sudo touch /etc/default/node_exporter
	sudo nano /etc/default/node_exporter

MY_OPTS="--log.level=debug"

3. Добавляем сервис в автозагрузку systemctl enable node_exporter
4. Проверяем корректность старта и завершения при помощи команд systemctl start node_exporter, systemctl stop node_exporter, systemctl status node_exporter
5. Перезагружаем виртуальную машину и проверяем автозапуск службы


![img.png](https://github.com/nalevov/DO-Netology-3.4/blob/main/Node_exporter%201.png)


## 2. Ознакомьтесь с опциями node_exporter и выводом `/metrics` по-умолчанию. Приведите несколько опций, которые вы бы выбрали для базового мониторинга хоста по CPU, памяти, диску и сети.

### Решение:

Для просмотра метрик можно использовать команду curl localhost:9100/metrics

node_cpu
node_cpu_seconds_total{cpu="0",mode="idle"} 632.05
node_cpu_seconds_total{cpu="0",mode="iowait"} 1.94
node_cpu_seconds_total{cpu="0",mode="irq"} 0
node_cpu_seconds_total{cpu="0",mode="nice"} 0
node_cpu_seconds_total{cpu="0",mode="softirq"} 0.31
node_cpu_seconds_total{cpu="0",mode="steal"} 0
node_cpu_seconds_total{cpu="0",mode="system"} 4.02
node_cpu_seconds_total{cpu="0",mode="user"} 2.56
node_cpu_seconds_total{cpu="1",mode="idle"} 631.24
node_cpu_seconds_total{cpu="1",mode="iowait"} 2.56
node_cpu_seconds_total{cpu="1",mode="irq"} 0
node_cpu_seconds_total{cpu="1",mode="nice"} 0
node_cpu_seconds_total{cpu="1",mode="softirq"} 0.42
node_cpu_seconds_total{cpu="1",mode="steal"} 0
node_cpu_seconds_total{cpu="1",mode="system"} 4.3
node_cpu_seconds_total{cpu="1",mode="user"} 2.03

node_filesystem_avail_bytes{device="/dev/mapper/ubuntu--vg-ubuntu--lv",fstype="ext4",mountpoint="/"} 2.5531826176e+10
node_filesystem_avail_bytes{device="/dev/sda2",fstype="ext4",mountpoint="/boot"} 8.41621504e+08
node_filesystem_avail_bytes{device="tmpfs",fstype="tmpfs",mountpoint="/run"} 1.01900288e+08
node_filesystem_avail_bytes{device="tmpfs",fstype="tmpfs",mountpoint="/run/lock"} 5.24288e+06
node_filesystem_avail_bytes{device="tmpfs",fstype="tmpfs",mountpoint="/run/snapd/ns"} 1.01900288e+08
node_filesystem_avail_bytes{device="tmpfs",fstype="tmpfs",mountpoint="/run/user/1000"} 1.02866944e+08

node_network_dormant{device="eth0"} 0
node_network_dormant{device="lo"} 0

![img.png](https://github.com/nalevov/DO-Netology-3.4/blob/main/Node_exporter%202.png)



  
