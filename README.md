# Домашнее задание к занятию "6.3. MySQL"

### Ответ:
```
Выполнение комманд: 
root@ubuntu:/# docker pull mysql:8.0
8.0: Pulling from library/mysql
6552179c3509: Pull complete
d69aa66e4482: Pull complete
3b19465b002b: Pull complete
7b0d0cfe99a1: Pull complete
9ccd5a5c8987: Pull complete
2dab00d7d232: Pull complete
64d3afdccd4a: Pull complete
82148d50b16c: Pull complete
8bb7d73a7d0c: Pull complete
74778cd68a75: Pull complete
d7e5f9309140: Pull complete
f2e376ecd59f: Pull complete
Digest: sha256:92d27b8222bbcf53bc42c70ca7cd1010d6c0527efc61f14980ce77c50932bef4
Status: Downloaded newer image for mysql:8.0

root@ubuntu:/# docker volume create vol1_mysql
vol1_mysql
root@ubuntu:/# docker run --rm --name mysql-docker -e MYSQL_ROOT_PASSWORD=mysql -ti -p 3306:3306 -v vol1_mysql:/etc/mysql/ mysql:8.0
...


Статус БД:
mysql> \s
--------------
mysql  Ver 8.0.28 for Linux on x86_64 (MySQL Community Server - GPL)

Connection id:          10
Current database:
Current user:           root@localhost
SSL:                    Not in use
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         8.0.28 MySQL Community Server - GPL
Protocol version:       10
Connection:             Localhost via UNIX socket
Server characterset:    utf8mb4
Db     characterset:    utf8mb4
Client characterset:    latin1
Conn.  characterset:    latin1
UNIX socket:            /var/run/mysqld/mysqld.sock
Binary data as:         Hexadecimal
Uptime:                 11 min 51 sec

Threads: 2  Questions: 5  Slow queries: 0  Opens: 117  Flush tables: 4  Open tables: 0  Queries per second avg: 0.007
--------------


mysql> 

Данные БД:
mysql> use test_db;
Database changed
mysql> show tables;
+-------------------+
| Tables_in_test_db |
+-------------------+
| orders            |
+-------------------+
1 row in set (0.00 sec)

mysql> select count(*) from orders where price >300;
+----------+
| count(*) |
+----------+
|        1 |
+----------+
1 row in set (0.00 sec)

Число записей 1
```

## Задача 2


### Ответ:
```
mysql> CREATE USER 'test'@'localhost' IDENTIFIED BY 'test-pass';
Query OK, 0 rows affected (0.22 sec)

mysql> 
mysql> ALTER USER 'test'@'localhost' ATTRIBUTE '{"fname":"James", "lname":"Pretty"}';
Query OK, 0 rows affected (0.16 sec)

mysql> 
mysql> ALTER USER 'test'@'localhost' 
    -> IDENTIFIED BY 'test-pass' 
    -> WITH
    -> MAX_QUERIES_PER_HOUR 100
    -> PASSWORD EXPIRE INTERVAL 180 DAY
    -> FAILED_LOGIN_ATTEMPTS 3 PASSWORD_LOCK_TIME 2;
Query OK, 0 rows affected (0.12 sec)

mysql> 
mysql> GRANT Select ON test_db.orders TO 'test'@'localhost';
Query OK, 0 rows affected, 1 warning (0.14 sec)

mysql> 
mysql> SELECT * FROM INFORMATION_SCHEMA.USER_ATTRIBUTES WHERE USER='test';
+------+-----------+---------------------------------------+
| USER | HOST      | ATTRIBUTE                             |
+------+-----------+---------------------------------------+
| test | localhost | {"fname": "James", "lname": "Pretty"} |
+------+-----------+---------------------------------------+
1 row in set (0.03 sec)
```

## Задача 3


### Ответ:
```
mysql> SELECT TABLE_NAME,ENGINE,ROW_FORMAT,TABLE_ROWS,DATA_LENGTH,INDEX_LENGTH FROM information_schema.TABLES WHERE table_name = 'orders' and  TABLE_SCHEMA = 'test_db' ORDER BY ENGINE asc;
+------------+--------+------------+------------+-------------+--------------+
| TABLE_NAME | ENGINE | ROW_FORMAT | TABLE_ROWS | DATA_LENGTH | INDEX_LENGTH |
+------------+--------+------------+------------+-------------+--------------+
| orders     | InnoDB | Dynamic    |          5 |       16384 |            0 |
+------------+--------+------------+------------+-------------+--------------+
1 row in set (0.06 sec)



mysql> ALTER TABLE orders ENGINE = MyISAM;
Query OK, 5 rows affected (1.17 sec)
Records: 5  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE orders ENGINE = InnoDB;
Query OK, 5 rows affected (1.37 sec)
Records: 5  Duplicates: 0  Warnings: 0

mysql> show profiles;
+----------+------------+------------------------------------+
| Query_ID | Duration   | Query                              |
+----------+------------+------------------------------------+
|        1 | 0.00013225 | show prifiles                      |
|        2 | 1.17695100 | ALTER TABLE orders ENGINE = MyISAM |
|        3 | 1.36970325 | ALTER TABLE orders ENGINE = InnoDB |
+----------+------------+------------------------------------+
3 rows in set, 1 warning (0.00 sec)

Продолжительность переключения на  MyISAM: 1,17
Продолжительность переключения на InnoDB: 1,36
```

## Задача 4 

### Ответ:
```
[mysqld]
pid-file        = /var/run/mysqld/mysqld.pid
socket          = /var/run/mysqld/mysqld.sock
datadir         = /var/lib/mysql
secure-file-priv= NULL

#Set IO Speed
# 0 - скорость
# 1 - сохранность
# 2 - универсальный параметр
innodb_flush_log_at_trx_commit = 0 

#Set compression
# Barracuda - формат файла с сжатием
innodb_file_format=Barracuda

#Set buffer
innodb_log_buffer_size	= 1M

#Set Cache size
key_buffer_size = 640М

#Set log size
max_binlog_size	= 100M

```
