# SpringBoot的特点

Spring Boot 主要目标是：

- 为所有 Spring 的开发者提供一个非常快速的、广泛接受的入门体验
- 开箱即用（启动器starter-其实就是SpringBoot提供的一个jar包），但通过自己设置参数（.properties），即可快速摆脱这种方式。
- 提供了一些大型项目中常见的非功能性特性，如内嵌服务器、安全、指标，健康检测、外部化配置等
- 绝对没有代码生成，也无需 XML 配置。

# pom文件必要配置

```xml
<!--所有SpringBoot项目的父工程都必须是这个 -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.0.0.RELEASE</version>
</parent>

	<!--默认maven工程的jdk版本是1.5，所以指定1.8 -->
    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <!--web启动器 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
```
# 编写main方法

**注意：**main方法的位置是所有java文件的最上级

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

## 写一个简单的Controller

```java
@RestController//此controller返回的全是json格式的数据
@RequestMapping("/访问路径")
@EnableAutoConfiguration//开启自动配置
public class HelloController {
	@GetMapping("hello")
	public String hello(){
	    return "hello, spring boot!";
	}
}
```
# 尝试java配置

java配置主要靠java类和一些注解，比较常用的注解有：

- `@Configuration`：声明一个类作为配置类，代替xml文件
- `@Bean`：声明在方法上，将方法的返回值加入Bean容器，代替`<bean>`标签
- `@PropertySource`：指定属性文件的路径是:`classpath:jdbc.properties`
- 通过`@Value`为属性注入值
- 通过@Bean将 `dataSource()`方法声明为一个注册Bean的方法，Spring会自动调用该方法，将方法的返回值加入Spring容器中。

然后我们就可以在任意位置通过`@Autowired`注入DataSource了！

我们接下来用java配置来尝试实现连接池配置：

首先引入Druid连接池依赖：

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.6</version>
</dependency>
```

创建一个jdbc.properties文件，编写jdbc属性：

```properties
jdbc.driverClassName=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://127.0.0.1:3306/leyou
jdbc.username=root
jdbc.password=123
```

然后编写代码：

```java
@Configuration
@PropertySource("classpath:jdbc.properties")
public class JdbcConfig {

    @Value("${jdbc.url}")
    String url;
    @Value("${jdbc.driverClassName}")
    String driverClassName;
    @Value("${jdbc.username}")
    String username;
    @Value("${jdbc.password}")
    String password;

    @Bean
    public DataSource dataSource() {
        DruidDataSource dataSource = new DruidDataSource();
        dataSource.setUrl(url);
        dataSource.setDriverClassName(driverClassName);
        dataSource.setUsername(username);
        dataSource.setPassword(password);
        return dataSource;
    }
}
```

# SpringBoot的属性注入

## 演示（第三种）

在SpringBoot中，提供了一种新的属性注入方式，支持各种java基本数据类型及复杂类型的注入。

1）我们新建一个类，用来进行属性注入：

```java
@ConfigurationProperties(prefix = "jdbc")
public class JdbcProperties {
    private String url;
    private String driverClassName;
    private String username;
    private String password;
    // ... 略
    // getters 和 setters
}

```

- 在类上通过@ConfigurationProperties注解声明当前类为属性读取类

- `prefix="jdbc"`读取属性文件中，前缀为jdbc的值。

- 在类上定义各个属性，名称必须与属性文件中`jdbc.`后面部分一致

- 需要注意的是，这里我们并没有指定属性文件的地址，所以我们需要把jdbc.properties名称改为application.properties，这是SpringBoot默认读取的属性文件名：

  ![1525496325572](D:/IDEA%20JAVA/%E4%B9%90%E4%BC%98%E5%95%86%E5%9F%8E%E3%80%8A%E9%A1%B9%E7%9B%AE%E7%AC%94%E8%AE%B0%E3%80%8B/day01%E7%AC%94%E8%AE%B0/assets/1525496325572.png)

2）在JdbcConfig中使用这个属性：

```java
@Configuration
@EnableConfigurationProperties(JdbcProperties.class)
public class JdbcConfig {

    @Bean
    public DataSource dataSource(JdbcProperties jdbc) {
        DruidDataSource dataSource = new DruidDataSource();
        dataSource.setUrl(jdbc.getUrl());
        dataSource.setDriverClassName(jdbc.getDriverClassName());
        dataSource.setUsername(jdbc.getUsername());
        dataSource.setPassword(jdbc.getPassword());
        return dataSource;
    }
}
```

- 通过`@EnableConfigurationProperties(JdbcProperties.class)`来声明要使用`JdbcProperties`这个类的对象
- 然后你可以通过以下方式注入JdbcProperties：

## 1.@Autowired注入

```java
@Autowired
private JdbcProperties prop;
```

## 2.构造函数注入

```java
private JdbcProperties prop;
public JdbcConfig(Jdbcproperties prop){
    this.prop = prop;
}
```

## 3.声明有@Bean的方法参数注入

- 不严格要求属性文件中的属性名与成员变量名一致。支持驼峰，中划线，下划线等等转换，甚至支持对象引导。比如：user.friend.name：代表的是user对象中的friend属性中的name属性，显然friend也是对象。@value注解就难以完成这样的注入方式。
- meta-data support：元数据支持，帮助IDE生成属性提示（写开源框架会用到）。

```java
@Bean
public Datasource dataSource(JdbcProperties prop){
    Datasource datasource = new Datasource();
        dataSource.setUrl(prop.getUrl());
        dataSource.setDriverClassName(prop.getDriverClassName());
        dataSource.setUsername(prop.getUsername());
        dataSource.setPassword(prop.getPassword());
}
```

## 1.更优雅的注入

事实上，如果一段属性只有一个Bean需要使用，我们无需将其注入到一个类（JdbcProperties）中。而是直接在需要的地方声明即可：

```java
@Configuration
public class JdbcConfig {
    
    @Bean
    // 声明要注入的属性前缀，SpringBoot会自动把相关属性通过set方法注入到DataSource中
    @ConfigurationProperties(prefix = "jdbc")
    public DataSource dataSource() {
        DruidDataSource dataSource = new DruidDataSource();
        return dataSource;
    }
}
```

我们直接把`@ConfigurationProperties(prefix = "jdbc")`声明在需要使用的`@Bean`的方法上，然后SpringBoot就会自动调用这个Bean（此处是DataSource）的set方法，然后完成注入。使用的前提是：**该类必须有对应属性的set方法！**

# 重要注解

1.@SpringBootApplication【启动】
2.@Configuration【声明Java配置类】
3.@Bean【注入返回值对象】
4.@ConfigurationProperties【读取属性文件】
5.@EnableConfigurationProperties【启用属性文件】

# 默认配置原理

spring-boot-autoconfigure包其中定义了大量自动配置类并且提供了默认配置。而且而默认配置生效的条件一般有两个：

- 1.引入了相关依赖

  https://docs.spring.io/spring-boot/docs/2.0.6.RELEASE/reference/htmlsingle/#using-boot-starter

- 2.没有配置

## 配置读取优先级排列

1.命令行参数
2.java:comp/env 里的 JNDI 属性
3.JVM 系统属性
4.操作系统环境变量
5.RandomValuePropertySource 属性类生成的 random.* 属性
6.应用以外的 application.properties（或 yml）文件
7.打包在应用内的 application.properties（或 yml）文件
8.在应用 @Configuration 配置类中，用 @PropertySource 注解声明的属性文件
9.SpringApplication.setDefaultProperties 声明的默认属性
可见，命令行参数优先级最高。这个可以根据这个优先级，可以在测试或生产环境中快速地修改配置参数值，而不需要重新打包和部署应用。
还有第 6 点，根据这个在多 moudle 的项目中，比如常见的项目分 api 、service、dao 等 moudles，往往会加一个 deploy moudle 去打包该业务各个子 moudle，应用以外的配置优先。

# 静态资源存放的路径

1.resource/META-INF/resources下
2.resource/resources下
3.resource/static下
4.resource/public下

# LOG



你会发现日志中什么都没有，因为我们记录的log级别是debug，默认是显示info以上，我们需要进行配置。

SpringBoot通过`logging.level.*=debug`来配置日志级别，*填写包名

```properties
# 设置com.leyou包的日志级别为debug
logging.level.com.leyou=debug
# 设置 所有 包的日志级别为debug
logging.level.org.springframework=debug
```

# 整合mybatis

```txt
步骤：引入启动器，覆盖默认配置mybatis.mapper-locations=classpath:mybatis/mapper/**/*.xml
```



```xml
<!--mybatis -->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>1.3.2</version>
</dependency>
```

配置，基本没有需要配置的：

```properties
# mybatis 别名扫描
mybatis.type-aliases-package=com.heima.pojo
# mapper.xml文件位置,如果没有映射文件，请注释掉
mybatis.mapper-locations=classpath:mappers/*.xml
```

需要注意，这里没有配置mapper接口扫描包，因此我们需要给每一个Mapper接口添加`@Mapper`注解，才能被识别。

```java
@Mapper
public interface UserMapper {
}
```

## 通用mapper

通用Mapper的作者也为自己的插件编写了启动器，我们直接引入即可：

```xml
<!-- 通用mapper -->
<dependency>
    <groupId>tk.mybatis</groupId>
    <artifactId>mapper-spring-boot-starter</artifactId>
    <version>2.0.2</version>
</dependency>
```

不需要做任何配置就可以使用了。

```java
@Mapper
public interface UserMapper extends tk.mybatis.mapper.common.Mapper<User>{
}
```

## 