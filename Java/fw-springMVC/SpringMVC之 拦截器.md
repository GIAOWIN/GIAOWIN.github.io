# 1.拦截器的概述 

	1. SpringMVC框架中的拦截器用于对处理器进行预处理和后处理的技术。 
	2. 可以定义拦截器链，连接器链就是将拦截器按着一定的顺序结成一条链，在访问被拦截的方法时，拦截器链 中的拦截器会按着定义的顺序执行。 
	3. 拦截器和过滤器的功能比较类似，有区别 
		1). 过滤器是Servlet规范的一部分，任何框架都可以使用过滤器技术。 
		2). 拦截器是SpringMVC框架独有的。 
		3). 过滤器配置了/*，可以拦截任何资源。 
		4). 拦截器只会对控制器中的方法进行拦截。 
	4. 拦截器也是AOP思想的一种实现方式 
	5. 想要自定义拦截器，需要实现HandlerInterceptor接口。
# 2.自定义拦截器步骤

1. 创建类，实现HandlerInterceptor接口，重写需要的方法

   ```java
    /**
    * @author Yuan-9826
    */
   public class MyInterceptor1 implements HandlerInterceptor {
   
       /**
        * 预处理，controller方法执行前
        * return true 放行，执行下一个拦截器，如果没有，执行controller中的方法
        * return false不放行
        * @param request
        * @param response
        * @param handler
        * @return
        * @throws Exception
        */
       @Override
       public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
           System.out.println("MyInterceptor1拦截器成功了");
           return true;
       }
   
       /**
        * 后处理方法，controller方法执行后，success.jsp执行之前
        * @param request
        * @param response
        * @param handler
        * @param modelAndView
        * @throws Exception
        */
       @Override
       public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
           System.out.println("MyInterceptor1执行了...后2222");
           // request.getRequestDispatcher("/WEB-INF/pages/error.jsp").forward(request,response);
       }
   
       /**
        * success.jsp页面执行后，该方法会执行
        * @param request
        * @param response
        * @param handler
        * @param ex
        * @throws Exception
        */
       @Override
       public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
           System.out.println("MyInterceptor1执行了...最后2222");
       }
   }
   ```

2. 在springmvc.xml中配置拦截器类

   ```xml
   <!--配置拦截器-->
   <mvc:interceptors>
       <!--配置拦截器-->
       <mvc:interceptor>
           <!--要拦截的具体的方法-->
           <mvc:mapping path="/interceptor/*"/>
           <!--不要拦截的方法
           <mvc:exclude-mapping path=""/>
           -->
           <!--配置拦截器对象-->
           <bean class="com.yuan.interceptor.MyInterceptor1"/>
       </mvc:interceptor>
   </mvc:interceptors>
   ```

# 3.配置多个拦截器

```xml
 多个拦截器是按照配置的顺序决定的
<!--配置拦截器-->
<mvc:interceptors>
    <!--配置拦截器1-->
    <mvc:interceptor>
        <!--要拦截的具体的方法-->
        <mvc:mapping path="/interceptor/*"/>
        <!--不要拦截的方法
        <mvc:exclude-mapping path=""/>
        -->
        <!--配置拦截器对象-->
        <bean class="com.yuan.interceptor.MyInterceptor1"/>
    </mvc:interceptor>
</mvc:interceptors>
<!--配置拦截器2-->
<mvc:interceptors>
    <!--配置拦截器-->
    <mvc:interceptor>
        <!--要拦截的具体的方法-->
        <mvc:mapping path="/interceptor/*"/>
        <!--不要拦截的方法
        <mvc:exclude-mapping path=""/>
        -->
        <!--配置拦截器对象-->
        <bean class="com.yuan.interceptor.MyInterceptor2"/>
    </mvc:interceptor>
</mvc:interceptors>

```

