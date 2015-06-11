Spring
================

### 面向切面编程

问题：由于没有采用面向接口的方式编程，代码编译时指定的trxDefService类型[foo.service.ServiceImpl]
和运行时通过(c3p0或者cglib)动态生成的类型 [$Proxy34] 不一致，所以导致service注入失败。

> REF
>* [面向接口的方式编程](http://tchen8.iteye.com/blog/902855)

我所遇到的现象

```java
@Autowired //@Resource
    private PushServiceImpl pushServiceImpl;
```

```xml
<bean id="pushServiceImpl" class="com.tplink.cloud.push.service.PushServiceImpl">
    <property name="pushDAO" ref="pushDAO" />
    <property name="dispatchService" ref="dispatchService" />
</bean>
```


`@Autowired`与`@Resource`控制台报错的表现情况不一样
* `@Autowired`
     Injection of autowired dependencies failed; nested exception is
     org.springframework.beans.factory.BeanCreationException: Could not autowire field:
     private com.tplink.cloud.push.service.PushServiceImpl
     com.tplink.cloud.push.service.PushServiceImplTest.pushServiceImpl;
     nested exception is org.springframework.beans.factory.NoSuchBeanDefinitionException:
     No matching bean of type [com.tplink.cloud.push.service.PushServiceImpl] found for dependency:
     expected at least 1 bean which qualifies as autowire candidate for this dependency.
     Dependency annotations: {@org.springframework.beans.factory.annotation.Autowired(required=true)}
* `@Resource`
    Injection of resource dependencies failed; nested exception is
    org.springframework.beans.factory.BeanNotOfRequiredTypeException: Bean named
    'pushServiceImpl' must be of type [com.tplink.cloud.push.service.PushServiceImpl],
    but was actually of type [com.sun.proxy.$Proxy14]

应该的代码

```java
@Autowired //@Resource
    private IPushService pushServiceImpl;
```
```java
public class PushServiceImpl implements IPushService {
    // ...
}
```

### 事务相关，面向切面

> REF
>* [Spring in Action 入门之面向切面编程AOP](http://www.cnblogs.com/yanghuahui/archive/2012/11/02/2750970.html)
>

问题：对于事务管理，如何来切面？

我的配置
```java
public class PushDAO extends CommonDAO {
}
```

```xml
    <!-- tx config -->
    <bean id="txManager"
        class="org.springframework.orm.hibernate3.HibernateTransactionManager">
        <property name="sessionFactory" ref="sessionFactory"></property>
    </bean>

    <!-- transaction -->
    <aop:config> <!-- <aop:config proxy-target-class="true"> -->
        <aop:advisor advice-ref="txAdvice"
            pointcut="execution(* com.tplink.cloud.push.database.PushDAO.*(..))" />
    </aop:config>

    <tx:advice id="txAdvice" transaction-manager="txManager">
        <tx:attributes>
            <tx:method name="get*" propagation="SUPPORTS"
                read-only="true" />
            <tx:method name="find*" propagation="SUPPORTS"
                read-only="true" />
            <tx:method name="*" propagation="REQUIRED"
                rollback-for="Exception" />
        </tx:attributes>
    </tx:advice>
```

总结：Spring 注入的是接口，关联的是实现类。 这里注入了实现类，所以报异常了。
使用cgilib来实现AOP，定义`proxy-target-class="true"`；`proxy-target-class`属性值决定是基于接口的还是基于类的代理被创建。
如果`proxy-target-class`属性值被设置为`true`，那么基于类的代理被创建,产生的代理对象会`instanceof`原来的类。

**切面的坑 CGLIB动态代理**  
通AOP切到的方法，final方法无法被代理调用。

那么`CGLIB`到底如何实现动态代理呢？

http://my.oschina.net/tryUcatchUfinallyU/blog/121668