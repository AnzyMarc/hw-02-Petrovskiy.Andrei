# Домашнее задание к занятию "`Домашнее задание к занятию 2 «Кластеризация и балансировка нагрузки»" - `Петровский Андрей`


### Задание 1
- Запустите два simple python сервера на своей виртуальной машине на разных портах
- Установите и настройте HAProxy, воспользуйтесь материалами к лекции по [ссылке](2/)
- Настройте балансировку Round-robin на 4 уровне.
- На проверку направьте конфигурационный файл haproxy, скриншоты, где видно перенаправление запросов на разные серверы при обращении к HAProxy.



### Решение : Балансировка Round-robin на 4 уровне (L4)
Описание:

Настроена балансировка TCP-трафика между двумя Python-серверами. HAProxy перенаправляет запросы по очереди на каждый сервер, не анализируя содержимое пакетов.

- Порт HAProxy: 8088

 - Сервер 1: 127.0.0.1:8888

 - Сервер 2: 127.0.0.1:9999

 - Режим: mode tcp

 - Алгоритм: roundrobin

 конфигурация
```bash
listen stats  # веб-страница со статистикой
        bind                    :888
        mode                    http
        stats                   enable
        stats uri               /stats
        stats refresh           5s
        stats realm             Haproxy\ Statistics

frontend example  # секция фронтенд
        mode tcp
        bind :8088
        default_backend web_servers



backend web_servers    # секция бэкенд
        mode tcp
        balance roundrobin
        option httpchk
        http-check send meth GET uri /index.html
        server s1 127.0.0.1:8888 check
        server s2 127.0.0.1:9999 check
``` 

<img src="./img/HAProxy_check.png" alt="out terminal" width="1000"/>
