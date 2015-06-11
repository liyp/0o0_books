MySQL study
=============

### load data
--local-infile

Q&A 

*LOAD DATA  INFILE or  LOAD DATA LOCAL INFILE doesn't insert  correctly an utf-8 data files in an utf8 data base : It reads correctly but the insertion is done on ANSI charset.*  [ref](http://bugs.mysql.com/bug.php?id=10195)

> use CHARACTER SET clause in the LOAD FILE command.
> See here for details.
> http://dev.mysql.com/doc/refman/5.5/en/load-data.html

### 乐观锁 悲观所
