# `Домашнее задание к занятию: "10.5 Балансировка нагрузки. HAProxy/Nginx."` - `Болдыш Леонид`
### Задание 1

- `Балансировкой нагрузки в зависимости от уровня абстракции и среды обычно называют процесс при котором трафик (пакеты) или вычислительные задачи распределяются между некоторыми определенными устройствами. Основным инетересом применения данный технологии с точки зрения бизнеса, явл. невозможность предсказать загруженность серверов в часы пикивой нагрузки (в частности для новых проектов). В случае недостаточной производительности сервера в связи ростом нагрузок и увеличения потока клиентов, решать данные проблемы возможно двумя способами: путем наращивания мощности сервера, или оптимизацией. Т.к. наращивание мощност требует значительного количества ресурсов, проще оптимизировать работу имеющейся инфраструктуры.`

### Задание 2

- `Round-robin - это алгоритм поочередного распределения нагрузки от сервера к серверу`
- `Weighted round-robin - это алгоритм поочередного распределения нагрузки аналогичный RR, при котором учитывается производительность каждого сервера, что позволяет установить фиксированное соотношение/количество пакетов поступающих на конкретный сервер. Применение данного алгоритма имеет смысл в случаях, когда производительность одного из сервверов в составе кластера значительно ниже другого, что не позволяет ему эффективно решать аналогичное количество задач.`

### Задание 3

![HAproxy](https://github.com/themave-tech/Netology-sys/blob/main/sys-homework-10.05/img/Screenshot_20221221_041337.png)

### Задание 4

![Nginx](https://github.com/themave-tech/Netology-sys/blob/main/sys-homework-10.05/img/Screenshot_20221221_041856.png)

### Задание 5

![Nginx-core](https://github.com/themave-tech/Netology-sys/blob/main/sys-homework-10.05/img/Screenshot_20221226_090137.png)

`Конфигурация`

```
server {
        listen 80 default_server;
        listen [::]:80 default_server;
        root /var/www/html;
        index index.html index.htm index.nginx-debian.html;
        server_name www.test.com;
        location / {
                try_files $uri $uri/ =404;
        }
        location /ping {
#               error_page 404 /404.html;
                return 200 'nginx is configured correctly';
        }
}

server {
        listen 8088;
        listen [::]:8088;
        server_name test.com;
        root /var/www/html;
        index index.html index.htm index.nginx-debian.html;
        location / {
                try_files $uri $uri/ =404;
        }
        location /ping {
#                error_page 404 /404.html;
                return 200 'nginx is configured correctly';
        }
}

```
