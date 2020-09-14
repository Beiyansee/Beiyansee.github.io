## 记录一次搭建Mysql主从数据库集群

> 主服务器(master): 47.107.166.94
>
> 从服务器(slave): 123.207.82.109
>
> 备份数据库名: admin

### 一. 数据同步

**1. 首先通过navicat连接两台服务器**

![image-20200907150103305](https://zlm-blog-img.oss-cn-beijing.aliyuncs.com/image-20200907150103305.png)

**2. 使用数据传输工具将主服务器中的admin数据库传输到从服务器数据库中(主从数据库数据以及状态保持一致)**

![image-20200907150426460](https://zlm-blog-img.oss-cn-beijing.aliyuncs.com/image-20200907150426460.png)



### 二. 主服务器配置（master）

**1. 编辑配置文件**

```shell
vim /etc/my.cnf
```

在配置文件末尾添加如下配置

```sql
#主数据库端ID号
server_id = 1           
 #开启二进制日志                  
log-bin = mysql-bin    
#需要复制的数据库名，如果复制多个数据库，重复设置这个选项即可(修改admin为你需要同步的数据库)
binlog-do-db = admin 
#将从服务器从主服务器收到的更新记入到从服务器自己的二进制日志文件中                 
log-slave-updates                        
#控制binlog的写入频率。每执行多少次事务写入一次(这个参数性能消耗很大，但可减小MySQL崩溃造成的损失) 
sync_binlog = 1                    
#这个参数一般用在主主同步中，用来错开自增值, 防止键值冲突
auto_increment_offset = 1           
#这个参数一般用在主主同步中，用来错开自增值, 防止键值冲突
auto_increment_increment = 1            
#二进制日志自动删除的天数，默认值为0,表示“没有自动删除”，启动时和二进制日志循环时可能删除  
expire_logs_days = 7                    
#将函数复制到slave  
log_bin_trust_function_creators = 1       
```

**2. 重启MySQL，创建允许从服务器同步数据的账户**

```sql
#重启mysql
[root@master ~]# service mysqld restart
#登录mysql
[root@master ~]# mysql -u root -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.7.25-log MySQL Community Server (GPL)

Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

#创建slave账号account，密码123456
mysql>  grant replication slave on *.* to 'account'@'123.207.82.109' identified by '123456';
Query OK, 0 rows affected, 1 warning (0.01 sec)
#更新数据库权限
mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)
```

**3. 查看服务器状态**

```sql
mysql> show master status;
+------------------+----------+--------------+------------------+-------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+------------------+----------+--------------+------------------+-------------------+
| mysql-bin.000001 |      605 | admin        |                  |                   |
+------------------+----------+--------------+------------------+-------------------+
1 row in set (0.00 sec)
```
这两个值需要记录一下,等会会用到
![image-20200907152318912](https://zlm-blog-img.oss-cn-beijing.aliyuncs.com/image-20200907152318912.png)

### 三.从服务器配置(slave)

**1. 编辑配置文件**

```shell
vim /etc/my.cnf
```

在配置文件末尾添加如下配置

```sql
server_id = 2
log-bin = mysql-bin
log-slave-updates
sync_binlog = 0
#log buffer将每秒一次地写入log file中，并且log file的flush(刷到磁盘)操作同时进行。该模式下在事务提交的时候，不会主动触发写入磁盘的操作
innodb_flush_log_at_trx_commit = 0        
#指定slave要复制哪个库 (修改admin为你需要同步的数据库)
replicate-do-db = admin 
#MySQL主从复制的时候，当Master和Slave之间的网络中断，但是Master和Slave无法察觉的情况下（比如防火墙或者路由问题）。Slave会等待slave_net_timeout设置的秒数后，才能认为网络出现故障，然后才会重连并且追赶这段时间主库的数据
slave-net-timeout = 60                    
log_bin_trust_function_creators = 1
```

**2. 重启MySQL，执行同步命令**

#重启mysql
```sql
[root@master ~]# service mysqld restart
```
#登录mysql

```sql
[root@master ~]# mysql -u root -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.7.25-log MySQL Community Server (GPL)

Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```
#执行同步命令，设置主服务器ip，同步账号密码，同步位置

注意: master_log_file 和 master_log_pos的值从主服务器状态表中得到.

```sql
mysql> change master to master_host='47.107.166.94',master_user='account',master_password='123456',master_log_file='mysql-bin.000001',master_log_pos=605
    -> ;
Query OK, 0 rows affected, 1 warning (0.00 sec)
```
启动从服务器
```sql
mysql> start slave;
Query OK, 0 rows affected (0.00 sec)
```
3. 查看从服务器状态
   Slave_IO_Running及Slave_SQL_Running的值都为Yes则成功

```
mysql> show slave status\G;
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 47.107.166.94
                  Master_User: account
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000001
          Read_Master_Log_Pos: 1355
               Relay_Log_File: centos-relay-bin.000002
                Relay_Log_Pos: 1070
        Relay_Master_Log_File: mysql-bin.000001
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
              Replicate_Do_DB: admin
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 1355
              Relay_Log_Space: 1278
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: 0
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 1
                  Master_UUID: 1a29c21a-4251-11e9-8bb9-00163e14a4ed
             Master_Info_File: /data/mysql/master.info
                    SQL_Delay: 0
          SQL_Remaining_Delay: NULL
      Slave_SQL_Running_State: Slave has read all relay log; waiting for more updates
           Master_Retry_Count: 86400
                  Master_Bind:
      Last_IO_Error_Timestamp:
     Last_SQL_Error_Timestamp:
               Master_SSL_Crl:
           Master_SSL_Crlpath:
           Retrieved_Gtid_Set:
            Executed_Gtid_Set:
                Auto_Position: 0
         Replicate_Rewrite_DB:
                 Channel_Name:
           Master_TLS_Version:
1 row in set (0.00 sec)
```

### 四. 验证

**1. 修改主服务器中一张表中的一条数据**

**2. 查看从数据库数据表中数据是否改变**

### 五. 可能出现的问题

#### 1.无法登录本地数据库

```shell
[root@centos ~]# mysql -u root -p
Enter password:
ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/var/lib/mysql/mysql.sock' (2)
```

**解决方案**

使用`mysql -h 127.0.0.1 -u root -p`命令登录

```shell
[root@centos local]# mysql -h 127.0.0.1 -u root -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.7.30-log MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

```

#### 2.出现''Last_IO_Errno: 1236"错误

 ```sql
mysql> show slave status\G;
*************************** 1. row ***************************
               Slave_IO_State:
                  Master_Host: 47.107.166.94
                  Master_User: account
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000033
          Read_Master_Log_Pos: 337523
               Relay_Log_File: centos-relay-bin.000001
                Relay_Log_Pos: 4
        Relay_Master_Log_File: mysql-bin.000033
             Slave_IO_Running: No
            Slave_SQL_Running: Yes
              Replicate_Do_DB: admin
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 337523
              Relay_Log_Space: 154
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: NULL
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 1236
                Last_IO_Error: Got fatal error 1236 from master when reading data from binary log: 'Could not find first log file name in binary log index file'
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 1
                  Master_UUID: 1a29c21a-4251-11e9-8bb9-00163e14a4ed
             Master_Info_File: /data/mysql/master.info
                    SQL_Delay: 0
          SQL_Remaining_Delay: NULL
      Slave_SQL_Running_State: Slave has read all relay log; waiting for more updates
           Master_Retry_Count: 86400
                  Master_Bind:
      Last_IO_Error_Timestamp: 200907 14:45:28
     Last_SQL_Error_Timestamp:
               Master_SSL_Crl:
           Master_SSL_Crlpath:
           Retrieved_Gtid_Set:
            Executed_Gtid_Set:
                Auto_Position: 0
         Replicate_Rewrite_DB:
                 Channel_Name:
           Master_TLS_Version:
1 row in set (0.00 sec)

 ```

**解决方案**

检查主从服务器配置的master_log_file 和 master_log_pos的值是否一致.