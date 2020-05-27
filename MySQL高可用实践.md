# MySQL高可用实践

## 环境说明

- MySQL 5.7
- VMWare Fusion 11.5.3
- 虚拟机Core i7 1核 2.6Ghz
- CentOS Linux release 7.8.2003 (Core)

## 搭建MySQL集群

### 安装单机版本MySQL服务

1. 在本机新增MySQL yum仓库，可以从[该页面](https://dev.mysql.com/downloads/repo/yum/)寻找对应系统版本的yum仓库，我这边根据自己的服务器版本选择了Redhat Linux 7版本：

   ```shell
   wget https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm
   sudo yum localinstall mysql80-community-release-el7-3.noarch.rpm
   ```

2. 由于上面安装的是8.0版本的仓库，因此默认安装的也是MySQL 8.0，需要更改配置默认安装5.7版本：

   1. 查看mysql仓库信息：

      ```shell
      yum repolist all | grep mysql
      ```

      会发现名称以“mysql80-community”开头的软件包默认启用

   2. 更改MySQL组件默认安装5.7版本：

      ```shell
      sudo yum-config-manager --disable mysql80-community
      sudo yum-config-manager --enable mysql57-community
      ```

      如果报错：找不到yum-config-manager命令，可以用`yum install yum-utils`安装

3. 安装MySQL 5.7

   ```shell
   yum install mysql-community-server
   ```

4. 开启MySQL服务器

   ```shell
   #开启mysql服务守护进程
   sudo service mysqld start
   #查看mysql服务状态
   sudo service mysqld status
   ```

在多台主机（不少于3台）上重复以上步骤，我这里在3台虚拟机上搭建了3个MySQL服务：

- 172.16.35.100:3306
- 172.16.35.101:3306
- 172.16.35.100:3306

### 基于binlog的Replication配置

1. 选择一个节点为Master节点，修改Master配置信息，包括：

   - server-id：`[1,2^32-1]`,如果为0，作为Master会拒绝所有的Slave连接，作为Slave会拒绝所有的Master连接。

   - 启用bin-log

     配置如下：

     ```mysql.conf
     [mysqld]
     log-bin=mysql-bin
     server-id=1	
     #master还要注意下面两个参数
     innodb_flush_log_at_trx_commit=1
     #skip_networking不能启用，否则Slave连不上Master
     #skip_networking
     ```

     完成后，重启mysqld服务：`service mysqld restart`

2. Slave连接到Master进行复制时需要使用用户名和密码，所有Slave节点可以共用一个用户名，也可以分别分配一个用户名，我这里选择共用一个用户名。

   ```sql
   create user 'repl'@'172.16.35.%' identified by '123456'
   grant replication slave on *.* to 'repl'@'172.16.35.%'
   ```

3. Slave复制Master时必须知道要从binlog的具体位置开始复制，获取Master的binlog的复制坐标操作如下：

   ```shell
   mysql> FLUSH TABLES WITH READ LOCK;
   mysql> SHOW MASTER STATUS;
   +------------------+----------+--------------+------------------+
   | File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
   +------------------+----------+--------------+------------------+
   | mysql-bin.000003 | 73       | test         | manual,mysql     |
   +------------------+----------+--------------+------------------+
   ```

   记住File和Position的值，等下Slave复制的时候会用到。**如果这个值是空的**，是因为你在配置binlog之前已经进行了其它操作导致有的数据没有存到binlog中，那么Slave复制时用空字符串`''`和4代替。

4. **如果Master节点还没有数据，可以跳过这个步骤**。否则，需要先将Master和Slave的数据同步使用mysqldump命令创建当前数据镜像，然后在复制之前将数据镜像导入Slave中：

   ```shell
   shell> mysqldump -all-databases --master-data >dbdump.db
   ```

   要导入这个数据，你可以吧dump文件复制到Slave，或者Slave连接到Master访问。

5. 设置Slave：

   1. 释放READ LOCK：

      ```shell
      mysql> UNLOCK TABLES;
      ```

   2. 修改Slave节点的配置文件，需要分配一个server-id，但是不需要启用binlog，重启服务。

      ```my.cnf
      [mysqld]
      server-id=2
      ```

   3. 在Slave节点配置Master的相关信息：

      1. 已有数据的Master节点，先导入数据：

         ```shell
         mysql < dbdump.db
         ```

      2. 空的Master节点，在每个Slave中执行下面的SQL语句。

         ```shell
         mysql> CHANGE MASTER TO
             ->     MASTER_HOST='master_host_name',
             ->     MASTER_USER='replication_user_name',
             ->     MASTER_PASSWORD='replication_password',
             ->     MASTER_LOG_FILE='recorded_log_file_name',
             ->     MASTER_LOG_POS=recorded_log_position;
         ```

         如果要导入已有的数据可以执行下面的命令，Master导入的数据会自动同步到Slave:

         ```shell
         shell> mysql -h master < fulldb.dump
         ```

      3. 开启主从复制，检查是否成功：

         ```mysql shell
         mysql> start slave;
         mysql> show slave status;
         ```

         ![](https://raw.githubusercontent.com/zhlzzzzz/notes/master/ShowSlaveStatus.png)

         如果开启成功，上图中Slave_IO_Running和Slave_SQL_Running都为yes

以上为搭建mysql主从复制集群的步骤。

