Hibernate
========

## Hibernate是什么
Hibernate是一个开放源代码的对象关系映射框架，它对JDBC进行了非常轻量级的对象封装，
使得Java程序员可以随心所欲的使用对象编程思维来操纵数据库。
Hibernate可以应用在任何使用JDBC的场合，既可以在Java的客户端程序使用，
也可以在Servlet/JSP的Web应用中使用，最具革命意义的是，
Hibernate可以在应用EJB的J2EE架构中取代CMP，完成数据持久化的重任。

* Session: Session接口负责执行被持久化对象的CRUD操作，Session对象是非线程安全的。
* SessionFactory: SessionFactory接口负责初始化Hibernate。它充当数据存储源的代理，并负责创建Session对象。
* Transaction:
* Query: HQL语言或本地数据库的SQL语句。
* Criteria: 允许创建并执行面向对象的标准化查询。
* Configuration
    * 参数配置 [hibernate-conf][]


[hibernate-conf]: http://www.cnblogs.com/elleniou/archive/2012/12/01/2797546.html

配置方式：
* Annotation (jpa)
* XML (hibernate)

这里有份较完整的介绍[hibernate-study][]

## 主键生成器

`native` `identity` `sequence` `uuid`
```xml
<id name="id" >
  <generator class="native"></generator>
</id>
```
## 关系映射/组件映射

### lazy load问题

* cglib?
* [lazy][]?

[lazy]: http://www.cnblogs.com/linbaoji/archive/2009/01/07/1370919.html


## 缓存管理
* 一级缓存 Session级
* 二级缓存 SessionFactory级


## Query

* NativeSQL > HQL. > EJBQL(JPQL 1.0) > QBC(Query By Criteria) > QBE(Query By Example)
* list和iterate的不同。
  1. list取所有
  2. iterate先取 ID,等用到的时候再根据ID来取对象
  3. session中list第二次发出，仍会到数据库査询
  4. iterate 第二次，首先找session级缓存

## 事务ACID

## Q&A
1. 外键关联查询提示session closed!
   > 在`x.hbm.xml`中的一对多关系`<set>`上加上`lazy="false"`



[hibernate-study]: http://www.cnblogs.com/alamps/archive/2012/05/13/2498332.html
