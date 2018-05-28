此镜像是在mysql：5.7的基础上修改,原有镜像无法保留在数据库中创建的数据。
-   运行实例
```
$ docker run -d --name mysql -p 3306:3306 -e  MYSQL_ROOT_PASSWORD=admin langtodu/mysql:v5.7.3
```
-   添加了start.sh文件在/

    start.sh
    ```
    sleep 3
    mysql -uroot -padmin -e 'CREATE DATABASE IF NOT EXISTS nginx'
    mysql -uroot -padmin -e 'GRANT ALL ON nginx.* TO "nginx"@"%" IDENTIFIED BY "nginx"'
    mysql -uroot -padmin -e 'GRANT ALL ON nginx.* TO "nginx"@"localhost" IDENTIFIED BY "nginx"'
    mysql -unginx -pnginx nginx -e 'CREATE TABLE IF NOT EXISTS `info` ( `uid` VARCHAR(100), `time` VARCHAR(100), `timestamp` double, `name` VARCHAR(20), PRIMARY KEY ( uid ) ) ENGINE=InnoDB DEFAULT CHARSET=utf8'
    ```
-   修改了/etc/mysql/mysql.conf.d/mysqld.cnf
```
[mysqld_safe]
socket          = /var/run/mysqld/mysqld.sock
nice            = 0

[mysqld]
#
# * Basic Settings
#
user            = mysql
pid-file        = /var/run/mysqld/mysqld.pid
socket          = /var/run/mysqld/mysqld.sock
port            = 3306
basedir         = /usr
datadir         = /var/lib/mysql
tmpdir          = /tmp
lc-messages-dir = /usr/share/mysql
skip-external-locking
#
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
bind-address            = 0.0.0.0
#
# * Fine Tuning
#
key_buffer_size         = 16M
max_allowed_packet      = 16M
thread_stack            = 192K
thread_cache_size       = 8
# This replaces the startup script and checks MyISAM tables if needed
# the first time they are touched
myisam-recover-options  = BACKUP
#max_connections        = 100
#table_cache            = 64
#thread_concurrency     = 10
#
# * Query Cache Configuration
#
query_cache_limit       = 1M
query_cache_size        = 16M
#
# * Logging and Replication
#
# Both location gets rotated by the cronjob.
# Be aware that this log type is a performance killer.
# As of 5.1 you can enable the log at runtime!
general_log_file        = /var/log/mysql/mysql.log
general_log             = 1
#
# Error log - should be very few entries.
#
log_error = /var/log/mysql/error.log
#
# Here you can see queries with especially long duration
#log_slow_queries       = /var/log/mysql/mysql-slow.log
#long_query_time = 2
#log-queries-not-using-indexes
#
# The following can be used as easy to replay backup logs or for replication.
# note: if you are setting up a replication slave, see README.Debian about
#       other settings you may need to change.
#server-id              = 1
#log_bin                        = /var/log/mysql/mysql-bin.log
expire_logs_days        = 10
max_binlog_size   = 100M
#binlog_do_db           = include_database_name
#binlog_ignore_db       = include_database_name
#
# * InnoDB
#
# InnoDB is enabled by default with a 10MB datafile in /var/lib/mysql/.
# Read the manual for more InnoDB related options. There are many!
#
# * Security Features
#
# Read the manual, too, if you want chroot!
# chroot = /var/lib/mysql/
#
# For generating SSL certificates I recommend the OpenSSL GUI "tinyca".
#
# ssl-ca=/etc/mysql/cacert.pem
# ssl-cert=/etc/mysql/server-cert.pem
# ssl-key=/etc/mysql/server-key.pem
```
-   日志挂载
```
$ docker run -d --name mysql -p 3306:3306 -e  MYSQL_ROOT_PASSWORD=admin -v path/to/mysql-log:/vat/log/mysql langtodu/mysql:v5.7.5
```
注：path/to/mysql-log权限必须可读写。