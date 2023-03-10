# devops-DZ10-monitoring-01-base
# Домашнее задание к занятию "13.Системы мониторинга"

 

## Обязательные задания

 

1. Вас пригласили настроить мониторинг на проект. На онбординге вам рассказали, что проект представляет из себя

платформу для вычислений с выдачей текстовых отчетов, которые сохраняются на диск. Взаимодействие с платформой

осуществляется по протоколу http. Также вам отметили, что вычисления загружают ЦПУ. Какой минимальный набор метрик вы

выведите в мониторинг и почему?

 

### Решение

 

- мониторинг OS

  - CPU:

    - Load Average (1 min, 5 min, 15 min)

    - Total (%)

    - User (%)

    - Wait(%)

  - MEMORY:

    - Usage (Gb, %)

    - Pages In/Out (pages/sec, %/h)

  - STORAGE:

    - объем данных (Gb, %)

    - скорость записи/чтения

    - inodes

  - сеть:

    - загруженность интерфейсов (bytes in/out, errors in/out, %)

- мониторинг уровня сервисов (БД, web-серверы, ...)

  - доступность

  - загруженность

  - наличие ошибок

  - время отклика

- мониторинг уровня приложения:

  - HTTP-запросы:

    - общее количество запросов

    - количество ошибочных запросов

    - время выполнения запросов

  - операции обработки данных:

    - количество

    - статус

    - время исполнения

 

 

2. Менеджер продукта посмотрев на ваши метрики сказал, что ему непонятно что такое RAM/inodes/CPUla. Также он сказал,

что хочет понимать, насколько мы выполняем свои обязанности перед клиентами и какое качество обслуживания. Что вы

можете ему предложить?

 

### Решение

 

Необходимо уточнить, какой SLA декларировался. Затем привести собранные метрики к задекларированным KPI. Например:

 

- доступность системы %;

- время выполнения запрошенной операции (min, max, average)

- допустимый % ошибок

 

3. Вашей DevOps команде в этом году не выделили финансирование на построение системы сбора логов. Разработчики в свою

очередь хотят видеть все ошибки, которые выдают их приложения. Какое решение вы можете предпринять в этой ситуации,

чтобы разработчики получали ошибки приложения?

 

### Решение

 

Как минимум, настроить мориторинг на базе свободного ПО: стек ELK, Zabbix, Grafana и тд.

Для минимизации затрат на используемые ресурсы, реализуем урезанную схему сбора логов. Например,

- собираем только записи с severity:ERROR или FATAL

- собираем ошибки с минимального количества приложений

- храним логи минимально необходимый срок

Такая схема позволит отлавливать определенный процент ошибок, и позволит масштабировать схему до полноценной, когда на это будут выделены ресурсы.

 

Можно предложить переложить эту задачу на бюджет разработки, чтобы переделать код так, чтобы он выдавал не только ошибку приложения, но и весь необходимый контекст из других связанных сервисов.

 

4. Вы, как опытный SRE, сделали мониторинг, куда вывели отображения выполнения SLA=99% по http кодам ответов.

Вычисляете этот параметр по следующей формуле: summ_2xx_requests/summ_all_requests. Данный параметр не поднимается выше

70%, но при этом в вашей системе нет кодов ответа 5xx и 4xx. Где у вас ошибка?

 

### Решение

Не учтены коды ответа: редиректы (3хх), информационные (1хх), их можно учитывать как успешные (не ошибочные).

Правильная формула расчёта **(summ_1xx_requests + summ_2xx_requests + summ_3xx_requests)/summ_all_requests**.

 

5. Опишите основные плюсы и минусы pull и push систем мониторинга.

 

### Решение

 

### Push

 

- Плюсы:

  - Можно получать данные при нестабильных каналых связи и/или периодической доступности объекта мониторинга

  - Можно отправлять данные с одного объекта на несколько систем мониторинга

  - Отдельная или независимая настройка конфигурации узла каждого объекта мониторинга

 

- Минусы:

  - Нет централизованного управления конфигурацией объектов мониторинга, каждый объект настраивается независимо

  - Высокая вероятность возникновения очереди сообщений или большого числа идентичных сообщений

  - Объект мониторинга не имеет обратной связи от системы мониторинга

 

### Pull

 

- Плюсы:

  - Использование привычных WEB протоколов (http/https)

  - Централизованное управление

  - Гибкий механизм опроса объекта мониторинга

  - Наличие механизмов повышения безопасности: шифрование канала сообщений, применение прокси-узлов

 

- Минусы:

  - Выше требования к ресурсам и надежности системы мониторинга

  - Выше утилизация сети - обмен информацией происходит в обе стороны

 

6. Какие из ниже перечисленных систем относятся к push модели, а какие к pull? А может есть гибридные?

 

    - Prometheus

    - TICK

    - Zabbix

    - VictoriaMetrics

    - Nagios

 

### Решение

 

- Гибридные (Push/Pull)

    - Zabbix

    - VictoriaMetrics

- Push

    - TICK

- Pull

    - Prometheus

    - Nagios

 

7. Склонируйте себе [репозиторий](https://github.com/influxdata/sandbox/tree/master) и запустите TICK-стэк,

используя технологии docker и docker-compose.

 

В виде решения на это упражнение приведите выводы команд с вашего компьютера (виртуальной машины):

 

    - curl http://localhost:8086/ping

    - curl http://localhost:8888

    - curl http://localhost:9092/kapacitor/v1/ping

 

А также скриншот веб-интерфейса ПО chronograf (`http://localhost:8888`).

 

P.S.: если при запуске некоторые контейнеры будут падать с ошибкой - проставьте им режим `Z`, например

`./data:/var/lib:Z`

 

### Решение

- Клонируем репозиторий
```bash
root@vkvm:/home/vk# git clone https://github.com/influxdata/sandbox.git TICK
Cloning into 'TICK'...
remote: Enumerating objects: 1718, done.
remote: Counting objects: 100% (32/32), done.
remote: Compressing objects: 100% (22/22), done.
remote: Total 1718 (delta 13), reused 25 (delta 10), pack-reused 1686
Receiving objects: 100% (1718/1718), 7.17 MiB | 171.00 KiB/s, done.
Resolving deltas: 100% (946/946), done.
```
- Запускаем
```bash
root@vkvm:/home/vk/TICK# ./sandbox up
Using latest, stable releases
Spinning up Docker Images...
If this is your first time starting sandbox this might take a minute...
/snap/docker/2343/lib/python3.6/site-packages/paramiko/transport.py:33: CryptographyDeprecationWarning: Python 3.6 is no longer supported by the Python core team. Therefore, support for it is deprecated in cryptography and will be removed in a future release.
  from cryptography.hazmat.backends import default_backend
Creating network "tick_default" with the default driver
Building influxdb
..........................................
Creating tick_documentation_1 ... done
Creating tick_influxdb_1      ... done
Creating tick_telegraf_1      ... done
Creating tick_kapacitor_1     ... done
Creating tick_chronograf_1    ... done

```
- Вывод команд curl
```bash
root@vkvm:/home/vk/TICK# curl http://localhost:8086/ping
root@vkvm:/home/vk/TICK# curl http://localhost:8888
<!DOCTYPE html><html><head><link rel="stylesheet" href="/index.c708214f.css"><meta http-equiv="Content-type" content="text/html; charset=utf-8"><title>Chronograf</title><link rel="icon shortcut" href="/favicon.70d63073.ico"></head><body> <div id="react-root" data-basepath=""></div> <script type="module" src="/index.e81b88ee.js"></script><script src="/index.a6955a67.js" nomodule="" defer></script> </body></html>root@vkvm:/home/vk/TICK# 
root@vkvm:/home/vk/TICK# curl http://localhost:9092/kapacitor/v1/ping
root@vkvm:/home/vk/TICK# 
```
- Cкриншот веб-интерфейса ПО chronograf (`http://localhost:8888`).
![Chronograf](1.png)

8. Перейдите в веб-интерфейс Chronograf (`http://localhost:8888`) и откройте вкладку `Data explorer`.

 

    - Нажмите на кнопку `Add a query`

    - Изучите вывод интерфейса и выберите БД `telegraf.autogen`

    - В `measurments` выберите mem->host->telegraf_container_id , а в `fields` выберите used_percent.

    Внизу появится график утилизации оперативной памяти в контейнере telegraf.

    - Вверху вы можете увидеть запрос, аналогичный SQL-синтаксису.

    Поэкспериментируйте с запросом, попробуйте изменить группировку и интервал наблюдений.

 

Для выполнения задания приведите скриншот с отображением метрик утилизации места на диске

(disk->host->telegraf_container_id) из веб-интерфейса.

 

### Решение
- Добавим в конфигурацию `telegraf/telegraf.conf` поддержку disk и mem
    - Память - `[[inputs.mem]]`
    - Диск
        ```
        [[inputs.disk]]
          ignore_fs = ["tmpfs", "devtmpfs", "devfs", "iso9660", "overlay", "aufs", "squashfs"]
        ```
- Перезапустим telegraf для применения настроек
```bash
root@vkvm:/home/vk/TICK# ./sandbox restart
Using latest, stable releases
Stopping all sandbox processes...
Starting all sandbox processes...
Services available!
```

- Скриншот использования диска
![Chronograf](2.png)

 

9. Изучите список [telegraf inputs](https://github.com/influxdata/telegraf/tree/master/plugins/inputs).

Добавьте в конфигурацию telegraf следующий плагин - [docker](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/docker):

 

```

[[inputs.docker]]

  endpoint = "unix:///var/run/docker.sock"

```

 

Дополнительно вам может потребоваться донастройка контейнера telegraf в `docker-compose.yml` дополнительного volume и

режима privileged:

 

```

  telegraf:

    image: telegraf:1.4.0

    privileged: true

    volumes:

      - ./etc/telegraf.conf:/etc/telegraf/telegraf.conf:Z

      - /var/run/docker.sock:/var/run/docker.sock:Z

    links:

      - influxdb

    ports:

      - "8092:8092/udp"

      - "8094:8094"

      - "8125:8125/udp"

```

 

После настройке перезапустите telegraf, обновите веб интерфейс и приведите скриншотом список `measurments` в

веб-интерфейсе базы telegraf.autogen . Там должны появиться метрики, связанные с docker.

 

Факультативно можете изучить какие метрики собирает telegraf после выполнения данного задания.

 ### Ответ
 
 - Скриншот использования Docker
![Chronograf](3.png)

## Дополнительное задание (со звездочкой*) - необязательно к выполнению

 

1. Вы устроились на работу в стартап. На данный момент у вас нет возможности развернуть полноценную систему

мониторинга, и вы решили самостоятельно написать простой python3-скрипт для сбора основных метрик сервера. Вы, как

опытный системный-администратор, знаете, что системная информация сервера лежит в директории `/proc`.

Также, вы знаете, что в системе Linux есть  планировщик задач cron, который может запускать задачи по расписанию.

 

Суммировав все, вы спроектировали приложение, которое:

 

- является python3 скриптом

- собирает метрики из папки `/proc`

- складывает метрики в файл 'YY-MM-DD-awesome-monitoring.log' в директорию /var/log

(YY - год, MM - месяц, DD - день)

- каждый сбор метрик складывается в виде json-строки, в виде:

  - timestamp (временная метка, int, unixtimestamp)

  - metric_1 (метрика 1)

  - metric_2 (метрика 2)

 

     ...

 

  - metric_N (метрика N)

 

- сбор метрик происходит каждую 1 минуту по cron-расписанию

 

Для успешного выполнения задания нужно привести:

 

а) работающий код python3-скрипта,

 

б) конфигурацию cron-расписания,

 

в) пример верно сформированного 'YY-MM-DD-awesome-monitoring.log', имеющий не менее 5 записей,

 

P.S.: количество собираемых метрик должно быть не менее 4-х.

P.P.S.: по желанию можно себя не ограничивать только сбором метрик из `/proc`.

 

2. В веб-интерфейсе откройте вкладку `Dashboards`. Попробуйте создать свой dashboard с отображением:

 

    - утилизации ЦПУ

    - количества использованного RAM

    - утилизации пространства на дисках

    - количество поднятых контейнеров

    - аптайм

    - ...

    - фантазируйте)

 

    ---

 

### Как оформить ДЗ?

 

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

 

---
