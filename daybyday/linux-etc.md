Linux ETC
=========

## linux theory

### process

* [理解linux进程](defrur.gitbooks.io/introduction-to-process) **未完成**
* [Linux 技巧：让进程在后台可靠运行的几种方法](https://www.ibm.com/developerworks/cn/linux/l-cn-nohup/)

## linux etc

在公司的电脑上安装ubuntu，配置自己的开发环境。

关闭ubuntu购物提醒

`gsettings set com.canonical.Unity.Lenses disabled-scopes "['more_suggestions-amazon.scope', 'more_suggestions-u1ms.scope', 'more_suggestions-populartracks.scope', 'music-musicstore.scope', 'more_suggestions-ebay.scope', 'more_suggestions-ubuntushop.scope', 'more_suggestions-skimlinks.scope']"`

## 文件权限

[linux 文件权限](http://www.cnblogs.com/kzloser/articles/2673790.html)

## linux tools

### ZSH

* [zsh-zhihu](http://zhuanlan.zhihu.com/mactalk/19556676)

**zsh**

> `sudo apt-get install zsh`  
> `chsh -s /bin/zsh`

**oh-my-zsh**

> 手动  
> `git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh`  
> `cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc`

> 自动  
>`wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh`

### tmux

### crontab

```
# Edit this file to introduce tasks to be run by cron.
#
# Each task to run has to be defined through a single line
# indicating with different fields when the task will be run
# and what command to run for the task
#
# To define the time you can provide concrete values for
# minute (m), hour (h), day of month (dom), month (mon),
# and day of week (dow) or use '*' in these fields (for 'any').#
# Notice that tasks will be started based on the cron's system
# daemon's notion of time and timezones.
#
# Output of the crontab jobs (including errors) is sent through
# email to the user the crontab file belongs to (unless redirected).
#
# For example, you can run a backup of all your user accounts
# at 5 a.m every week with:
# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/
#
# For more information see the manual pages of crontab(5) and cron(8)
#
# m h  dom mon dow   command
```

**下面是 quartz-scheduler 的cron表达式**

http://www.quartz-scheduler.org/documentation/quartz-1.x/tutorials/crontrigger

Field Name |  Mandatory | Allowed Values | Allowed Special Characters
------- | -------| ------- | -------
Seconds | YES | 0-59 | , - * /
Minutes | YES | 0-59 | , - * /
Hours | YES | 0-23 | , - * /
Day of month | YES | 1-31 | , - * ? / L W
Month | YES | 1-12 or JAN-DEC | , - * /
Day of week | YES | 1-7 or SUN-SAT | , - * ? / L #
Year | NO | empty, 1970-2099 | , - * /

> So cron expressions can be as simple as this: `* * * * ? *`

> Pay attention to the effects of `?` and `*` in the day-of-week and day-of-month fields!

**Special characters**

`*` ("all values") - used to select all values within a field. For example, "" in the minute field means \*"every minute".

`?` ("no specific value") - useful when you need to specify something in one of the two fields in which the character is allowed, but not the other. For example, if I want my trigger to fire on a particular day of the month (say, the 10th), but don't care what day of the week that happens to be, I would put "10" in the day-of-month field, and "?" in the day-of-week field. See the examples below for clarification.

`-` - used to specify ranges. For example, "10-12" in the hour field means "the hours 10, 11 and 12".

`,` - used to specify additional values. For example, "MON,WED,FRI" in the day-of-week field means "the days Monday, Wednesday, and Friday".

`/` - used to specify increments. For example, "0/15" in the seconds field means "the seconds 0, 15, 30, and 45". And "5/15" in the seconds field means "the seconds 5, 20, 35, and 50". You can also specify '/' after the '' character - in this case '' is equivalent to having '0' before the '/'. '1/3' in the day-of-month field means "fire every 3 days starting on the first day of the month".

`L` ("last") - has different meaning in each of the two fields in which it is allowed. For example, the value "L" in the day-of-month field means "the last day of the month" - day 31 for January, day 28 for February on non-leap years. If used in the day-of-week field by itself, it simply means "7" or "SAT". But if used in the day-of-week field after another value, it means "the last xxx day of the month" - for example "6L" means "the last friday of the month". When using the 'L' option, it is important not to specify lists, or ranges of values, as you'll get confusing results.

`W` ("weekday") - used to specify the weekday (Monday-Friday) nearest the given day. As an example, if you were to specify "15W" as the value for the day-of-month field, the meaning is: "the nearest weekday to the 15th of the month". So if the 15th is a Saturday, the trigger will fire on Friday the 14th. If the 15th is a Sunday, the trigger will fire on Monday the 16th. If the 15th is a Tuesday, then it will fire on Tuesday the 15th. However if you specify "1W" as the value for day-of-month, and the 1st is a Saturday, the trigger will fire on Monday the 3rd, as it will not 'jump' over the boundary of a month's days. The 'W' character can only be specified when the day-of-month is a single day, not a range or list of days.
> The 'L' and 'W' characters can also be combined in the day-of-month field to yield 'LW', which translates to *"last weekday of the month"*.

`#` - used to specify "the nth" XXX day of the month. For example, the value of "6#3" in the day-of-week field means "the third Friday of the month" (day 6 = Friday and "#3" = the 3rd one in the month). Other examples: "2#1" = the first Monday of the month and "4#5" = the fifth Wednesday of the month. Note that if you specify "#5" and there is not 5 of the given day-of-week in the month, then no firing will occur that month.
> The legal characters and the names of months and days of the week are not case sensitive. MON is the same as mon.

### `{}`扩展

cp /etc/httpd/httpd.{,.bakup}

mv resume{z,}.doc

> Brace expansion(大括号扩展或叫做花括号扩展) 是用来随机产生字符串组合的机制。这种机制类似于文件的扩展名，但是并不需要存在响应的文件。
Brace expansion(大括号扩展或叫做花括号扩展)模式是一可选的preamble(前导字符)，后面跟着一系列逗号分隔的字符串，包含在一对花括号中，
再后面是一个可选的postscript(打印编程语言)。preamble(前导字符)被添加到花括号中的每个字符串前面，postscript(打印编程语言)被附加到每个结果字符串之后，
从左到右进行扩展。花括号扩展可以嵌套。扩展字符串的结果没有排序；而是保留了从左到右的顺序。

### SMB/CIFS

smbclient主要用在Linux访问windows的共享文件，或者是访问Linux的samba服务器。

功能说明：可存取SMB/CIFS服务器的用户端程序。

语法：smbclient [网络资源][密码][-EhLN][-B<IP地址>][-d<排错层级>][-i<范围>][-I<IP地址>][-l<记录文件>][-M<NetBIOS名称>][-n<NetBIOS名称>][-O<连接槽选项>][-p<TCP连接端口>][-R<名称解析顺序>][-s<目录>][-t<服务器字码>][-T<tar选项>][-U<用户名称>][-W<工作群组>]

补充说明：SMB与CIFS为服务器通信协议，常用于Windows95/98/NT等系统。smbclient可让Linux系统存取Windows系统所分享的资源。

`smbclient -c "cd dir; put README.md" -U username%passwd   //file.net/sharedDir`
