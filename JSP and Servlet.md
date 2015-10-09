###JSP中使用JavaBean对象
在JSP中使用JavaBean有两种方法：
1。在src下按照规则新建一个JavaBean类，然后再WebRoot下对应的jsp文件中，在声明里新建一个JavaBean类，然后赋值、使用。
2.或是使用JSp的动作标签，如：
```java
<jsp:useBean id="标识符(类似于Java中的变量名)" 
class="java类名(需要将包名一同写入)" scope="作用范围(默认是page)">
```
###Cookie对象
使Cookie失效的方法：cookie.setMax(0),也就是设置有效生命周期为0；注意修改cookie后需要重新保存添加cookie，否则不会生效
```java
response.addCookie(cookie)//重新保存更改后的cookie，否则不会起作用
```

###Session与Cookie的区别
|   Session  |   Cookie  |
|:------------:|:-----------:|
|保存在服务器端的内存中|保存在客户端，浏览器中|
|暂时的，退出会话后信息丢失|永久的，退出后不会丢失|
|session中保存的是`Object`类型|cookie中保存的是`String`类型|
|保存重要信息|保存不重要信息|

##Servlet
sevlet是运行在服务器端的小程序，本质上是一个java类，并且可以通过“请求-响应”编程模型来访问的这个驻留在服务器内存里的Servlet程序。

###Tomcat容器等级
Sevlet容器可以管理多个Context容器，一个Context对应一个web工程。

Servlet的继承关系：
1. 自定义Servlet继承自抽象类`HttpServlet`,一般重新doGet与doPost方法
2. `HttpServlet`是实现了http协议的servlet，是一个抽象类,继承自`GenericServlet`
3. `GenericServlet`是一个与协议无关的Servlet,是一个抽象类，继承自`Servlet`
4. `Servlet`是一个接口，有三个方法：`Init(),service(),destroy()`

编写Sevlet程序的步骤：
1. 继承HttpServlet抽象类
2. 重写doGet()或者doPost()方法
3. 在web.xml中注册Sevlet

>编写Servlet的doPost方法时，需要抛出`ServletExcpetion`和`IOException`异常，不会抛出`HttpServletException`异常。

假设在helloapp应用中有一个HelloServlet类，它在 web.xml文件中的配置如下:
```java
 <servlet>   
  <servlet-name>HelloServlet</servlet-name>    
  <servlet-class>org.javathinker.HelloServlet</servlet-class>
 </servlet>  
<servlet-mapping>   
  <servlet-name>HelloServlet</servlet-name>  
  <url-pattern>/hello</url-pattern>
</servlet-mapping>  
```
那么在浏览器端访问HelloServlet的URL是:`http://localhost:8080/helloapp/hello`
>localhost是服务器主机名，也可以是IP地址127.0.0.1；8080是tomcat服务器的端口号；helloapp是web工程的上下文地址ContexRoot（一般情况下与web工程名一致）；最后是<url-pattern/>标签中的内容。

###Servlet运行流程
1. 提供URL（超链接访问是Get方式请求）
2. 在web.xml中的通过<url-pattern>中的URL与用户访问提供的URL对接，找到<servlet-name>中的servlet
3. 再通过<servlet-class>找到对应的servlet处理程序，调用doGet方法，完成处理

###Servlet生命周期
servlet生命周期阶段包括初始化、加载、实例化、服务和销毁。
1. 客户端发送请求给服务器
2. 服务器开始接受，先判断该请求的servlet实例是否存在，如果不存在先调用构造方法装载一个servlet类并创建实例。如果存在则直接调用该servlet的service方法，由service方法判断是调用doGet方法还是doPost方法。
3. servlet创建实例后，调用init方法进行初始化。之后调用servce方法，由service方法判断是调用doGet方法还是doPost方法。
4. 最后判断服务是否关闭，如果关闭则调用destroy方法。

####Servlet的装载三种情况：
1. 自动装载：某些Servlet如果需要在Servlet容器启动时就加载，需要在web.xml下它的<Servlet>标签里中，添加优先级代码：
```java
<Servlet>
<load-on-startup>1<load-on-startup>
</Servlet>
```
数字越小表示该servlet的优先级越高，会先于其他自动装载的优先级较低的先装载
2. Servlet容器启动后，客户首次向某个Servlet发送请求时，Tomcat容器会加载它
3. 当Servlet类文件被更新后，也会重新自动加载（不用重启服务器，会自动先销毁服务器当前内存中的sevlet，然后重新自动加载修改后的servlet）

>Servlet是长期驻留在内存里的。某个Servlet一旦被加载，就会长期存在于服务器的内存里，直到服务器关闭。Servlet被装载后，Servlet容器创建一个Servlet实例并且调用Servlet的init()方法进行初始化。**在Servlet的整个生命周期内，init()方法只被调用一次**

在使用eclipse直接新建servlet文件时，会在创建的Servlet上自动添加注解`@WebServlet("/servlet/ServletLifeCircle1")`，这个注解的功能相当于在web.xml中注册Servlet时写的代码：
```java
 <servlet>
    <servlet-name>ServletLifeCircle1</servlet-name>
    <servlet-class>servlet.ServletLifeCircle1</servlet-class>
  </servlet>
 <servlet-mapping>
    <servlet-name>ServletLifeCircle1</servlet-name>
    <url-pattern>/servlet/ServletLifeCircle1</url-pattern>
  </servlet-mapping> 
```
如果在注解中指定了该Servlet的url，那么在web.xml中就不用写上面的代码了，否则会冲突报错。使用注解的方式更加简洁方便
###JSP的九大内置对象与Servlet的对应关系
|JSP                 |servlet              |
|:--------------------:|:-----------------:|
|out | resq.getWriter()|
|reaquest |  service方法中的req参数| 
|response |   service方法中的resp参数|
|session  |   request.getSession()函数|
|application |  getServletContext()函数|
|exception |  Throwable|
|page |  this|
|pageContext|  pageContext|
|Config |  getServletConfig函数|


在Servlet中使用`request.getParameter(String param)`获取表单提交的参数数据时，`param`的名字要与请求jsp页面的`name`属性名称一致才可以，如：
```java
//在reg.jsp的表单中要提交的数据：
      <tr>
          <td class="label">自我介绍：</td>
          <td class="controler">
            <textarea name="introduce" rows="10" cols="40"></textarea>
          </td>
      </tr>
```
那么在相应的Servlet中获取该数据应该这样写：
```java
 introduce=request.getParameter("introduce"); //参数"introduce"要与jsp代码中的name属性值一致
```

*NOTE:*获取的属性值为单个的话使用`request.getParameter`获取
checkbox复选框返回的是一个字符串数组，因此必须要用`request.getParameterValues`来获取
javabean的属性值的设值和取值方法一定要命名不要与变量名称重复，如一开始犯的错误就是给User的是否接受条款属性起名为`isAccept`,结果在eclipse自动生成该方法的get方法时，方法的名称也为`isAccept()`,方法名与属性名一样，这样在Servlet中使用`<jsp:getProperty name="regUser" property="isAccept"/>`来调用getter方法时会取不到相应的属性值。

###JSP跳转Servlet的路径写法
1. 相对路径访问：相对于当前文件的路径。如href="servlet/TestServlet" ，如果写成`/servlet/TestServlet`会报错，因为第一个`/`表示的是【服务器根目录：http://localhost:8080/】
2. 绝对路径访问：使用内置成员变量path实现，如`href="<%=path%>/servlet/TestServlet"`。**requst.getContextPath()可以获得项目的根目录**，如：`/MyFirstSevletDemo`

**注意**：
jsp页面中的地址第一个`/`指的是tomcat服务器的根目录，即`http://localhost:8080/`.而配置文件`web.xml`的url-pattern中的第一个`/`指的是项目根目录，如`http://localhost:8080/Servlet002_GetFormDemo`。所以jsp页面链接地址不加`/`，配置文件中的url-patttern要加`/`。