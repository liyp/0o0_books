# Shell

学习Linux Shell脚本知识，不管再运维还是日常开发操作都会非常受用。

- [Advanced Bash-Scripting Guide](http://tldp.org/LDP/abs/html/)
- [unix shell tutorial](http://www.tutorialspoint.com/unix/unix-shell.htm)
- [Linux Shell Scripting Tutorial](http://bash.cyberciti.biz/guide/Main_Page)

- [linux shell scripting cookbook]()
- [linux man shell](http://man.linuxde.net/)


参数处理 | 说明
---- | ----
`$#` | 传递到脚本的参数个数
`$*` | 以一个单字符串显示所有向脚本传递的参数
`$$` | 脚本运行的当前进程ID号
`$!` | 后台运行的最后一个进程的ID号
`$@` | 与`$*`相同，但是使用时加引号，并在引号中返回每个参数。
`$-` | 显示Shell使用的当前选项，与`set`命令功能相同。
`$?` | 显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误。
