# `Spring`中的统一功能处理

## 1. 拦截器

拦截器是Spring框架提供的核心功能之一，主要用来拦截用户的请求，在指定方法前后，根据业务需要执行预先设定的代码。

在拦截器当中，开发人员可以在应用程序中做一些通用性的操作，比如通过拦截器来拦截前端发来的请求，判断Session中是否有登录用户的信息。如果有就可以放行，如果没有就进行拦截。

在Spring中，使用拦截器非常方便，只需==定义拦截器+注册使用==即可：

```java
// 定义拦截器
@Slf4j
@Component
public class LoginInterceptor implements HandlerInterceptor {
  @Override
  public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
    log.info("LoginInterceptor ⽬标⽅法执⾏前执⾏..");
    return true;
  }
  @Override
  public void postHandle(HttpServ letRequest request, HttpServletResponse
  response, Object handler, ModelAndView modelAndView) throws Exception {
  	log.info("LoginInterceptor ⽬标⽅法执⾏后执⾏");
  }
  @Override
  public void afterCompletion(HttpServletRequest request,
  HttpServletResponse response, Object handler, Exception ex) throws Exception {
  	log.info("LoginInterceptor 视图渲染完毕后执⾏，最后执⾏");
  }
}
```

```java
// 注册拦截器
@Configuration
public class WebConfig implements WebMvcConfigurer {
  //⾃定义的拦截器对象
  @Autowired
  private LoginInterceptor loginInterceptor;
  @Override
  public void addInterceptors(InterceptorRegistry registry) {
    //注册⾃定义拦截器对象
    //设置拦截器拦截的请求路径（ /** 表⽰拦截所有请求）
    registry.addInterceptor(loginInterceptor)
    .addPathPatterns("/**");
  }
}
```

#### 拦截器的作用时间

![image-20240607085158949](E:\Note\Java\JavaEE进阶\Spring中的统一功能处理.assets\image-20240607085158949.png)

#### DispatcherServlet源码分析  

## 2. 统一数据返回格式





## 3. 统一异常处理

















​    