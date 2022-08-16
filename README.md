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
  
2. Создаем файл конфигурации сервера, при помощи команд 

`touch /etc/systemd/system/node_exporter.service`

`sudo nano /etc/systemd/system/node_exporter.service`

Прописываем

```
[Unit]
Description=Node Exporter

[Service]
ExecStart=/opt/node_exporter-1.3.1.linux-amd64/node_exporter $MY_OPTS
EnvironmentFile=/etc/default/node_exporter

[Install]
WantedBy=multi-user.target

```

`sudo touch /etc/default/node_exporter`

`sudo nano /etc/default/node_exporter`

Прописываем:

```
MY_OPTS="--log.level=debug"
```


3. Добавляем сервис в автозагрузку systemctl enable node_exporter
4. Проверяем корректность старта и завершения при помощи команд systemctl start node_exporter, systemctl stop node_exporter, systemctl status node_exporter
5. Перезагружаем виртуальную машину и проверяем автозапуск службы


![img.png](https://github.com/nalevov/DO-Netology-3.4/blob/main/Node_exporter%201.png)


## 2. Ознакомьтесь с опциями node_exporter и выводом `/metrics` по-умолчанию. Приведите несколько опций, которые вы бы выбрали для базового мониторинга хоста по CPU, памяти, диску и сети.

### Решение:

Для просмотра метрик можно использовать команду curl localhost:9100/metrics

```
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

```

![img.png](https://github.com/nalevov/DO-Netology-3.4/blob/main/Node_exporter%202.png)


## 3. Установите в свою виртуальную машину [Netdata](https://github.com/netdata/netdata). Воспользуйтесь [готовыми пакетами](https://packagecloud.io/netdata/netdata/install) для установки (`sudo apt install -y netdata`). После успешной установки:
    * в конфигурационном файле `/etc/netdata/netdata.conf` в секции [web] замените значение с localhost на `bind to = 0.0.0.0`,
    * добавьте в Vagrantfile проброс порта Netdata на свой локальный компьютер и сделайте `vagrant reload`:

    ```bash
    config.vm.network "forwarded_port", guest: 19999, host: 19999
    ```

    После успешной перезагрузки в браузере *на своем ПК* (не в виртуальной машине) вы должны суметь зайти на `localhost:19999`. Ознакомьтесь с метриками, которые по умолчанию собираются Netdata и с комментариями, которые даны к этим метрикам.
    
### Решение:   

![img.png](https://github.com/nalevov/DO-Netology-3.4/blob/main/Netdata.png)


## 4. Можно ли по выводу `dmesg` понять, осознает ли ОС, что загружена не на настоящем оборудовании, а на системе виртуализации?

### Решение:   

Да, по вывроду команды `dmesg |grep virt` видим, что ОС запущена в виртуальной среде

```
[    0.001392] CPU MTRRs all blank - virtualized system.
[    0.081340] Booting paravirtualized kernel on KVM
[    3.683590] systemd[1]: Detected virtualization oracle.
```


## 5. Как настроен sysctl `fs.nr_open` на системе по-умолчанию? Узнайте, что означает этот параметр. Какой другой существующий лимит не позволит достичь такого числа (`ulimit --help`)?

### Решение:

При помоща команды `sysctl -n fs.nr_open` видим, что по умолчанию настроен на 1048576. Данный параметр отвечает за максимальное количество открытых дескрипторов. 
Лимит открытых дескрипторов на пользователя (ulimit -n) не позволит достичь этого числа. 


## 6. Запустите любой долгоживущий процесс (не `ls`, который отработает мгновенно, а, например, `sleep 1h`) в отдельном неймспейсе процессов; покажите, что ваш процесс работает под PID 1 через `nsenter`. Для простоты работайте в данном задании под root (`sudo -i`). Под обычным пользователем требуются дополнительные опции (`--map-root-user`) и т.д.

### Решение:

`sleep 1h`

```
^Z
[1]+  Stopped                 sleep 1h
```

`bg 1`

```
1]+ sleep 1h &
```

`ps -aux | grep sleep`

```
vagrant     1661  0.0  0.0   5476   592 pts/0    S    11:40   0:00 sleep 1h
vagrant     1663  0.0  0.0   6432   664 pts/0    S+   11:41   0:00 grep --color=auto sleep
```

`sudo nsenter --target 1661 --pid --mount`

```
PID TTY          TIME CMD
1669 pts/0    00:00:00 sudo
1670 pts/0    00:00:00 nsenter
1671 pts/0    00:00:00 bash
1690 pts/0    00:00:00 ps
```


## 7. Найдите информацию о том, что такое `:(){ :|:& };:`. Запустите эту команду в своей виртуальной машине Vagrant с Ubuntu 20.04 (**это важно, поведение в других ОС не проверялось**). Некоторое время все будет "плохо", после чего (минуты) – ОС должна стабилизироваться. Вызов `dmesg` расскажет, какой механизм помог автоматической стабилизации. Как настроен этот механизм по-умолчанию, и как изменить число процессов, которое можно создать в сессии?

### Решение:

Cоздает функцию, которая уходит в фон и создает саму себя снова, получается бесконечная рекурсия с порождением все новых и новых процессов.
Стабилизации помог механизм [ 2576.214212] cgroup: fork rejected by pids controller in /user.slice/user-1000.slice/session-4.scope.
система на основании этих файлов в пользовательской зоне ресурсов имеет определенное ограничение на создаваемые ресурсы и при превышении начинает блокировать создание числа.
Изменить число процессов, которые можно создать в сессии,  возможно командой ulimit -u <pid-count> 
