# Java Serialization

* [https://docs.oracle.com/javase/7/docs/platform/serialization/spec/serialTOC.html](https://docs.oracle.com/javase/7/docs/platform/serialization/spec/serialTOC.html)

在序列化类中，final变量不会被重新赋值：

1. 通过构造函数为fina变量赋值
2. 通过方法返回值final变量赋值
3. final修饰的属性不是基本类型

保存在磁盘的对象文件包含：

1. 类描述信息

    包括，包路径，继承关系，访问权限，变量描述，变量访问权限，方法签名，返回值，变量的关联类信息

    不记录，方法，构造函数，static变量等

2. 非瞬态（transient）非静态（static）的实例变量值

