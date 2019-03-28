# java dump 分析

```bash
#!/bin/bash

SERVICE_NAME="${project.artifactId}"
VERSION="${project.version}"
SERVICE_FULL_NAME=${SERVICE_NAME}-${VERSION}

# For tomcat service, this script is in folder ${SERVICE_FULL_NAME}/WEB-INF/classes,
#    cd ./../.. to reach base_dir.
# For other service, this script is in folder ${SERVICE_FULL_NAME}/bin, cd ./.. to reach base_dir
PRO_CUR_DIR=$(cd `dirname $0`; pwd)

pid=`ps -ef | grep ${SERVICE_FULL_NAME} | grep java | awk '{print $2}'`
if [ x"${pid}" == x ]; then
    # If service is a tomcat service
    pid=`ps -ef | grep ${SERVICE_NAME} | grep tomcat | grep java | awk '{print $2}'`
    if [ x"${pid}" != x ]; then
        # For tomcat service, cd out from WEB-INF/classes
        PRO_BASE_DIR=$(cd ${PRO_CUR_DIR}/../..; pwd)
    fi
else
    # For not tomcat service, cd out from bin
    PRO_BASE_DIR=$(cd ${PRO_CUR_DIR}/..; pwd)
fi

if [ x"${pid}" == x ]; then
    echo "NO INSTANCE OF ${SERVICE_FULL_NAME} IS RUNNING."
    exit 1
else
    echo "FOUND INSTANCE OF ${SERVICE_FULL_NAME} IS RUNNING..., pid:${pid}."
fi

DUMP_DIR=${PRO_BASE_DIR}/dump
if [ ! -d ${DUMP_DIR} ]; then
    mkdir ${DUMP_DIR}
fi

DUMP_TIME=`date +%Y%m%d%H%M%S`
TIME_DIR=${DUMP_DIR}/${DUMP_TIME}
if [ ! -d ${TIME_DIR} ]; then
    mkdir ${TIME_DIR}
fi

# http://bugs.java.com/bugdatabase/view_bug.do?bug_id=7050524
ptrace_scope=`cat /proc/sys/kernel/yama/ptrace_scope`
if [ ${ptrace_scope} -ne 0  ]; then
    echo "Change the value to 0 in /proc/sys/kernel/yama/ptrace_scope with root permission"
    sudo bash -c 'echo 0 > /proc/sys/kernel/yama/ptrace_scope'
fi

echo -e "Dumping the ${SERVICE_FULL_NAME} ...\c"
# 查看线程占用，死锁等问题
jstack ${pid} > ${TIME_DIR}/jstack-${pid}.dump 2>&1
echo -e ".\c"

# 查看进程的Java配置信息，具体包括Java系统属性和JVM命令行参数
jinfo ${pid} > ${TIME_DIR}/jinfo-${pid}.dump 2>&1
echo -e ".\c"

# 打印gc情况 http://docs.oracle.com/javase/7/docs/technotes/tools/share/jstat.html
## 包括各个内存分区使用百分比，YGC，FGC次数以及时间等
jstat -gcutil ${pid} > ${TIME_DIR}/jstat-gcutil-${pid}.dump 2>&1
echo -e ".\c"
## 包括各个内存分区的容量情况
jstat -gccapacity ${pid} > ${TIME_DIR}/jstat-gccapacity-${pid}.dump 2>&1
echo -e ".\c"

# jmap不指定选项时，打印共享对象的映射（起始地址、映射大小、共享对象文件的完整路径）
jmap ${pid} > ${TIME_DIR}/jmap-${pid}.dump 2>&1
echo -e ".\c"

# 打印堆情况的概要信息，包括堆配置、各堆空间的容量、已使用和空闲情况
jmap -heap ${pid} > ${TIME_DIR}/jmap-heap-${pid}.dump 2>&1
echo -e ".\c"

# 打印堆中对象统计信息，包括类、实例数、占用内存
jmap -histo ${pid} > ${TIME_DIR}/jmap-histo-${pid}.dump 2>&1
echo -e ".\c"

# 打印被进程打开的文件信息
if [ -r /usr/bin/lsof ]; then
    /usr/bin/lsof -p ${pid} > ${TIME_DIR}/lsof-${pid}.dump
    echo -e ".\c"
fi

# 打印与网络相关的一些统计信息
if [ -r /bin/netstat ]; then
    /bin/netstat -an | sort > ${TIME_DIR}/netstat.dump 2>&1
    echo -e ".\c"
fi

# 打印各文件系统的磁盘空间使用情况
if [ -r /bin/df ]; then
    /bin/df -h > ${TIME_DIR}/df.dump 2>&1
    echo -e ".\c"
fi

# 打印模块下各目录/文件磁盘空间使用情况
if [ -r /usr/bin/du ]; then
    /usr/bin/du -h --max-depth=1 > ${TIME_DIR}/du.dump 2>&1
    echo -e ".\c"
fi

# 打印与IO相关的一些统计信息
if [ -r /usr/bin/iostat ]; then
    /usr/bin/iostat 1 5 > ${TIME_DIR}/iostat.dump 2>&1
    echo -e ".\c"
fi

# 打印与CPU相关的一些统计信息
if [ -r /usr/bin/mpstat ]; then
    /usr/bin/mpstat 1 5 > ${TIME_DIR}/mpstat.dump 2>&1
    echo -e ".\c"
fi

# 打印进程、内存、内存分页
if [ -r /usr/bin/vmstat ]; then
    /usr/bin/vmstat 1 5 > ${TIME_DIR}/vmstat.dump 2>&1
    echo -e ".\c"
fi

# 内存
if [ -r /usr/bin/free ]; then
    /usr/bin/free -t > ${TIME_DIR}/free.dump 2>&1
    echo -e ".\c"
fi

# 主机运行时间、登陆用户数、系统过去的1分钟、5分钟、15分钟内的平均负载
if [ -r /usr/bin/uptime ]; then
    /usr/bin/uptime > ${TIME_DIR}/uptime.dump 2>&1
    echo -e ".\c"
fi

echo "FINISHED!"
echo "DUMP TO:${TIME_DIR}"
```

