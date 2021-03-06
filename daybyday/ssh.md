# SSH

## ssh tunnel

* [利用SSH Tunnel帮助内网的机器连接互联网](https://www-304.ibm.com/connections/blogs/liuxx/entry/%e5%88%a9%e7%94%a8ssh_tunnel%e5%b8%ae%e5%8a%a9%e5%86%85%e7%bd%91%e7%9a%84%e6%9c%ba%e5%99%a8%e8%bf%9e%e6%8e%a5%e4%ba%92%e8%81%94%e7%bd%9158?lang=tr_tr)
* [实战 SSH 端口转发](https://www.ibm.com/developerworks/cn/linux/l-cn-sshforward/)

### 正向代理

## ssh commands

```bash
# 复制SSH密钥到目标主机，开启无密码SSH登录
ssh-copy-id user@host
# or
cat ~/.ssh/id_rsa.pub | ssh user@machine “mkdir ~/.ssh; cat >> ~/.ssh/authorized_keys”
# 一步将SSH公钥传输到另一台机器
ssh-keygen; ssh-copy-id user@host; ssh user@host

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

# 对所有数据请求压缩
ssh -C host

# 创建到目标主机的持久化连接
ssh -MNf <user>@<host>
#在后台创建到目标主机的持久化连接，将这个命令和你~/.ssh/config中的配置结合使用：
#
#Host host
#ControlPath ~/.ssh/master-%r@%h:%p
#ControlMaster no
#所有到目标主机的SSH连接都将使用持久化SSH套接字，如果你使用SSH定期同步文件（使用rsync/sftp/cvs/svn），这个命令将非常有用，因为每次打开一个SSH连接时不会创建新的套接字。

# 通过SSH将MySQL数据库复制到新服务器
mysqldump –add-drop-table –extended-insert –force –log-error=error.log -uUSER -pPASS OLD_DB_NAME | ssh -C user@newhost “mysql -uUSER -pPASS NEW_DB_NAME”

# 实时SSH网络吞吐量测试
yes | pv | ssh $host “cat > /dev/null”
```

## ssh 远程命令执行过程中，环境变量问题

> [http://feihu.me/blog/2014/env-problem-when-ssh-executing-command-on-remote/](http://feihu.me/blog/2014/env-problem-when-ssh-executing-command-on-remote/)

