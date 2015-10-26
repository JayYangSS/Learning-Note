###Filter的执行顺序
服务器会按照web.xml中过滤器定义的顺序组创出一条链，按顺序执行

Chain.doFilter是放行方法，它的作用是将请求转发给过滤器链上的下一个对象，这里的下一个对象是指下一个filter，如果没有filter，那就是你请求的资源。


###servlet2.5的4种过滤器：

1. REQUEST
直接请求资源默认使用的是REQUEST过滤器，使用请求重定向的方式`req.sendRedirect(req.getContextPath()+"/main.jsp")`使用的就是REQUEST过滤器，使用请求重定向的方式则不是使用`REQUEST`过滤器
2. FORWARD
使用`req.getRequestDispatcher("main.jsp").forward(request,response)`内部转发使用的是`FOWARD`过滤器
使用如下方式可以指定使用`FORWARD`方式的过滤器：

```xml
    <filter-mapping>
        <filter-name>FirstFilter</filter-name>
        <url-pattern>/main.jsp</url-pattern>
        <dispatcher>FORWARD</dispatcher>
    </filter-mapping>
```
资源main.jsp的过滤方式为`FORWARD`,也就是说对转发到目标main.jsp的请求过滤，如果直接访问目标资源main.jsp，该过滤器将不起作用。
另外使用如下JSP标签也是可以走FORWARD过滤器的：`<jsp:forward page="/main.jsp"></jsp:forward>`

3. INCLUDE
使用`req.getRequestDispatcher("main.jsp").include(request,response)`内部转发使用的是`INCLUDE`过滤器,使用如下标签也是可以走`INCLUDE`过滤器的`<jsp:include page="/main.jsp"></jsp:forward>`
下面的代码表示对包含了目标资源二的请求过滤，如果直接访问目标资源二，则此过滤器将不起作用。

```xml
   <filter-mapping>  
        <filter-name>myFilter</filter-name> 
        <servlet-name>目标资源二</servlet-name> 
        <dispatcher>INCLUDE</dispatcher> 
   </filter-mapping>
```
4. ERROR 
使用如下标签可以配置异常页面，当发生异常，会自动跳转到配置的error.jsp页面

```xml
 <error-page> 
    <error-code>404</error-code> 
    <location>/error.jsp</location> 
 </error-page>
```

使用ERROR过滤器，可以在发生异常跳转到异常页面之前，被ERROR过滤器拦截处理，之后再送往error.jsp页面。如下所示：

```java
class ErrorFilter{
    @Overide
    public void doFilter(ServletRequest arg0, ServletResponse arg1, FilterChain arg2){
        System.out.println("检测到有错误");
        arg2.doFilter(arg0,arg1);//过滤器处理后放行，否则请求就被拦截无法传递了
    }

    @Overide
    public void init(FilterConfig arg0) throws ServletException{
        //TODO:
    }
}
```
然后再web.xml中配置过滤器：

```xml
<filter>
    <filter-name>ErrorFilter</filter-name>
    <filter-class>com.imooc.ErrorFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>ErrorFilter</filter-name>
    <url-pattern>/error.jsp</url-pattern>
    <dispatcher>ERROR</dispatcher>
</filter-mapping>
```
**NOTE:**转发是服务器行为，重定向是客户端行为.请求重定向相当于客户端向服务器发送了两次请求，用户会看到URL的变化。内部转发是客户端向服务器发送一次请求，服务器内部将这个请求的处理权转发给另外一个，用户不会看到自己URL的变化

![servlet过滤器](http://7xniym.com1.z0.glb.clouddn.com/servlet%20filter.png)

###servlet3.0新增的过滤器：
5. ASYNC
@WebFilter 用于声明类
![@WebFilter](http://7xniym.com1.z0.glb.clouddn.com/WebFilter.png)
