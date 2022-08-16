 Домашнее задание к занятию "3.4. Операционные системы, лекция 2"

1. На лекции мы познакомились с [node_exporter](https://github.com/prometheus/node_exporter/releases). В демонстрации его исполняемый файл запускался в background. Этого достаточно для демо, но не для настоящей production-системы, где процессы должны находиться под внешним управлением. Используя знания из лекции по systemd, создайте самостоятельно простой [unit-файл](https://www.freedesktop.org/software/systemd/man/systemd.service.html) для node_exporter:

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

![img.png] (https://github.com/nalevov/DO-Netology-3.4/blob/main/Node_exporter%201.png)



  
