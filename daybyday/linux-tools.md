# 介绍几个linux调测工具 【[转](http://blog.csdn.net/zj0910/article/details/28691985)】

### top

top 命令显示当前的活动进程，默认它是按消耗 CPU 的厉害程度进行排序，每5秒钟刷新一次列表，你也可以选择不同的排序方式。

常用键要会用会提供更多便利:

```shell
t :显示摘要信息开关.
m :显示内存信息开关.
A :分类显示系统不同资源的使用大户。有助于快速识别系统中资源消耗多的任务。
f :添加删除所要显示栏位.
o :调整所要显示栏位的顺序.
r :调整一个正在运行的进程Nice值.
k :结束一个正在运行的进程.
z :彩色/黑白显示开关
```

### strace

跟踪内核系统调用的信息和接收的信号信息.

```
linux-xvbv:/usr/src/linux # strace
usage: strace [-dffhiqrtttTvVxx] [-a column] [-e expr] ... [-o file]
              [-p pid] ... [-s strsize] [-u username] [-E var=val] ...
              [command [arg ...]]
   or: strace -c [-e expr] ... [-O overhead] [-S sortby] [-E var=val] ...
              [command [arg ...]]
-c -- count time, calls, and errors for each syscall and report summary
-f -- follow forks, -ff -- with output into separate files
-F -- attempt to follow vforks, -h -- print help message
-i -- print instruction pointer at time of syscall
-q -- suppress messages about attaching, detaching, etc.
-r -- print relative timestamp, -t -- absolute timestamp, -tt -- with usecs
-T -- print time spent in each syscall, -V -- print version
-v -- verbose mode: print unabbreviated argv, stat, termio[s], etc. args
-x -- print non-ascii strings in hex, -xx -- print all strings in hex
-a column -- alignment COLUMN for printing syscall results (default 40)
-e expr -- a qualifying expression: option=[!]all or option=[!]val1[,val2]...
   options: trace, abbrev, verbose, raw, signal, read, or write
-o file -- send trace output to FILE instead of stderr
-O overhead -- set overhead for tracing syscalls to OVERHEAD usecs
-p pid -- trace process with process id PID, may be repeated
-s strsize -- limit length of print strings to STRSIZE chars (default 32)
-S sortby -- sort syscall counts by: time, calls, name, nothing (default time)
-u username -- run command as username handling setuid and/or setgid
-E var=val -- put var=val in the environment for command
-E var -- remove var from the environment for command

-c 统计每一系统调用的所执行的时间,次数和出错的次数等.
-d 输出strace关于标准错误的调试信息.
-f 跟踪由fork调用所产生的子进程.
-ff 如果提供-o filename,则所有进程的跟踪结果输出到相应的filename.pid中,pid是各进程的进程号.
-F 尝试跟踪vfork调用.在-f时,vfork不被跟踪.
-h 输出简要的帮助信息.
-i 输出系统调用的入口指针.
-q 禁止输出关于脱离的消息.
-r 打印出相对时间关于,,每一个系统调用.
-t 在输出中的每一行前加上时间信息.
-tt 在输出中的每一行前加上时间信息,微秒级.
-ttt 微秒级输出,以秒了表示时间.
-T 显示每一调用所耗的时间.
-v 输出所有的系统调用.一些调用关于环境变量,状态,输入输出等调用由于使用频繁,默认不输出.
-V 输出strace的版本信息.
-x 以十六进制形式输出非标准字符串
-xx 所有字符串以十六进制形式输出.
-a column 设置返回值的输出位置.默认为40.
-e expr 指定一个表达式,用来控制如何跟踪.格式如下:
[qualifier=][!]value1[,value2]... qualifier只能是 trace,abbrev,verbose,raw,signal,read,write其中之一.value是用来限定的符号或数字.默认的qualifier是 trace.感叹号是否定符号.例如:
-eopen等价于 -e trace=open,表示只跟踪open调用.而-etrace!=open表示跟踪除了open以外的其他调用.有两个特殊的符号 all 和 none.
注意有些shell使用!来执行历史记录里的命令,所以要使用\\\\.
-e trace=set 只跟踪指定的系统调用.例如:-e trace=open,close,rean,write表示只跟踪这四个系统调用.默认的为set=all.
-e trace=file 只跟踪有关文件操作的系统调用.
-e trace=process 只跟踪有关进程控制的系统调用.
-e trace=network 跟踪与网络有关的所有系统调用.
-e strace=signal 跟踪所有与系统信号有关的系统调用
-e trace=ipc 跟踪所有与进程通讯有关的系统调用
-e abbrev=set 设定strace输出的系统调用的结果集.-v 等与 abbrev=none.默认为abbrev=all.
-e raw=set 将指定的系统调用的参数以十六进制显示.
-e signal=set 指定跟踪的系统信号.默认为all.如signal=!SIGIO(或者signal=!io),表示不跟踪SIGIO信号.
-e read=set 输出从指定文件中读出的数据.例如:
-e read=3,5 -e write=set 输出写入到指定文件中的数据.
-o filename 将strace的输出写入文件filename
-p pid 跟踪指定的进程pid.
-s strsize 指定输出的字符串的最大长度.默认为32.文件名一直全部输出.
-u username 以username的UID和GID执行被跟踪的命令.
```

### gstack

这个工具非常好, 可以指定一个进程ID 后 查看其调用栈:

```
linux-xvbv:/usr/src/linux # gstack 3789

Thread 1 (process 3789):
#0  0x00007fb74b129925 in mq_timedreceive () from /lib64/librt.so.1
#1  0x000000000267291e in mcque_recv ()
#2  0x000000000267a49e in pf_ppe_ingressprocFromX86 ()
#3  0x000000000267aaff in pf_ppe_mainproc_thread ()
#4  0x00007fb74b3345f0 in start_thread () from /lib64/libpthread.so.0
#5  0x00007fb74a82984d in clone () from /lib64/libc.so.6
#6  0x0000000000000000 in ?? ()
```

### mpstat

多处理器使用率

mpstat命令可以显示所有可用处理器的使用情况，处理器编号从0开始。

### vmstat

系统活动、硬件及系统信息

使用vmstat命令可以得到关于进程、内存、内存分页、堵塞IO、traps及CPU活动的信息

### pmap

进程的内存使用

pmap命令可以显示进程的内存映射，使用这个命令可以找出造成内存瓶颈的原因

如果你想知道你的服务器正在做干什么，你就需要了解一些基本的命令，一旦你精通了这些命令，那你就是一个 专业的 Linux 系统管理员。

有些 Linux 发行版会提供 GUI 程序来进行系统的监控，例如 SUSE Linux 就有一个非常棒而且专业的工具 YaST，KDE 的 KDE System Guard 同样很出色。当然，要使用这些工具，你必须在服务器跟前进行操作，而且这些 GUI 的程序占用了很多系统资源，所以说，尽管 GUI 用来做基本的服务器健康状态监测挺好，但如果你想知道真正发生什么，请关掉 GUI 开始命令行之旅吧。

你应该只在需要的时候去启动 GUI ，不用的时候关掉它。如果要让服务器保持最佳性能，你应该将 Linux 服务器的运行级别 runlevel 设置为 3 ，就是控制台模式，当你需要图形化桌面的时候使用 startx 命令来启动它。

如果你的服务器启动后就直接进入图形界面，你需要修改配置 /etc/inittab 找到 initdefault 一样，将 id:5:initdefault 修改为 id:3:initdefault。

如果你没找到 /etc/inittab 文件，那就创建一个新的，文件内容增加 id:3 这么一行。这样下次服务器启动的时候就不会进入图形界面。如果你不想等到服务器重启的时候才生效，你可以执行 init 3 这个命令。

一旦你的服务器是在控制台模式下运行，你就可以开始我们接下来的内容。

### iostat

iostat 命令用来显示存储子系统的详细信息，通常用它来监控磁盘 I/O 的情况。要特别注意 iostat 统计结果中的 %iowait 值，太大了表明你的系统存储子系统性能低下。

### meminfo 和 free

Meminfo 可让你获取内存的详细信息，你可以使用 cat 和 grep 命令来显示 meminfo 信息：

cat /proc/meminfo

另外你可以使用 free 命令来显示动态的内存使用信息，free 只是给你大概的内存信息，而 meminfo 提供的信息更加详细。

### mpstat

mpstat mpstat是MultiProcessor Statistics的缩写，是实时系统监控工具。其报告与CPU的一些统计信息，这些信息存放在/proc/stat文件中。在多CPUs系统里，其不但能查看所有CPU的平均状况信息，而且能够查看特定CPU的信息。

关于 mpstat 执行结果中的参数意思请参考此贴。

### netstat

Netstat 和 ps 命令类似，是 Linux 管理员基本上每天都会用的工具，它显示了大量跟网络相关的信息，例如 socket 的使用、路由、接口、协议、网络等等，下面是一些常用的参数：
```
　　1-a Show all socket information

　　2-r Show routing information

　　3-i Show network interface statistics

　　4-s Show network protocol statistics
```

### nmon

Nmon, 是 Nigel's Monitor 的缩写，是一个使用很普遍的开源工具，用以监控 Linux 系统的性能。Nmon 监控多个子系统的性能数据，例如处理器的使用率、内存使用率、队列、磁盘I/O统计、网络I/O统计、内存页处理和进程信息。Nmon 也提供了一个图形化的工具.

要运行 nmon，你可以在命令行中启动它，然后选择要监控的子系统，这些子系统都对应有一个快捷键，例如输入 c 可查看 CPU 信息，m用于查看内存，d用来查看磁盘信息等，你也可以使用 -f 命令将 nmon 的执行结果保存到一个 CSV 文件中，便于日后分析。

在每日的监控工作中，我发现 nmon 是我最常用的工具。

### pmap

pmap 命令用来报告每个进程占用内存的详细情况，可用来看是否有进程超支了，该命令需要进程 id 作为参数。

### ps 和 pstree

ps 和 pstree 命令是 Linux 系统管理员最好的朋友，都可以用来列表正在运行的所有进程。ps 告诉你每个进程占用的内存和 CPU 处理时间，而 pstree 显示的信息没那么详细，但它以树形结构显示进程之间的依赖关系，包括子进程信息。一旦发现某个进程有问题，你可以使用 kill 来杀掉它。

### sar

sar 程序是系统监控工具里的瑞士军刀。该程序包含三个工具：sar 用来显示数据，sa1 和 sa2 用来收集数据并保存。sar 可用来显示 CPU 使用率、内存页数据、网络 I/O 和传输统计、进程创建活动和磁盘设备的活动详情。sar 和 nmon 最大的不同就是 sar 跟适合用作长期的监控，而 nmon 可以让你快速的了解系统当前状态。

### strace

strace 经常被认为是程序员调试的工具，但不止如此。它可以记录进程进行系统调用的详情，因此它也是一个非常好的诊断工具，例如你可以使用它来找出某个程序正在打开某个配置文件。

Strace 也有一个缺陷，但它在跟踪某个进程时会让该进程的性能变得非常差，因此请谨慎使用。

### tcpdump

Tcpdump 是一个简单、可靠的网络监控工具，用来做基本的协议分析，看看那些进程在使用网络以及如何使用网络。当然，如果你要获取跟详细的信息，你应该使用 Wireshark (下面我们会介绍).

- [使用tcpdump抓包](https://www.52os.net/articles/tcpdump-capture-packages.html)
- [tcpdump man](http://www.tcpdump.org/tcpdump_man.html)
- [Linux tcpdump命令详解](http://www.cnblogs.com/ggjucheng/archive/2012/01/14/2322659.html)

### uptime

uptime 命令告诉你这台服务器从开机启动到现在已经运行了多长时间了。同时也包含了从启动到现在服务器的平均负载情况

### vmstat

你可以使用 vmstat 来监控虚拟内存，一般 Linux 上的开发者喜欢使用虚拟内存来获得最佳的存储性能。该命令报告关于内核线程、虚拟内存、磁盘、陷阱和 CPU 活动的统计信息。由 vmstat 命令生成的报告可以用于平衡系统负载活动。系统范围内的这些统计信息(所有的处理器中)都计算出以百分比表示的平均值，或者计算其总和。

### Wireshark

Wireshark, 前身是 Ethereal ，是一个网络协议检测程序，让您经由程序抓取运行的网站的相关资讯，包括每一封包流向及其内容、资讯可依操作系统语系看出,方便查看、监控TCP session动态等等.
