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


层次化的applicationContext加载：有一个Spring上下文环境配置文件，还有不同DispatcherSevlet的上下文环境配置文件，具体请看下图：
![applicationContext](http://7xniym.com1.z0.glb.clouddn.com/dispatcherContext.png)

###@RequestMapping的使用

@RequestMapping用来定义访问的URL，你可以为整个类定义一个@RequestMapping，或者为每个方法指定一个。 把@RequestMapping放在类级别上，这可令它与方法级别上的@RequestMapping注解协同工作，取得缩小选择范围的效果。 

完整的参数项为：`@RequestMapping(value="",method ={"",""},headers={},params={"",""})`

`@RequestMapping params`的说明，可以通过设置参数条件来限制访问地址，例如`params="myParam=myValue"`表达式，访问地址中参数只有包含了该规定的值`"myParam=myValue"`才能匹配得上，类似`"myParam"`之类的表达式也是支持的，表示当前请求的地址必须有该参数(参数的值可以是任意)，`"!myParam"`之类的表达式表明当前请求的地址不能包含具体指定的参数`"myParam"`。如：

```java
@RequestMapping("/bbs.do")  
public class BbsController {  
    @RequestMapping(params = "method=getList")  // /bbs.do?method=getList可以访问到方法getList()
    public String getList() {  
     return "list";  
    }  
@RequestMapping(value= "/spList")  
public String getSpecialList() {  
     return "splist";  
    }  
}  
```
同样的道理，请求的header中必须含有headers指定的内容才可以访问到该方法，如：
```java
@Controller
@RequestMapping("/owners/{ownerId}")
public class RelativePathUriTemplateController {

    @RequestMapping(path = "/pets", method = RequestMethod.GET, headers="myHeader=myValue")
    public void findPet(@PathVariable String ownerId, @PathVariable String petId, Model model) {
        // implementation omitted
    }

}
```

###注解@PathVarible与@RequestParam的使用

下面代码把URI template 中变量 `courseId`的值绑定到方法的参数上。若方法参数名称和需要绑定的uri template中变量名称不一致，需要在@PathVariable("name")指定uri template中的名称。
```java
//本方法将处理 /courses/view?courseId=123 形式的URL
    @RequestMapping(value="/view", method=RequestMethod.GET)
    public String viewCourse(@RequestParam("courseId") Integer courseId,
            Model model) {
        log.debug("In viewCourse, courseId = {}", courseId);
        Course course = courseService.getCoursebyId(courseId);
        model.addAttribute(course);
        return "course_overview";
    }
    
    //本方法将处理 /courses/view2/123 形式的URL(RESTFUL风格的URL)
    @RequestMapping("/view2/{courseId}")
    public String viewCourse2(@PathVariable("courseId") Integer courseId,
            Map<String, Object> model) {
        
        log.debug("In viewCourse2, courseId = {}", courseId);
        Course course = courseService.getCoursebyId(courseId);
        model.put("course",course);
        return "course_overview";
    }
```

###@ResponseBody
该注解的作用是将处理函数的返回值绑定到服务器的HTTP响应体中(HTTP Response body),即返回值放入HTTP响应体中返回给客户端而不是放入一个`Model`中或者被当作一个`view name`，如下代码，返回"success"放入HTTP响应体中，如果去掉这个注解，则返回值会被spring mvc处理成`ModelAndView`.(或者我理解为View Name).
```java
@RequestMapping(value = "/login", method = RequestMethod.GET, produces = MediaType.APPLICATION_JSON_VALUE)
    @ResponseBody
    public String login(HttpServletRequest request, Model model) {
        return "success";
    }
```
和 @RequestBody类似，@ResponseBody注解使springMVC通过`HttpMessageConverter`将函数的返回值转换为HTTP response body.
可以使用@controller,@RequestMapping,@Responsebody可以实现REST API。(通过URL调用相应的函数返回需要的jason数据)。


###@ResponseBody
该注解的作用是将HTTP request body绑定到方法中被注解的参数上，如下所示：
```java
@RequestMapping(path = "/something", method = RequestMethod.PUT)
public void handle(@RequestBody String body, Writer writer) throws IOException {
    writer.write(body);
}
```
使用的是`HttpMessageConverter `将HTTP request body转化为方法中参数类型。