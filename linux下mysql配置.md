# glibc安装MySQL数据库

## 学习目标

- 能够熟练掌握MySQL glibc版本的安装

## 安装步骤

## 1.下载软件包

官网下载

```shell
# wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.27-linux-glibc2.12-x86_64.tar.gz
```

## 2.解压文件包

```shell
1.解压
# tar xvf mysql-5.7.27-linux-glibc2.12-x86_64.tar.gz
2.移动
# mv mysql-5.7.27-linux-glibc2.12-x86_64 /usr/local/mysql
3.验证
# ls /usr/local/mysql/
COPYING  README  bin  docs  include  lib  man  share  support-files

```

## 3.编辑配置文件

```she
1.编辑主配置文件
# vi /etc/my.cnf
[mysqld]
datadir = /usr/local/mysql/data/
socket = /tmp/mysql.sock
symbolic-links = 0

[mysqld_safe]
log-error = /var/log/mysqld.log
pid-file = /var/run/mysqld/mysqld.pid

!includedir /etc/my.cnf.d

2.创建子配置文件目录
# mkdir /etc/my.cnf.d

3.创建数据目录
# mkdir /usr/local/mysql/data

4.创建pid文件存放目录
# mkdir /var/run/mysqld

5.创建启动用户mysql
# useradd mysql

6.将上面创建的数据目录及pid文件存放目录权限都改为mysql
# chown mysql.mysql /var/run/mysql /usr/local/mysql/ -R
```

## 4.初始化数据库仓库

```shel
# /usr/local/mysql/bin/mysql_install_db --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data

注意：
这里如果执行上面命令报如下的错误
2019-11-28 05:33:06 [WARNING] mysql_install_db is deprecated. Please consider switching to mysqld --initialize
2019-11-28 05:33:06 [ERROR]   Child process: /usr/local/mysql/bin/mysqldterminated prematurely with errno= 32
2019-11-28 05:33:06 [ERROR]   Failed to execute /usr/local/mysql/bin/mysqld --bootstrap --datadir=/usr/local/mysql/data --lc-messages-dir=/usr/local/mysql/share --lc-messages=en_US --basedir=/usr/local/mysql
-- server log begin --
解决办法：
# yum install libaio numactl -y
```

## 5.添加MySQL全局环境变量

```shel
# echo "export PATH=$PATH:/usr/local/mysql/bin">>/etc/profile
# source /etc/profile
```

## 6.启动MySQL，并设为开机启动

```shell
# cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysqld
# chkconfig --add mysqld
# chkconfig --level 2345 mysqld on
# service mysqld start
```

## 7.更改密码

```shell
1.查看初始密码
# cat /root/.mysql_secret
# Password set for user 'root@localhost' at 2019-11-28 06:15:21
......
2.修改密码
# mysqladmin -u root password "123456" -p
Enter password: #这里输入上面查看到的密码，...

3.验证密码是否修改成功
# mysql -p123456 -e 'show database'
mysql: [Warning] Using a password on the command line interface can be insecure.

+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+

4.设置远端登录
# myseq -p123456
mysql> grant all on *.* to 'root'@'%' identified by '123456';
Query OK, 0 rows affected, 1 warning (0.00 sec)
mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)
mysql> \q
Bye
```



