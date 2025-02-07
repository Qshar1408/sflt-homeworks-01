# Домашнее задание к занятию 1 «Disaster recovery и Keepalived»
## Грибанов Антон. FOPS-31

### Задание 1
- Дана [схема](1/hsrp_advanced.pkt) для Cisco Packet Tracer, рассматриваемая в лекции.
- На данной схеме уже настроено отслеживание интерфейсов маршрутизаторов Gi0/1 (для нулевой группы)
- Необходимо аналогично настроить отслеживание состояния интерфейсов Gi0/0 (для первой группы).
- Для проверки корректности настройки, разорвите один из кабелей между одним из маршрутизаторов и Switch0 и запустите ping между PC0 и Server0.
- На проверку отправьте получившуюся схему в формате pkt и скриншот, где виден процесс настройки маршрутизатора.
- 
![Keepalive_001](https://github.com/Qshar1408/sflt-homeworks-01/blob/main/img/sflt01_001.png)
![Keepalive_002](https://github.com/Qshar1408/sflt-homeworks-01/blob/main/img/sflt01_002.png)
![Keepalive_003](https://github.com/Qshar1408/sflt-homeworks-01/blob/main/img/sflt01_003.png)
![Keepalive_004](https://github.com/Qshar1408/sflt-homeworks-01/blob/main/img/sflt01_004.png)
#### Схема pkt: [hsrp_advanced_gribanovav.pkt](https://github.com/Qshar1408/sflt-homeworks-01/blob/main/files/hsrp_advanced_gribanovav.pkt)

------


### Задание 2
- Запустите две виртуальные машины Linux, установите и настройте сервис Keepalived как в лекции, используя пример конфигурационного [файла](1/keepalived-simple.conf).
- Настройте любой веб-сервер (например, nginx или simple python server) на двух виртуальных машинах
- Напишите Bash-скрипт, который будет проверять доступность порта данного веб-сервера и существование файла index.html в root-директории данного веб-сервера.
- Настройте Keepalived так, чтобы он запускал данный скрипт каждые 3 секунды и переносил виртуальный IP на другой сервер, если bash-скрипт завершался с кодом, отличным от нуля (то есть порт веб-сервера был недоступен или отсутствовал index.html). Используйте для этого секцию vrrp_script
- На проверку отправьте получившейся bash-скрипт и конфигурационный файл keepalived, а также скриншот с демонстрацией переезда плавающего ip на другой сервер в случае недоступности порта или файла index.html

#### Bash-скрипт: [check_server.sh](https://github.com/Qshar1408/sflt-homeworks-01/blob/main/files/check_server.sh)
```bash
#!/bin/bash
if [[ $(netstat -ant | grep LISTEN | grep :80) ]] && [[ -f /var/www/html/index.nginx-debian.html ]]; then
  exit 0
else
  sudo systemctl stop keepalived
fi
```

#### Конфигурационный файл MASTER: [keepalived_22.conf](https://github.com/Qshar1408/sflt-homeworks-01/blob/main/files/keepalived_22.conf)
```bash
vrrp_script check_server {
        script "/home/qshar/check_server.sh"
        interval 3
}

vrrp_instance VI_1 {
        state MASTER
        interface enp0s3
        virtual_router_id 15
        priority 255
        advert_int 1

        virtual_ipaddress {
                192.168.1.22/24
        }

        track_script {
                check_server
        }

}
```

#### Конфигурационный файл BACKUP: [keepalived_23.conf](https://github.com/Qshar1408/sflt-homeworks-01/blob/main/files/keepalived_23.conf)
```bash
vrrp_instance VI_1 {
	state BACKUP
	interface enp0s3
	virtual_router_id 15
	priority 155
	advert_int 1

	virtual_ipaddress {
		192.168.1.23/24
	}

}
```

![Keepalive_005](https://github.com/Qshar1408/sflt-homeworks-01/blob/main/img/sflt01_005.png)
![Keepalive_006](https://github.com/Qshar1408/sflt-homeworks-01/blob/main/img/sflt01_006.png)
![Keepalive_007](https://github.com/Qshar1408/sflt-homeworks-01/blob/main/img/sflt01_007.png)
![Keepalive_008](https://github.com/Qshar1408/sflt-homeworks-01/blob/main/img/sflt01_008.png)
![Keepalive_009](https://github.com/Qshar1408/sflt-homeworks-01/blob/main/img/sflt01_009.png)
![Keepalive_010](https://github.com/Qshar1408/sflt-homeworks-01/blob/main/img/sflt01_010.png)
![Keepalive_011](https://github.com/Qshar1408/sflt-homeworks-01/blob/main/img/sflt01_011.png)
![Keepalive_012](https://github.com/Qshar1408/sflt-homeworks-01/blob/main/img/sflt01_012.png)
![Keepalive_013](https://github.com/Qshar1408/sflt-homeworks-01/blob/main/img/sflt01_013.png)
------


