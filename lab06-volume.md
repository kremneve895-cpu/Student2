# Lab: Docker Volume — MySQL Data Persistence

## 1. Запуск контейнера с volume
docker volume create mysql-data
docker run -d --name mysql-lab -e MYSQL_ROOT_PASSWORD=secret -v mysql-data:/var/lib/mysql mysql:5.7

## 2. Создание тестовых данных
docker exec -it mysql-lab mysql -u root -psecret -e "CREATE DATABASE lab; USE lab; CREATE TABLE students (id INT PRIMARY KEY, name VARCHAR(50)); INSERT INTO students VALUES (1, 'Evgenius'); SELECT * FROM students;"

## 3. Удаление контейнера
docker stop mysql-lab
docker rm mysql-lab

## 4. Повторный запуск с тем же томом
docker run -d --name mysql-lab -e MYSQL_ROOT_PASSWORD=secret -v mysql-data:/var/lib/mysql mysql:5.7

## 5. Проверка данных после пересоздания
docker exec -it mysql-lab mysql -u root -psecret -e "SELECT * FROM lab.students;"

## Вывод
Данные сохранились в volume mysql-data, несмотря на удаление и повторный запуск контейнера: том живёт отдельно от контейнера.
