# MySQL安装

安装很简单，主要是配置文件的修改！！在`ubuntu`下，配置文件主要在目录`/etc/mysql`下，对全局用户生效就修改`/etc/mysql/my.cnf`文件，若想只修改当前用户的配置文件，就修改/创建 `~/.my.cnf`文件！！

```shell
# 一个MySQL配置文件示例
!includedir /etc/mysql/conf.d/
!includedir /etc/mysql/mysql.conf.d/
[mysqld]
# 设置3306端口
port=3306
# 设置mysql的安装目录
# basedir=/usr/local/mysql
# 设置mysql数据库的数据的存放目录
# datadir= /var/lib/mysql
# 允许最大连接数
max_connections=200
# 允许连接失败的次数。这是为了防止有人从该主机试图攻击数据库系统
max_connect_errors=10
# 服务端使用的字符集默认为UTF8
character-set-server=utf8mb4
#使用–skip-external-locking MySQL选项以避免外部锁定。该选项默认开启
external-locking = FALSE
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
# 默认使用“mysql_native_password”插件认证
default_authentication_plugin=mysql_native_password

[mysqld_safe]
log-error=error.log
#pid-file=mysqld.pid
# 定义mysql应该支持的sql语法，数据校验
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8mb4
[client]
# 设置mysql客户端连接服务端时默认使用的端口
port=3306
default-character-set=utf8mb4
```

`ubuntu`下`MySQL`各类文件位置：

1. 客户端程序：`/usr/bin/mysql`

2. 服务器程序：`/usr/sbin/mysqld`

3. 数据库和日志文件：`/var/lib/mysql`

4. 配置文件：`/etc/mysql/my.cnf （全局）或者~/.my.cnf（针对用户）`

5. 启动mysql服务器：`/etc/init.d/mysql`



查看服务状态：`service mysql status`。包括重启之类的操作都是 `service ... restart/start/stop`

==重要==：一般软件的配置文件所在目录：`/etc/....`；

