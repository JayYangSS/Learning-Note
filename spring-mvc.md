##spring mvc的基本概念

###DispatcherServlet
前端控制器，用来将请求分发给合适的controller

###Controller
调用业务逻辑生成model，DispatcherServlet调用完controller后，会将controller其生成的Model或者Map转换为`ModelAndView`对象

###HandlerAdapter
`spring-mvc`中没有`controller`这个接口和类，调用controller是通过调用各种handler来调用controller的，而HandlerAdapter就是Handler适配器，可以将各种各样的的Handler适配成DispatcherSevlet可以使用的Handler,这样，HandlerAdapter可以很轻松的调用controller

###HandlerInterceptor
是一个接口，是一个拦截器，在需要被拦截对象的两侧添加一些处理程序。Handler调用之前，之后，以及view呈现后可以做很多事情(需要的话可以去实现)

###HandlerMapping
告诉`DispatcherServlet`，一个请求到来后，由哪个controller来响应请求，是一个类

###HandlerExceptionChain
在controller外面包裹一层`HandlerInterceptor`
`preHandle`--->`Controller method`--->`postHandle`--->`afterCompletion`

###ModelAndView
DispatcherServlet会统统将Model还有map统统自动变成ModelAndView来调用,这个其实就是MVC中的M。

###ViewResolver
根据配置找出对应的视图(view)对象，如jsp，jstl等

###View
负责呈现页面，如jstl,jsp等，DispatcherServlet得到View对象后，即调用View的render方法，执行真正的渲染工作。

下面是整个工作原理：
![spring-mvc](http://7xniym.com1.z0.glb.clouddn.com/2013-2-20-6-58-4-953.jpg)


#注解
在java类的上方添加标签`@Controller`，则可以使`spring-mvc`明白这个类是一个controller，添加`@RequestMapping("/hello")`可以指定将这个java类映射到哪个URL路径：
```java
@Controller
@RequestMapping("/hello")
public class HelloMVC{
    //使用//host:8080/hello/mvc即可访问到该方法（DispatcherServlet使用handlerMapping找到此方法）
    @RequestMapping("/mvc")
    public String hello(){
        return "home";
    }
} 
```