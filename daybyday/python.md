Python
==============

学习Python，学习他人的经验积累。

## 基础

[Python学习笔记](https://github.com/qyuhen/book)  

> Names have no type, but objects do.

**名字空间** LEGB规则

* L `locals`: 函数内部名字空间，包括局部变量和形参。
* E `enclosing function`: 外部嵌套函数的名字空间。
* G `globals`: 函数定义所在模块的名字空间。
* B `__builtins__`: 内置模块的名字空间。

> python 3 提供`nonlocal` 修改外部嵌套函数名字空间
> locals FAST区域优化。 使用`exec`可解决
> global 编译期作用域不受执行期条件影响

**闭包** closure

> 函数离开创建环境，依然持有其上下文关系。

**yield**

[python-yield](http://www.ibm.com/developerworks/cn/opensource/os-cn-python-yield/)

> yield 的作用就是把一个函数变成一个 generator，带有 yield 的函数不再是一个普通函数，Python 解释器会将其视为一个 generator。
> yield 的好处是显而易见的，把一个函数改写为一个 generator 就获得了迭代能力，比起用类的实例保存状态来计算下一个 next() 的值，不仅代码简洁，而且执行流程异常清晰。

比较清晰的例子：

```python
class Fab(object): 

    def __init__(self, max): 
        self.max = max 
        self.n, self.a, self.b = 0, 0, 1 

    def __iter__(self): 
        return self 

    def next(self): 
        if self.n < self.max: 
            r = self.b 
            self.a, self.b = self.b, self.a + self.b 
            self.n = self.n + 1 
            return r 
        raise StopIteration()
```

```python
def fab(max): 
    n, a, b = 0, 0, 1 
    while n < max: 
        yield b 
        # print b 
        a, b = b, a + b 
        n = n + 1 
```
> 仅仅把 print b 改为了 yield b，就在保持简洁性的同时获得了 iterable 的效果。

## Q&A

### BOM问题 (utf8)

这里很好的解决了我的问题  
http://stackoverflow.com/questions/12180376/utf8-python-bom  
http://stackoverflow.com/questions/934160/write-to-utf-8-file-in-python  
https://docs.python.org/2/library/codecs.html#encodings-and-unicode  

> 还有其他的回答
> utf-8编码本身是ascii兼容的，所以只有ascii字符的时候，“是否utf-8”编码是没有意义的。
> 如果要求文件可以被明确识别为utf8编码，则需要在文件头写入几个特殊字符（即UTF-8的BOM）

> 知乎里的一些了解 http://www.zhihu.com/question/20167122

查找包含BOM头的文件，命令如下： `grep -r -I -l $'^\xEF\xBB\xBF' ./`  `\xef\xbb\xbf`是`UTF8 BOM`的16进制表示  
把当前目录及所有子目录下的BOM头删除掉，二选一：
1. `find . -type f -exec sed -i 's/\xEF\xBB\xBF//' {} \;`
2. `echo -ne '\xef\xbb\xbf123456' | awk '{if(NR==1)sub(/^\xef\xbb\xbf/,""); print}'`

当然明智的VIM帮你解决：  
去掉或者加上bom，如果有vim那最好不过了，去掉命令：
`set encoding=utf-8` `set nobomb`
添加命令:
`set encoding=utf-8` `set bomb`

### 字符集

[由web程序出现乱码开始挖掘(Bom头、字符集与乱码）](http://www.cnblogs.com/chengmo/archive/2010/10/30/1864004.html)

> 什么是bom头？

bom全称是：byte order mark，汉语意思是标记字节顺序码。只是出现在：unicode字符集中，只有unicode字符集，存储时候，要求指定编码，如果不指定，windows还会用默认的：ANSI读取。常见的bom头是：

编码  |  头字节
----- | -----
UTF-8 | EF BB BF 
UTF-16LE | FF FE (小尾)
UTF-16BE | FE FF (大尾)
UTF-32LE | FF FE 00 00 
UTF-32BE | 00 00 FE FF

> unicode与utf-8 、utf-16 utf-32是什么关系？

unicode（统一码、万国码、单一码）是一种字符集，Unicode是国际组织制定的可以容纳世界上所有文字和符号的字符编码方案。Unicode用数字0-0x10FFFF来映射这些字符，最多可以容纳1114112个字符，或者说有1114112个码位。UTF-8、UTF-16、UTF-32都是将数字转换到程序数据的编码方案。在Unicode中：汉字“字”对应的数字是23383。我们可以用：UTF-8、UTF-16、UTF-32表示这个数字，将数字23383存储在计算机中。UTF-8对应是：0xE6, 0xB1, 0x89(3个字节）,UTF-16对应是：0x6c49（2个字节）,UTF-32对应是：0x6c49（4个字节）。utf-8,utf-16,utf-32是unicode码一种实现形式，都是属于unicode编码。

> unicode编码特点是什么？

unicode编码特点是，它定义了编码方式和存储实现方式。编码方式就是上面说的可以用，utf-8…utf-32表示，而存储实现方式，无论那种编码都知道了文件头(bom)。因此，可以通过这个特殊头来判断存储的文本文件使用那种字符集编码。 

### 开发规范

[以正确的方式开源 Python 项目](http://www.oschina.net/translate/open-sourcing-a-python-project-the-right-way)  
[Python风格规范](http://zh-google-styleguide.readthedocs.org/en/latest/google-python-styleguide/python_style_rules/)  


* SheBang: 大部分.py文件不必以#!作为文件的开始。程序的main文件应该以`#!/usr/bin/python`开始.

### 尾递归优化 Tail Call Optimization

[TCO-zhuhu](http://www.zhihu.com/question/20761771/answer/19996299)
[what-is-TCO](https://stackoverflow.com/questions/310974/what-is-tail-call-optimization)

> python (java/c#) 不能进行 TCO 。 最大深度 `sys.getrecursionlimit()` 1000?

