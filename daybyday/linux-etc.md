Linux ETC
=========

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

### cron

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

### ssh

```bash
# 复制SSH密钥到目标主机，开启无密码SSH登录
ssh-copy-id user@host
# or
cat ~/.ssh/id_rsa.pub | ssh user@machine “mkdir ~/.ssh; cat >> ~/.ssh/authorized_keys”

# 从某主机的80端口开启到本地主机2001端口的隧道
ssh -N -L2001:localhost:80 somemachine

# 将你的麦克风输出到远程计算机的扬声器
dd if=/dev/dsp | ssh -c arcfour -C username@host dd of=/dev/dsp

# 比较远程和本地文件
ssh user@host cat /path/to/remotefile | diff /path/to/localfile –

# 通过SSH挂载目录/文件系统
sshfs name@server:/path/to/folder /path/to/mount/point

# 通过中间主机建立SSH连接
ssh -t reachable_host ssh unreachable_host

# 通过SSH运行复杂的远程shell命令
ssh host -l user “`cat cmd.txt`”
```
