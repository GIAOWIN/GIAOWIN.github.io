# @RequestMapping注解 

```
1. RequestMapping注解的作用是建立请求URL和处理方法之间的对应关系 
2. RequestMapping注解可以作用在方法和类上 
   1. 作用在类上：第一级的访问目录 
   2. 作用在方法上：第二级的访问目录 
   3. 细节：路径可以不编写 / 表示应用的根目录开始 
   4. 细节：${ pageContext.request.contextPath }也可以省略不写，但是路径上不能写 / 
3. RequestMapping的属性 
   1. path 指定请求路径的url 
   2. value		value属性和path属性是一样的 
   3. mthod		指定该方法的请求方式 
   4. params	指定限制请求参数的条件 
   5. headers	发送的请求中必须包含的请求头
```

# 常用注解

## @RequestParam 

**范围：**方法参数上(value = “表单属性名”)

**作用：**使 由于页面和传入参数的变量名不同导致 的 赋值失败情况变为成功

**例子：**

```java
    @RequestMapping("/param")
    public String paramUser(@RequestParam("uname") String username){
        System.out.println("username = " + username);
        return "param" ;
    }

```

## @RequestBody 

**范围：**方法参数上

**作用：**用于获取请求体的内容（注意：get方法不可以）以键值对的形式aa=AA&bb=BB

**例子：**

```java
    @RequestMapping("/param")
    public String paramUser(@RequestBody String body){
        System.out.println("body = " + body);
        return "param" ;
    }
```

## @PathVaribale

**范围：**方法参数上(value = “restful路径上的占位符”)

**作用：**把路径上的值取到赋值给方法上的形参

**例子：**

```
    @RequestMapping("/param/{qid}")
    public String paramUser(@PathVariable("qid") Integer id){
        System.out.println("id = " + id);
        return "param" ;
    }

```

## @RequestHeader

**范围：**方法参数上value = “想要获取值的头名“

**作用：**获取指定请求头的值

**例子：**

```java
@RequestMapping("/param")
public String paramUser(@RequestHeader("请求头") Integer name){
    System.out.println("name = " + name);
   return "param" ;
}
```

## @CookieValue

**范围：**方法参数上value = “想要获取值的Cookie名

**作用：**获取指定请求头的值

**例子：**

```java
@RequestMapping("/param")
public String paramUser(@CookieValue("JSESSIONID") String jsessionid){
    System.out.println("jsessionid = " + jsessionid);
   return "param" ;
}
```

## @ModelAttribute

**范围：**方法上、方法参数上

**作用：**以页面传来的值为准，没传来的由前一个方法返回的返回值填补，**@ModelAttribute标注过的方法会在@RequestMapping标注过的方法前运行**

**例子1：**方法上

```java
@Controller
@RequestMapping("/first")
public class HelloController {
   @RequestMapping("/modelAttribute")
    public String second(User userFirst){
        System.out.println("userFirst = " + userFirst);
        return "param" ;
    }

    @ModelAttribute
    public User first(){
        User userFirst = new User();
        List<Account> accounts = new ArrayList<Account>();
        Account account1 = new Account();
        account1.setAccountName("setAccountName1");
        account1.setAccountJob("setAccountJob1");
        account1.setAccountPara("setAccountPara1");
        Account account2 = new Account();
        account2.setAccountName("setAccountName2");
        account2.setAccountJob("setAccountJob2");
        account2.setAccountPara("setAccountPara2");
        accounts.add(account1);
        accounts.add(account2);
        userFirst.setAccounts(accounts);
        return userFirst ;
    }
}
```

**例子2：**方法参数上

```java
/**
 * 控制器实例类
 */
@Controller
@RequestMapping("/first")
public class HelloController {
    @RequestMapping("/modelAttribute")
    public String second(@ModelAttribute("user1")User userFirst) {
        System.out.println("userFirst = " + userFirst);
        return "param";
    }

    @ModelAttribute
    public void first(Map<String,User> userMap) {

        User userFirst = new User();
        List<Account> accounts = new ArrayList<Account>();
        Account account1 = new Account();
        account1.setAccountName("setAccountName1");
        account1.setAccountJob("setAccountJob1");
        account1.setAccountPara("setAccountPara1");
        accounts.add(account1);
        userFirst.setAccounts(accounts);
        userMap.put("user1",userFirst);
        return;
    }
}
```



## @SessionAttribute

**范围：**

**作用：**用于多次执行控制器方法间的参数共享

**例子：**

```java
/**
 * 控制器实例类
 */
@Controller
@RequestMapping("/first")

/**
 * 把数据存入到session域对象中
 */
@SessionAttributes(value = {"username", "password", "age"}, types = {String.class, Integer.class})
public class HelloController {

    /**
     * 向session中存入值
     *
     * @return
     */
    @RequestMapping(path = "/save")
    public String save(Model model) {
        System.out.println("向session域中保存数据");
        model.addAttribute("username", "root");
        model.addAttribute("password", "123");
        model.addAttribute("age", 20);
        return "success";
    }

    /**
     * 从session中获取值
     *
     * @return
     */
    @RequestMapping(path = "/find")
    public String find(ModelMap modelMap) {
        String username = (String) modelMap.get("username");
        String password = (String) modelMap.get("password");
        Integer age = (Integer) modelMap.get("age");
        System.out.println(username + " : " + password + " : " + age);
        return "success";
    }

    /**
     * 清除值
     *
     * @return
     */
    @RequestMapping(path = "/delete")
    public String delete(SessionStatus status) {
        status.setComplete();
        return "success";
    }
}
```

# 请求参数的绑定 

### 请求参数的绑定说明 

1. 绑定机制 
  1. 表单提交的数据都是k=v格式的 username=haha&password=123 
  2. SpringMVC的参数绑定过程是把表单提交的请求参数，作为控制器中方法的参数进行绑定的 
  3. 要求：提交表单的name和参数的名称是相同的 
2. 支持的数据类型 
  1. 基本数据类型和字符串类型 
  2. 实体类型（JavaBean） 
  3. 集合数据类型（List、map集合等） 

### 基本数据类型和字符串类型 

1. 提交表单的name和参数的名称是相同的 
2. 区分大小写 

3. ### 实体类型（JavaBean） 

  1. 提交表单的name和JavaBean中的属性名称需要一致 
  2. 如果一个JavaBean类中包含其他的引用类型，那么表单的name属性需要编写成：对象.属性 例如： address.name 
4. ### 给集合属性数据封装

  1. JSP页面编写方式：list[0].属性 
5. ### 请求参数中文乱码的解决 

  1. 在web.xml中配置Spring提供的过滤器类

     ```xml
     <!-- 配置过滤器，解决中文乱码的问题 -->
     <filter>
         <filter-name>characterEncodingFilter</filter-name>
         <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filterclass>        <!-- 指定字符集 -->
         <init-param>
             <param-name>encoding</param-name>
             <param-value>UTF-8</param-value>
         </init-param>
     </filter>
     <filter-mapping>
         <filter-name>characterEncodingFilter</filter-name>
         <url-pattern>/*</url-pattern>
     </filter-mapping>	
     ```

6. ### 自定义类型转换器 

  1. 表单提交的任何数据类型全部都是字符串类型，但是后台定义Integer类型，数据也可以封装上，说明 Spring框架内部会默认进行数据类型转换。 
  2. 如果想自定义数据类型转换，可以实现Converter的接口 
     1. 自定义类型转换器

        ```java
        package cn.itcast.utils;
        
        import java.text.DateFormat;
        import java.text.SimpleDateFormat;
        import java.util.Date;
        
        import org.springframework.core.convert.converter.Converter;
        
        /**
         * 把字符串转换成日期的转换器 * @author rt
         */
        public class StringToDateConverter implements Converter<String, Date> {
        
            /**
             * 进行类型转换的方法
             */
            public Date convert(String source) {        
                // 判断        
                if(source == null) {            
                    throw new RuntimeException("参数不能为空");
            	}
           	}
        }
        ```

        2. 注册自定义类型转换器，在springmvc.xml配置文件中编写配置	

        ```xml
            <!-- 注册自定义类型转换器 -->
            <bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
                <property name="converters">
                    <set>
                        <bean class="cn.itcast.utils.StringToDateConverter"/>
                    </set>
                </property>
            </bean>        <!-- 开启Spring对MVC注解的支持 -->
            <mvc:annotation-driven conversion-service="conversionService"/>
        ```

7. ### 在控制器中使用原生的ServletAPI对象

  1. 只需要在控制器的方法参数定义HttpServletRequest和HttpServletResponse对象