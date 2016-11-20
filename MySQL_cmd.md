# MySQL 常用基本操作

## 安装mysql-server
```bash
sudo apt update
sudo apt install mysql-server
```
## 卸载mysql-server
```bash
sudo apt-get purge mysql-server mysql-client mysql-common mysql-server-core-5.5 mysql-client-core-5.5
sudo rm -rf /etc/mysql /var/lib/mysql
sudo apt-get autoremove
sudo apt-get autoclean
```

## install MySQLdb for python

```bash
apt-get install python-pip
pip install -U pip
```

```bash
apt-get install python-dev libmysqlclient-dev
pip install MySQL-python

```

##中文支持

```bash
$ sudo vim /etc/mysql/my.cnf

[client]
default-character-set = utf8

[mysqld]
default-storage-engine = INNODB
character-set-server = utf8
collation-server = utf8_general_ci
```

在python里面connect database的时候要set charset=utf8 explicitly.

## 重启mysql
```bash
/etc/init.d/mysql stop   
/etc/init.d/mysql  start
```


## 创建database和user

在terminal进入数据库
```shell
$ mysql -u root -p
$ #输入密码
mysql> SHOW DATABASES; #这里分号;非常重要,必不可少,数据库操作对大小写不敏感,常用大写
# 输出结果
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
mysql> SELECT User FROM mysql.user;#查看所有用户
+------------------+
| User             |
+------------------+
| debian-sys-maint |
| mysql.sys        |
| root             |
+------------------+
3 rows in set (0.00 sec)

mysql> CREATE DATABASE kancloud;#创建一个数据库,名字为kancloud
mysql> CREATE USER 'testuser'@'localhost' IDENTIFIED BY 'test623'#创建一个用户
mysql> GRANT ALL ON kancloud.* TO 'testuser'@'localhost'#为用户提供该数据库所有权限
mysql> SHOW DATABASES#可以看到有新的数据库建成功了
+--------------------+
| Database           |
+--------------------+
| information_schema |
| kancloud           |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.00 sec)

mysql> SELECT User FROM mysql.user;
+------------------+
| User             |
+------------------+
| debian-sys-maint |
| mysql.sys        |
| root             |
| testuser         |
+------------------+
4 rows in set (0.00 sec)

mysql>quit
```
这样我们就创建了数据库和用户了,并对用户进行了授权.

## 查询
```mysql
select name from books where name like '%C++%';
+---------------------------------------------+
| name                                        |
+---------------------------------------------+
| 像计算机科学家一样思考（C++版)              |
| 实战c++中的vector系列                       |
| 浅析C++11新特性                             |
| C++开发人脸性别识别教程                     |
| 实战c++中的string系列                       |
| C++真知灼见                                 |
| 关注C++细节                                 |
| C++学习积累                                 |
| Effective C++ 学习笔记                      |
| vc++编程实践                                |
| 设计模式C++实现                             |
+---------------------------------------------+
11 rows in set (0.00 sec)

mysql> select name from books where name like '%C++%' && dlable='1';
+---------------------------------------------+
| name                                        |
+---------------------------------------------+
| 像计算机科学家一样思考（C++版)              |
| C++开发人脸性别识别教程                     |
| 关注C++细节                                 |
| C++学习积累                                 |
| Effective C++ 学习笔记                      |
| vc++编程实践                                |
| 设计模式C++实现                             |
+---------------------------------------------+
7 rows in set (0.00 sec)

```
