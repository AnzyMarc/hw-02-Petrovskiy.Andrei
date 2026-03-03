# Домашнее задание к занятию "`Система мониторинга Zabbix`" - `Петровский Андрей`


### Задание 1 

Установите Zabbix Server с веб-интерфейсом.

#### Процесс выполнения
1. Выполняя ДЗ, сверяйтесь с процессом отражённым в записи лекции.
2. Установите PostgreSQL. Для установки достаточна та версия, что есть в системном репозитороии Debian 11.
3. Пользуясь конфигуратором команд с официального сайта, составьте набор команд для установки последней версии Zabbix с поддержкой PostgreSQL и Apache.
4. Выполните все необходимые команды для установки Zabbix Server и Zabbix Web Server.

#### Требования к результатам 
1. Прикрепите в файл README.md скриншот авторизации в админке.
2. Приложите в файл README.md текст использованных команд в GitHub.

----------------------------------------------------------




### Решение Задания 1: Установка Zabbix Server с веб-интерфейсом
В ходе выполнения домашнего задания был развернут стек мониторинга Zabbix с использованием контейнеризации (Docker Compose), что является современным стандартом развертывания, обеспечивающим изоляцию компонентов.
1. Используемые компоненты

    Операционная система: Ubuntu (в соответствии с выбранным YAML-конфигом).
    СУБД: PostgreSQL 17 (Alpine-based).
    Веб-сервер: Apache.
    Zabbix Version: 7.4-latest.

2. Список выполненных команд
Для установки и запуска системы использовались следующие команды в терминале:

```bash
# 1. Обновление системных пакетов и установка Docker Compose V2
sudo apt-get update
sudo apt-get install docker-compose-plugin

# 2. Клонирование официального репозитория Zabbix Docker
git clone https://github.com
cd zabbix-docker

# 3. Остановка и очистка старых контейнеров (если были)
sudo docker compose -f docker-compose_v3_ubuntu_pgsql_latest.yaml down

# 4. Запуск стека Zabbix с PostgreSQL и Apache
# Мы выбираем конкретные сервисы из общего шаблона для экономии ресурсов
sudo docker compose -f docker-compose_v3_ubuntu_pgsql_latest.yaml up -d \
  postgres-server \
  server-db-init \
  zabbix-server \
  zabbix-web-apache-pgsql \
  zabbix-agent

# 5. Проверка статуса запущенных контейнеров
sudo docker ps
```
3. Скриншот авторизации в админке
Ниже представлен скриншот окна авторизации в веб-интерфейсе Zabbix (доступен по порту 8081).

<img src="img/Autorization.png" alt="авторизация" width="1000">



### Задание 2 

Установите Zabbix Agent на два хоста.

#### Процесс выполнения
1. Выполняя ДЗ, сверяйтесь с процессом отражённым в записи лекции.
2. Установите Zabbix Agent на 2 вирт.машины, одной из них может быть ваш Zabbix Server.
3. Добавьте Zabbix Server в список разрешенных серверов ваших Zabbix Agentов.
4. Добавьте Zabbix Agentов в раздел Configuration > Hosts вашего Zabbix Servera.
5. Проверьте, что в разделе Latest Data начали появляться данные с добавленных агентов.

#### Требования к результатам
1. Приложите в файл README.md скриншот раздела Configuration > Hosts, где видно, что агенты подключены к серверу
2. Приложите в файл README.md скриншот лога zabbix agent, где видно, что он работает с сервером
3. Приложите в файл README.md скриншот раздела Monitoring > Latest data для обоих хостов, где видны поступающие от агентов данные.
4. Приложите в файл README.md текст использованных команд в GitHub

-------------------------------------

### Решение

Установка Zabbix Agent на два хоста

Агенты были установлены на две виртуальные машины: основную (где работает сервер) и дополнительную (v2-agent).
1. Решение проблемы с правами доступа (Access Permissions)

При добавлении хостов возникла ошибка «Received empty response from Zabbix Agent». Проблема была решена путем корректировки конфигурации агента:

    В интерфейсе Zabbix адрес 127.0.0.1 был заменен на внешний IP сервера.

    В файле конфигурации агента /etc/zabbix/zabbix_agentd.conf в параметре Server были добавлены внешний IP сервера и подсеть Docker, чтобы агент принимал запросы от сервера, работающего в контейнере.
2. Текст использованных команд    
```bash

# Установка агента из официального репозитория Zabbix
wget https://repo.zabbix.com/zabbix/7.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_7.0-2+ubuntu24.04_all.deb
sudo dpkg -i zabbix-release_7.0-2+ubuntu24.04_all.deb
sudo apt update && sudo apt install zabbix-agent -y

# Настройка доступа в конфиге
# Отредактирована строка: Server=127.0.0.1,158.160.74.111
sudo nano /etc/zabbix/zabbix_agentd.conf

# Перезапуск службы
sudo systemctl restart zabbix-agent

```

## Screnshots

Web
<img src="img/web-zabbix.png" alt="web" width="1000">

Server log
<img src="img/vm_server_log.png" alt="server log" width="1000">

Host log

<img src="img/vm_log.png" alt="host log" width="1000">

Latest Data

<img src="img/Latest Data.png" alt="latest data" width="1000">


