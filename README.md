# Домашнее задание к занятию  «Очереди RabbitMQ» - Савельев Алексей SYS-25

---

### Задание 1. Установка RabbitMQ

Используя Vagrant или VirtualBox, создайте виртуальную машину и установите RabbitMQ.
Добавьте management plug-in и зайдите в веб-интерфейс.

*Итогом выполнения домашнего задания будет приложенный скриншот веб-интерфейса RabbitMQ.*

---
### Ответ 1.

- При решение данной задачи я использовал [материал](https://losst.pro/ustanovka-rabbitmq-v-ubuntu-20-04) со стороннего сайта. На данном сайте инструкция для установки на `Ubuntu 20.04` но у меня установлена версия 22.04 и в некоторых пунктах пришлось применить некоторые изменения. Дальше идёт инструкция для установки на `Ubuntu 22.04` с внесёнными мной изменениями.
---
### Что я делал:Установка RabbitMQ в Ubuntu 22.04

1. Добавление ключей в систему:
```bash
sudo apt install curl gnupg -y
```
2. Для добавления ключей выполнил такие команды:
```bash
curl -1sLf "https://keys.openpgp.org/vks/v1/by-fingerprint/0A9AF2115F4687BD29803A206B73A36E6026DFCA" | sudo gpg --dearmor | sudo tee /usr/share/keyrings/com.rabbitmq.team.gpg > /dev/null
```
```bash
curl -1sLf https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-erlang/gpg.E495BB49CC4BBE5B.key | sudo gpg --dearmor | sudo tee /usr/share/keyrings/io.cloudsmith.rabbitmq.E495BB49CC4BBE5B.gpg > /dev/null
```
```bash
curl -1sLf https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-server/gpg.9F4587F226208342.key | sudo gpg --dearmor | sudo tee /usr/share/keyrings/io.cloudsmith.rabbitmq.9F4587F226208342.gpg > /dev/null
```
2. Добавление репозиториев:
- Для работы с репозиториями необходимо установить пакет apt-transport-https:
```bash
sudo apt install apt-transport-https
```
- Создал файл /etc/apt/sources.list.d/rabbitmq.list и добавил в него сначала репозитории Erlang:
```bash
sudo nano /etc/apt/sources.list.d/rabbitmq.list
```
```bash
deb [signed-by=/usr/share/keyrings/io.cloudsmith.rabbitmq.E495BB49CC4BBE5B.gpg] https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-erlang/deb/ubuntu jummy main
deb-src [signed-by=/usr/share/keyrings/io.cloudsmith.rabbitmq.E495BB49CC4BBE5B.gpg] https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-erlang/deb/ubuntu jummy main
```
- Затем репозитории RabbitMQ:
```bash
deb [signed-by=/usr/share/keyrings/io.cloudsmith.rabbitmq.9F4587F226208342.gpg] https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-server/deb/ubuntu jummy main
deb-src [signed-by=/usr/share/keyrings/io.cloudsmith.rabbitmq.9F4587F226208342.gpg] https://dl.cloudsmith.io/public/rabbitmq/rabbitmq-server/deb/ubuntu jummy main
```
- После сохранения изменений обновляю списки пакетов в репозиториях:
```bash
sudo apt update -y
```
3. Установка erlang:
- Для установки всех необходимых пакетов erlang выполнил:
```bash
sudo apt install -y erlang-base \
erlang-asn1 erlang-crypto erlang-eldap erlang-ftp erlang-inets \
erlang-mnesia erlang-os-mon erlang-parsetools erlang-public-key \
erlang-runtime-tools erlang-snmp erlang-ssl \
erlang-syntax-tools erlang-tftp erlang-tools erlang-xmerl
```
- После установки можно убедится что всё прошло успешно выполнив:
```bash
erl
```
- Для того чтобы выйти из консоли нажал Ctrl+C.
- Затем проверил что сервер запущен и работает:
```bash
sudo systemctl status rabbitmq-server
```
5. Веб интерфейс:
- Мне нужен доступ к веб-интерфейсу RabbitMQ и его необходимо включить. Для этого выполнил:
```bash
sudo rabbitmq-plugins enable rabbitmq_management
```
- Получить доступ к веб-интерфейсу можно по адресу http://адрес_сервера:15672, для localhost это будет http://localhost:15672. По умолчанию логин и пароль для доступа - guest:guest. Но эти данные доступа работают только для localhost. Если необходимо подключиться удалённо, то нужно создать нового пользователя. Я для теста создал пользователя admin:
```bash
sudo rabbitmqctl add_user admin сложный_пароль
```
- Для того чтобы сделать только что созданного пользователя администратором выполнил такую команду:
```bash
sudo rabbitmqctl set_user_tags admin administrator
```
- Выдаю пользователю доступ к RabbitMQ:
```bash
sudo rabbitmqctl set_permissions -p / admin ".*" ".*" ".*"
```
- После завершения настройки авторизуюсь в веб-интерфейсе программы от имени только что созданного пользователя:
![overview_abc](https://github.com/Lexacbr/rabbitmq/blob/main/scrsh/abc-owerview.png)
![abc_queue](https://github.com/Lexacbr/rabbitmq/blob/main/scrsh/abc-queue.png)
---
### Задание 2. Отправка и получение сообщений

Используя приложенные скрипты, проведите тестовую отправку и получение сообщения.
Для отправки сообщений необходимо запустить скрипт producer.py.

Для работы скриптов вам необходимо установить Python версии 3 и библиотеку Pika.
Также в скриптах нужно указать IP-адрес машины, на которой запущен RabbitMQ, заменив localhost на нужный IP.

```shell script
$ pip install pika
```

Зайдите в веб-интерфейс, найдите очередь под названием hello и сделайте скриншот.
После чего запустите второй скрипт consumer.py и сделайте скриншот результата выполнения скрипта

*В качестве решения домашнего задания приложите оба скриншота, сделанных на этапе выполнения.*

Для закрепления материала можете попробовать модифицировать скрипты, чтобы поменять название очереди и отправляемое сообщение.

---
### Ответ 2.
---
- Для установки `python3` на ВМ я пользовался подсказками из [этой статьи](https://www.digitalocean.com/community/tutorials/how-to-install-python-3-and-set-up-a-programming-environment-on-an-ubuntu-20-04-server-ru)
- Так же установил библиотеку `pika` предложеным способом:
```bash 
pip install pika
```
- Модифицировал немного скрипты `producer`... 
```py
#!/usr/bin/env python
# coding=utf-8
import pika

parameters = pika.URLParameters('amqp://admin:adminpass@localhost:5672/')
connection = pika.BlockingConnection(parameters)
channel = connection.channel()

channel.queue_declare(queue='hello')

if __name__ == '__main__':
    
    count = 0

    while True:
        channel.basic_publish(
            exchange='', 
            routing_key='hello', 
            body=f'Hello, People! - {count}',
            )
        count += 1
```
- ...`consumer`
```py
#!/usr/bin/env python
# coding=utf-8
import pika

parameters = pika.URLParameters('amqp://admin:adminpass@localhost:5672/')
connection = pika.BlockingConnection(parameters)
channel = connection.channel()

channel.queue_declare(queue='hello')

def callback(ch, method, properties, body) -> None:
    #print( ch, method, properties, body)
    print(body)
    
channel.basic_consume(
    queue='hello', 
    on_message_callback=callback,
    auto_ack=True,
    consumer_tag='netology_consumer',
    )

if __name__ == '__main__':
    channel.start_consuming() 
```
- и запустил из той же папки где они находились на ВМ:
```bash
$ python3 producer1.py
```
```bash
$ python3 consumer1.py
```
- вот результат работы:
![abc_publish&consumer](https://github.com/Lexacbr/rabbitmq/blob/main/scrsh/abc-publish%26consumer.png)

### Задание 3. Подготовка HA кластера

Используя Vagrant или VirtualBox, создайте вторую виртуальную машину и установите RabbitMQ.
Добавьте в файл hosts название и IP-адрес каждой машины, чтобы машины могли видеть друг друга по имени.

Пример содержимого hosts файла:
```shell script
$ cat /etc/hosts
192.168.0.10 rmq01
192.168.0.11 rmq02
```
После этого ваши машины могут пинговаться по имени.
Затем объедините две машины в кластер и создайте политику ha-all на все очереди.
*В качестве решения домашнего задания приложите скриншоты из веб-интерфейса с информацией о доступных нодах в кластере и включённой политикой.*
Также приложите вывод команды с двух нод:

```shell script
$ rabbitmqctl cluster_status
```
Для закрепления материала снова запустите скрипт producer.py и приложите скриншот выполнения команды на каждой из нод:

```shell script
$ rabbitmqadmin get queue='hello'
```
После чего попробуйте отключить одну из нод, желательно ту, к которой подключались из скрипта, затем поправьте параметры подключения в скрипте consumer.py на вторую ноду и запустите его.
*Приложите скриншот результата работы второго скрипта.*

---
### Ответ 3.
---
1. Установил ещё одну Вм, установил на неё `rabbitmq` `erlang` `python3` `pika`. Всё то что устанавливал и на первую ВМ.Опираясь на [статью](https://itisgood.ru/2018/12/11/kak-nastroit-klaster-rabbitmq-v-ubuntu-18-04-lts/) я произвёл следующие настройки:
- Для работы кластера RabbitMQ все узлы, участвующие в кластере, должны иметь одинаковые файлы cookie. Скопируйте по пути 
``` bash 
sudo nano /var/lib/rabbitmq/.erlang.cookie
```
на своем первом узле на все остальные узлы в кластере.
- поменял настройки в файле `/etc/hosts`
[etc-hosts](scrsh/etc-hosts.png)

2. Сброс RabbitMQ на второй машине:
- Перезапускаю сервис RabbitMQ.
```bash
$ sudo systemctl restart rabbitmq-server
```
- Останавливаю приложение:
```bash
$ sudo rabbitmqctl stop_app
```
- Сбрасываю rabbitmq:
```bash
$ sudo rabbitmqctl reset
```
- Присоединяю узел к кластеру:
```bash
$ sudo rabbitmqctl join_cluster rabbit@имя_перовой_ВМ_в_кластере
```
- Запускаю процесс подачи приложения:
```bash
$ sudo rabbitmqctl start_app
```
- Проверьте статус кластера:
```bash
$ root@первая_ВМ_в_кластере:~# rabbitmqctl cluster_status
```
3. Настраиваю политики RabbitMQ HA:
- Создайте политику, которая позволяет зеркалировать очереди для всех узлов в кластере:
```bash
$ sudo rabbitmqctl set_policy ha-all ".*" '{"ha-mode":"all"}'
```
- и вот результаты работы кластера:
- создание второй ВМ.
![abc-virtualbox-overview](https://github.com/Lexacbr/rabbitmq/blob/main/scrsh/abc-virtualbox-overview.png)
- создание кластера:
![create-cluster](https://github.com/Lexacbr/rabbitmq/blob/main/scrsh/create-cluster.png)
- подключения:
![connections](https://github.com/Lexacbr/rabbitmq/blob/main/scrsh/connections.png)
- кластер с выклеченым publisher. Из очереди только читается информация.
![cluster-1publisher](https://github.com/Lexacbr/rabbitmq/blob/main/scrsh/cluster-1publisher.png)
- результат одновременной работы и publisher и consumer
![pub&cons](https://github.com/Lexacbr/rabbitmq/blob/main/scrsh/pub%26cons.png)
![publish&consumer](https://github.com/Lexacbr/rabbitmq/blob/main/scrsh/publish%26consumer.png)
- результат команды:
```shell script
$ rabbitmqadmin get queue='hello'
```
![get_queue](https://github.com/Lexacbr/rabbitmq/blob/main/scrsh/get_queue.png)
- результат команды:
```shell script
$ rabbitmqctl cluster_status
```
![cluster-status](scrsh/cluster-status.png)
- проверка через терминал:
```bash
$ sudo rabbitmqctl list_users
```


