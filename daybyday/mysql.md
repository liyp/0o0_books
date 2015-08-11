MySQL study
=============

**load data**

mysql [...] --local-infile 启动

**grant usage**

`GRANT USAGE ON *.* TO 'username'@'localhost' IDENTIFIED BY 'password' WITH GRANT OPTION;`

`GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP ON tablename.*  TO 'username'@'localhost' IDENTIFIED BY 'password';`

`FLUSH PRIVILEGES;`

> @"%" 表示对所有非本地主机授权， `grant all privileges on testDB.* to 'username'@localhost identified by 'password';`

## 运行和配置

### mysqld 与 mysqld_safe  

mysqld_safe脚本会在启动MySQL服务器后继续监控其运行情况，并在其死机时重新启动它。

1. 检查系统和选项。
2. 检查MyISAM表。
3. 保持MySQL服务器窗口。
4. 启动并监视mysqld，如果因错误终止则重启。
5. 将mysqld的错误消息发送到数据目录中的host_name.err 文件。
6. 将mysqld_safe的屏幕输出发送到数据目录中的host_name.safe文件。

## Q&A

*LOAD DATA  INFILE or  LOAD DATA LOCAL INFILE doesn't insert  correctly an utf-8 data files in an utf8 data base : It reads correctly but the insertion is done on ANSI charset.*  [ref](http://bugs.mysql.com/bug.php?id=10195)

> use CHARACTER SET clause in the LOAD FILE command.
> See here for details.
> http://dev.mysql.com/doc/refman/5.5/en/load-data.html

### 乐观锁 悲观所


### 查看IO瓶颈

`iostat` 查看IO情况  
`iotop` 定位负载来源进程  
`pt-ioprofile` 定位负载来源文件  
