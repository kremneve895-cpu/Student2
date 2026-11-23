# Лабораторная работа. Задание 9. Python-клиенты к Redis

Выполнила: студентка, Docker Lab 1, task09-redis

## Цель
Запустить два Python-контейнера, которые общаются через Redis
в пользовательской сети: один кладёт значение, другой читает.

## Шаги

1. В скриптах set_message.py и get_message.py указан хост
   redis-server — имя контейнера Redis из задания 8:

   r = redis.Redis(host="redis-server", port=6379)

2. Запущен контейнер-писатель (в сети my-network):

   docker run --rm --network my-network \
     -v $(pwd)/set_message.py:/set_message.py \
     python:3.8-slim bash -c "pip install -q redis && python /set_message.py"

   Вывод:
   Message set in Redis

3. Запущен контейнер-читатель:

   docker run --rm --network my-network \
     -v $(pwd)/get_message.py:/get_message.py \
     python:3.8-slim bash -c "pip install -q redis && python /get_message.py"

   Вывод:
   Message from Redis: Hello from Python

## Ответ на вопрос задания
Почему в скриптах указано имя redis-server, а не IP-адрес, и кто
это имя разрешает?

IP-адрес контейнера не постоянен: при пересоздании контейнера он
меняется, и скрипт с захардкоженным IP сломался бы. Имя же
redis-server закреплено за контейнером командой docker run --name
и не зависит от IP. Внутри пользовательской сети Docker
встроенный DNS-сервер разрешает имя redis-server в актуальный
адрес контейнера автоматически. Поэтому в скриптах используется
имя, а не IP: работает всегда, даже если Redis перезапущен и
получил другой адрес.

## Результат
- Два отдельных Python-контейнера обменялись сообщением через
  Redis в сети my-network.
- Писатель положил "Hello from Python", читатель получил и
  напечатал: Message from Redis: Hello from Python.
- Соединение установлено по имени redis-server (через DNS
  Docker), без указания IP-адреса.
