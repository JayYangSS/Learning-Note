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
使用@RequestParam注解可以将请求中的参数值绑定到注解修饰的参数上，并且会自动转换为指定参数的类型。@PathVariable也是参数绑定的注解，不同的是它是用于将被注解参数与URI template variable进行绑定。下面代码把URI template 中变量 `courseId`的值绑定到方法的参数上。若方法参数名称和需要绑定的uri template中变量名称不一致，需要在@PathVariable("name")指定uri template中的名称。参看如下代码可以对比两者绑定参数的用法不同之处：
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


###@RequestBody
该注解的作用是将HTTP request body绑定到方法中被注解的参数上，如下所示：
```java
@RequestMapping(path = "/something", method = RequestMethod.PUT)
public void handle(@RequestBody String body, Writer writer) throws IOException {
    writer.write(body);
}
```
使用的是`HttpMessageConverter `将HTTP request body转化为方法中参数类型。

###@ResponseStatus
使用@ResponseStatus注解的异常出现时，`ResponseStatusExceptionResolver`可以通过设置response的状态来处理该异常。示例如下：
```java
@ControllerAdvice//使用@ControllerAdvice,作用域是全局Controller范围
  public class AControllerAdvice {
      @ExceptionHandler(NullPointerException.class)//当出现NullPointerException异常时，执行被@ExceptionHandler注解的方法
      @ResponseStatus(HttpStatus.BAD_REQUEST)//当NullPointerException异常出现时，将response的状态置为HttpStatus.BAD_REQUEST
      @ResponseBody
      public String handleIOException(NullPointerException ex) {
         return ClassUtils.getShortName(ex.getClass()) + ex.getMessage();
     }
```
###@ControllerAdvice
@ControllerAdvice使用@Component注解，被@ControllerAdvice注解的类可以使用<context:component-scan>自动扫描到。
，如果不使用任何参数，则表示作用域是全局的@Controller，如果增加参数，表示@ControllerAdvice辅助的@Controller为指定范围内的Controller。请看示例：
```java
// Target all Controllers annotated with @RestController
@ControllerAdvice(annotations = RestController.class)
public class AnnotationAdvice {}

// Target all Controllers within specific packages
@ControllerAdvice("org.example.controllers")
public class BasePackageAdvice {}

// Target all Controllers assignable to specific classes
@ControllerAdvice(assignableTypes = {ControllerInterface.class, AbstractController.class})
public class AssignableTypesAdvice {}
```
可应用到所有@RequestMapping类或方法上的@ExceptionHandler、@InitBinder、@ModelAttribute,下面的代码表示处理所有抛出NullPointerException异常的Controller。
```java
@ControllerAdvice
  public class AControllerAdvice {
      @ExceptionHandler(NullPointerException.class)
      @ResponseStatus(HttpStatus.BAD_REQUEST)
      @ResponseBody
      public Strtring handleIOException(NullPointerException ex) {
          return ClassUtils.getShortName(ex.getClass()) + ex.getMessage();
     }
 }
```
把@ControllerAdvice注解内部使用@ExceptionHandler、@InitBinder、@ModelAttribute注解的方法应用到所有的 @RequestMapping注解的方法。非常简单，不过只有当使用@ExceptionHandler最有用，另外两个用处不大。
```java
@ControllerAdvice  
public class ControllerAdviceTest {  
  
    @ModelAttribute  
    public User newUser() {  
        System.out.println("============应用到所有@RequestMapping注解方法，在其执行之前把返回值放入Model");  
        return new User();  
    }  
  
    @InitBinder  
    public void initBinder(WebDataBinder binder) {  
        System.out.println("============应用到所有@RequestMapping注解方法，在其执行之前初始化数据绑定器");  
    }  
  
    @ExceptionHandler(UnauthenticatedException.class)  
    @ResponseStatus(HttpStatus.UNAUTHORIZED)  
    public String processUnauthenticatedException(NativeWebRequest request, UnauthenticatedException e) {  
        System.out.println("===========应用到所有@RequestMapping注解的方法，在其抛出UnauthenticatedException异常时执行");  
        return "viewName"; //返回一个逻辑视图名  
    }  
}  
```


###拦截器的使用

1. 创建一个实现HandlerInterceptor接口的类，这个类就是一个拦截器
2. 在spring mvc中注册这个拦截器
```xml
<mvc:interceptors>
  <mvc:mapping path="/viewAll.form"><!--拦截规则，只拦截以viewAll.form结尾的请求，这里的value可以使用正则表达式-->
  <bean class="拦截器的类名"></bean>
</mvc:interceptors>
```

3. 配置拦截器的拦截规则，使用示例请见上方代码


###多个拦截器的工作顺序

请看下图：
![多个拦截器](http://7xniym.com1.z0.glb.clouddn.com/多个拦截器执行问题.png)