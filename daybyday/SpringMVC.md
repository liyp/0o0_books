# SpringMVC


## RESTfull
ref: [rest-springMVC](http://www.ibm.com/developerworks/cn/web/wa-spring3webserv/)

开发环境

* IDE：Eclipse IDE for JEE (v3.4+)
* Java SE5 以上
* Web 容器：Apache Tomcat 6.0（Jetty 或其他容器也可）
* Spring 3 框架（v3.0.3 是本文编写时的最新版本）
* 其他程序库：JAXB 2、JSTL、commons-logging

> - web.xml 文件来激活 Spring WebApplicationContext
> - rest-servlet.xml 包含与 MVC/REST 有关的配置
> - rest-context.xml 包含服务级别的配置（如数据源 beans）

在 web.xml 中激活 Spring WebApplicationContext
```xml
<context-param>
<param-name>contextConfigLocation</param-name>
<param-value>
	/WEB-INF/rest-context.xml
</param-value>
</context-param>
	
<!-- This listener will load other application context file in addition to 
            rest-servlet.xml -->
<listener>
<listener-class>
	org.springframework.web.context.ContextLoaderListener
</listener-class>
</listener>
	
<servlet>
<servlet-name>rest</servlet-name>
<servlet-class>
	org.springframework.web.servlet.DispatcherServlet
</servlet-class>
<load-on-startup>1</load-on-startup>
</servlet>

<servlet-mapping>
<servlet-name>rest</servlet-name>
<url-pattern>/service/*</url-pattern>
</servlet-mapping>
```

在 rest-servlet.xml 文件中创建 Spring MVC 配置
```xml
<context:component-scan base-package="dw.spring3.rest.controller" />

<!--To enable @RequestMapping process on type level and method level-->
<bean class="org.springframework.web.servlet.mvc.annotation.DefaultAnnotationHandlerMapping" />
<bean class="org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter" />

<!--Use JAXB OXM marshaller to marshall/unmarshall following class-->
<bean id="jaxbMarshaller"
class="org.springframework.oxm.jaxb.Jaxb2Marshaller">
<property name="classesToBeBound">
	<list>
	    <value>dw.spring3.rest.bean.Employee</value>
	    <value>dw.spring3.rest.bean.EmployeeList</value>
	</list>
</property>
</bean>

<bean id="employees" class=
	"org.springframework.web.servlet.view.xml.MarshallingView">
<constructor-arg ref="jaxbMarshaller" />
</bean>

<bean id="viewResolver" class=
"org.springframework.web.servlet.view.BeanNameViewResolver" />
```

- Component-scan
启用对带有 Spring 注释的类进行自动扫描
在实践中，它将检查控制器类中所定义的 @Controller 注释。
- DefaultAnnotationHanlderMappings 和 AnnotationMethodHandlerAdapter
使用 @ReqeustMapping 注释的类或函数的 beans 由 Spring 处理
这个注释将在下一节进行详细介绍。
- Jaxb2Mashaller
定义使用 JAXB 2 进行对象 XML 映射（OXM）的编组器（marshaller）和解组器（unmarshaller
- MashallingView
定义一个使用 Jaxb2Mashaller 的 XML 表示 view
- BeanNameViewResolver
使用用户指定的 bean 名称定义一个视图解析器 
本例将使用名为 “employees” 的 MarshallingView。

dw.spring3.rest.controller.EmployeeController
```java
@Controller
publicclass EmployeeController {
@RequestMapping(method=RequestMethod.GET, value="/employee/{id}")
public ModelAndView getEmployee(@PathVariable String id) {
	Employee e = employeeDS.get(Long.parseLong(id));
	returnnew ModelAndView(XML_VIEW_NAME, "object", e);
}
}
```
@RequestMapping 注释是 Spring REST 特性的关键所在。它指定所注释的方法将处理哪个 HTTP 方法（RequestMethod.GET）和哪个 URI（/employee/{id}）。注意：

- 对于 {id} 占位符，使用 @PathVariable 注释可以将 {} 内的值注入到函数的参数。
- XML_VIEW_NAME 为 employees，这是 rest-servlet.xml 中定义的视图名称。
- employeeDS 是一个基于内存的数据源，它的实现已经超出本文写作范围。

将 Web 应用发布到您的 Tomcat 上。这时，您可以打开浏览器，然后输入 `http://<host>:<port>/<appcontext>/service/employee/1`。浏览器就会显示一个 ID 为 1 的员工信息的 XML 视图。

### 方法
资源操作是通过 HTTP 方法实现的，如 GET、POST、PUT 和 DELETE。您在前面已经了解了如何使用 GET 方法查询员工信息。现在我们将介绍 POST、PUT 和 DELETE。
通过使用 @RequestMapping 注释的功能，处理不同方法的代码是非常相似的。清单 4 显示了 EmployeeController 的代码片断。
清单 4. dw.spring3.rest.controller.EmployeeController
```java
@RequestMapping(method=RequestMethod.POST, value="/employee")
public ModelAndView addEmployee(@RequestBody String body) {
	Source source = new StreamSource(new StringReader(body));
	Employee e = (Employee) jaxb2Mashaller.unmarshal(source);
	employeeDS.add(e);
	returnnew ModelAndView(XML_VIEW_NAME, "object", e);
}

@RequestMapping(method=RequestMethod.PUT, value="/employee/{id}")
public ModelAndView updateEmployee(@RequestBody String body) {
	Source source = new StreamSource(new StringReader(body));
	Employee e = (Employee) jaxb2Mashaller.unmarshal(source);
	employeeDS.update(e);
	returnnew ModelAndView(XML_VIEW_NAME, "object", e);
}

@RequestMapping(method=RequestMethod.DELETE, value="/employee/{id}")
public ModelAndView removeEmployee(@PathVariable String id) {
	employeeDS.remove(Long.parseLong(id));
	List<Employee> employees = employeeDS.getAll();
	EmployeeList list = new EmployeeList(employees);
	returnnew ModelAndView(XML_VIEW_NAME, "employees", list);
}
```
在上面的代码中：

- RequestMethod.<Method> 的值确定所注释的函数应该处理哪个 HTTP 方法。
- 通过 @RequestBody，HTTP 请求的主体内容可以作为一个参数注入。
在本例中，主体内容是表示员工信息的 XML 数据。我们使用 JAXB 2 来将 XML 解组为 Java Bean，然后将它存储。一个示例请求主体可以是：
`<employee><id>3</id><name>guest</name></employee>`
- 其他可以注入到函数参数的有用的注释有 @PathVariable、@RequestParm 等等。Spring 文档有完整列表的注释。

### 资源集合
通常，您还需要操作批量的资源。例如，您可能希望获取所有员工的信息而不只是一个员工的信息。您可以采取类似于之前情况的方法实现；您所需要做的修改就是将 URI 从 /employee 修改成 /employees。员工的复数形式能够正确反映批量的语义。清单 5 显示了这种实现方法。
清单 5. EmployeeController 的 getAllEmployees
```java
@RequestMapping(method=RequestMethod.GET, value="/employees")
public ModelAndView getEmployees() {
	List<Employee> employees = employeeDS.getAll();
	EmployeeList list = new EmployeeList(employees);
	returnnew ModelAndView(XML_VIEW_NAME, "employees", list);
}
```
您需要为 Employee 集合声明一个包装类。这个包装类是 JAXB 2 所需要的，因为它无法正确地编组 java.util.List 类。清单 6 显示了 EmployeeList 类。
清单 6. dw.spring3.rest.bean.EmployeeList
```java
@XmlRootElement(name="employees")
publicclass EmployeeList {
	privateint count;
	private List<Employee> employees;

	public EmployeeList() {}
	
	public EmployeeList(List<Employee> employees) {
		this.employees = employees;
		this.count = employees.size();
	}

	publicint getCount() {
		return count;
	}
	publicvoid setCount(int count) {
		this.count = count;
	}
	
	@XmlElement(name="employee")
	public List<Employee> getEmployees() {
		return employees;
	}
	publicvoid setEmployees(List<Employee> employees) {
		this.employees = employees;
	}
	
}
```

### 内容协商
REST 服务的另一个常用特性是它们能够根据请求产生不同的表示。例如，如果客户端请求所有员工的 HTML/text 表示方式，那么服务器就应该为用户产生一个符合语法规则的 HTML 页面。如果客户端请求的是员工的 application/XML 表示方式，那么服务器就应该产生一个 XML 结果。其他受欢迎的表示方式还有 ATOM 和 PDF。
Spring 3 引入了一个名为 ContentNegotiatingViewResolver 的新视图解析器。它可以根据请求的内容类型（请求头中的 Accept 属性）或 URI 后缀来切换视图解析器。下面的例子使用 ContentNegotiatingViewResolver 来实现多种表示方式的支持。
在 rest-servlet.xml 文件中，用注释去掉原来定义的 viewResolver。而使用 ContentNegotiatingViewResolver 来替代它，如 清单 7 所示。
清单 7. 定义内容协商
```xml
<bean class="org.springframework.web.servlet.view.ContentNegotiatingViewResolver">
<property name="mediaTypes">
	<map>
	<entry key="xml" value="application/xml"/>
	<entry key="html" value="text/html"/>
	</map>
</property>
<property name="viewResolvers">
	<list>
	<bean class="org.springframework.web.servlet.view.BeanNameViewResolver"/>
	<bean class="org.springframework.web.servlet.view.UrlBasedViewResolver">
		<property name="viewClass" value=
		"org.springframework.web.servlet.view.JstlView"/>
		<property name="prefix" value="/WEB-INF/jsp/"/>
		<property name="suffix" value=".jsp"/>
	</bean>
	</list>
</property>
</bean>
```
这个定义显示了处理两种请求内容的支持：application/xml 和 text/html。这段代码也定义了两个视图解析器：其中 BeanNameViewResolver 是负责处理 application/xml 的，而另一个 UrlBasedViewResolver 则负责处理 text/html。
在实践中，当您在浏览器上输入 http://<host>:<port>/<appcontext>/service/employees，那么它就会请求 text/html 形式的所有员工信息。然后 UrlBasedViewResolver 会进行解析，而 Spring 将会选择 /WEB-INF/jsp/employees.jsp 作为返回的视图。当您添加请求头 Accept:application/xml 并再发起请求时，那么 BeanNameViewResolver 就会进行解析。如 清单 5 中的代码，它将使用一个名为 employees 的视图来表示，它就是所定义的 JAXB 2 编组器视图。
getAllEmployees() 的控制器代码不需要修改。employees.jsp 将会使用名为 employees 的模型对象来渲染。清单 8 显示了 employees.jsp 的代码片断。
清单 8. /WEB-INF/jsp 中的 employees.jsp
```
<table border=1>
	<thead><tr>
		<th>ID</th>
		<th>Name</th>
		<th>Email</th>
	</tr></thead>
	<c:forEach var="employee" items="${employees.employees}">
	<tr>
		<td>${employee.id}</td>
		<td>${employee.name}</td>
		<td>${employee.email}</td>
	</tr>
	</c:forEach>
</table>
```

## 与 REST 服务通信的客户端
现目前为止，您已经开发一个简单的支持对员工信息的 CRUD（增删查改）操作的 RESTful Web Service。接下来这一节将介绍如何与这个服务进行通信。您将使用 curl 来测试这个 REST 服务。
您也可以使用名为 RESTClient 的 Firefox 插件来测试 REST 服务。它很容易使用且带有良好的 UI。
### 使用 curl
Curl 是一个流行的能以 HTTP 和 HTTPS 协议向服务器发送请求的命令行工具。Curl 是 Linux® 和 Mac® 上的一个内置工具。对于 Windows® 平台，您可以另外下载这个工具（见 参考资料）。
要初始化查询所有员工信息的第一个 curl 命令，您可以输入：
`curl –HAccept:application/xml 
http://localhost:8080/rest/service/employees`
它的响应将是 XML 格式的，并且包含所有的员工信息。包含员工姓名、ID 和电子邮件的 XML 输出
您也可以在浏览器上尝试访问相同的 URL。这时，头信息中的 Accept 为 text/html，所以浏览器会显示 employees.jsp 中定义的一个表格。图 2 显示的是这种情况。
图 2. HTML 方式表示的所有员工信息
有一些数据与图 1 相同，但是这些数据是显示在表格中的
要将一个新的员工信息 POST 到服务器上，我们可以使用下面的代码。清单 4 中的 addEmployee() 代码将会使用请求体并将它解组为 Employee 对象。
`curl -X POST -HContent-type:application/xml --data
"<employee><id>3</id><name>guest</name><email>guest@ibm.com</employee>"
http://localhost:8080/rest/service/employee`
这样就添加了一个新的员工信息。您可以使用第一个例子来验证员工列表。
PUT 方法类似于 POST。
`curl -X PUT -HContent-type:application/xml --data
"<employee><id>3</id><name>guest3</name><email>guest3@ibm.com</employee>" 
http://localhost:8080/rest/service/employee/3`
上面的代码修改了 ID 为 3 的员工数据。