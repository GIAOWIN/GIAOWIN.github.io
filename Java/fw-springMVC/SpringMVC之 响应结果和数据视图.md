# 1.响应之返回值是String

```java
@RequestMapping("/stringTest")
public String stringTest(Model mode) {
    System.out.println("stringTest...............");
    User user = new User();
    user.setUsername("妹妹");
    user.setPassworld("meimei");
    user.setAge(21);
    mode.addAttribute("user", user);
    System.out.println("user = " + user);
    System.out.println(mode);
    return "success";
}
```



# 2.响应之返回值是void

```java
@RequestMapping("/voidTest")
public void voidTest(HttpServletRequest request, HttpServletResponse response) throws Exception {
	System.out.println("voidTest................");
//        转发
	request.getRequestDispatcher("/success.jsp").forward(request, response);
//        重定向
	response.sendRedirect(request.getContextPath()+"/response.jsp");
}
```



# 3.响应之返回值是ModeAndView

```java
@RequestMapping("/ModeAndViewTest")
public ModelAndView ModeAndViewTest(Model mode) {
    ModelAndView mv = new ModelAndView();
    User user = new User();
    
    user.setUsername("枚枚");
    user.setPassworld("meimei");
    user.setAge(21);
    
    // 把user对象存储到mv对象中，也会把user对象存入到request对象
    mv.addObject("user",user);

    // 跳转到哪个页面
    mv.setViewName("success");
    
    return mv;
}

```



# 4.响应之返回值是利用forward和redirect进行页面跳转

```java
/**
 * 使用关键字的方式进行转发或者重定向
 * @return
 */
@RequestMapping("/testForwardOrRedirect")
public String testForwardOrRedirect(){
    System.out.println("testForwardOrRedirect方法执行了...");

    // 请求的转发
    // return "forward:/WEB-INF/pages/success.jsp";

    // 重定向
    return "redirect:/index.jsp";
}

```



# 5.响应json数据之过滤静态资源

```xml
<!--Spring MVC配置文件-->
<!--前端控制器，哪些静态资源不拦截-->
<mvc:resources location="/css/" mapping="/css/**"/>
<mvc:resources location="/images/" mapping="/images/**"/>
<mvc:resources location="/js/" mapping="/js/**"/>
```



# 6.响应json数据之发送ajax的请求

```html
<script src="js/jquery.min.js"></script>

<script>
    // 页面加载，绑定单击事件
    $(function(){
        $("#btn").click(function(){
            // alert("hello btn");
            // 发送ajax请求
            $.ajax({
                // 编写json格式，设置属性和值
                url:"user/testAjax",
                contentType:"application/json;charset=UTF-8",
                data:'{"username":"hehe","password":"123","age":30}',
                dataType:"json",
                type:"post",
                success:function(data){
                    // data服务器端响应的json的数据，进行解析
                    alert(data);
                    alert(data.username);
                    alert(data.password);
                    alert(data.age);
                }
            });
        });
    });
</script>

```



# 7.响应json数据之响应json格式数据

```java
/**
 * 模拟异步请求响应
 */
@RequestMapping("/testAjax")
@ResponseBody
public User testAjax(@RequestBody User user){
    System.out.println("testAjax方法执行了...");
    // 客户端发送ajax的请求，传的是json字符串，后端把json字符串封装到user对象中
    System.out.println(user);
    // 做响应，模拟查询数据库
    user.setUsername("hiahia");
    user.setAge(40);
    // 做响应
    return user;
}
```

