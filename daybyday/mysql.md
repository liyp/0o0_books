MySQL study
=============

**load data**

mysql [...] --local-infile 启动

**grant usage**

`GRANT USAGE ON *.* TO 'username'@'localhost' IDENTIFIED BY 'password' WITH GRANT OPTION;`

`GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP ON tablename.*  TO 'username'@'localhost' IDENTIFIED BY 'password';`

`FLUSH PRIVILEGES;`

> @"%" 表示对所有非本地主机授权， `grant all privileges on testDB.* to 'username'@localhost identified by 'password';`

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
