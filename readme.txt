Задача:

Написать ansible роль, которая создает кластер серверов clickhouse.


Описание:

Необходимо написать роль, которая настроит кластер серверов clickhouse, установит
необходимый софт и подключит их к кластеру zookeeper-ов.


Как будет проверяться:

 - в файле ansible_hosts, группе clickhouse прописываем тестовые сервера
 - запускаем роль и ожидаем выполнения без ошибок
 - создаем базу test на всех серверах
 
   CREATE DATABASE test

 - на всех серверах создаем таблицу :

   CREATE TABLE test.Migrations ( date Date DEFAULT toDate(now()), id UInt64, time UInt64) ENGINE = ReplicatedMergeTree('/clickhouse/tables/test/{shard}/Migrations', '{replica}', date, (id, time), 8192);

 - на одном из серверов делаем insert в таблицу
 - проверяем чтобы репликация отработала: на других серверах select возвращал данные


Параметры окружения:

 - для всех серверов clickhouse будет единый шард
 - серверов может быть 2+, проверять будем на 2 или 3 впс-ках
 - каждая нода кластера содержит демон zookeeper и clickhouse
 - на серверах изначально не будет подключена репа clickhouse


Технические данные:

 - использовать ОС ubuntu любой версии
 - clickhouse последней версии в репозитории
 - zookeeper последней версии в репозитории
 - ansible 2.x версии


https://clickhouse.yandex/docs/ru/
http://ansible.com/