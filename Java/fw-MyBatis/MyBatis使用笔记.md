## 1.MyBatis简述

> mybatis是一个优秀的基于 java 的持久层框架，它内部封装了 jdbc，使开发者只需要关注 sql语句本身， 而不需要花费精力去处理加载驱动、创建连接、创建 statement 等繁杂的过程。**
>
> mybatis通过xml 或注解的方式将要执行的各种statement配置起来，并通过java对象和statement 中 sql 的动态参数进行映射生成最终执行的 sql 语句，最后由 mybatis 框架执行 sql 并将结果映射为 java 对象并 返回。
>
> **采用 ORM 思想解决了实体和数据库映射的问题，对 jdbc进行了封装**，屏蔽了 jdbc api 底层访问细节，使我 们不用与 jdbc api 打交道，就可以完成对数据库的持久化操作。

## 2.MyBatis入门之配置文件实现

### 2.1.pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.yuan</groupId>
    <artifactId>note_mybaties_01</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <dependencies>
        <!--        mybatis-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.5</version>
        </dependency>
        <!--        单元测试-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.10</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <!--            数据库-->
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.6</version>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <!--            日志-->
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.12</version>
        </dependency>
    </dependencies>

</project>
```

### 2.2.编写实体类

**路径com/yuan/doman/User.java**

```java
package com.yuan.doman;

import java.io.Serializable;
import java.util.Date;

public class User implements Serializable{
    private Integer id;
    /**
     * 用户姓名
     */
    private String  username;
    /**
     * 用户生日
     */
    private Date    birthday;
    /**
     * 用户性别
     */
    private String  sex;
    /**
     * 用户地址
     */
    private String  address;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public Date getBirthday() {
        return birthday;
    }

    public void setBirthday(Date birthday) {
        this.birthday = birthday;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    @Override
    public String toString() {
        return  "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", birthday=" + birthday +
                ", sex='" + sex + '\'' +
                ", address='" + address + '\'' +
                '}';
    }
}
```

### 2.3.编写持久层接口

**路径：com/yuan/dao/UserDao.java**

```java
package com.yuan.dao;

import com.yuan.doman.User;

import java.util.List;

/**
 * ，用户的持久层接口
 */
public interface UserDao {

    List<User> findAll();


}
```

### 2.4.编写映射文件

**路径：resources/yuan/dao/UserDao.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.yuan.dao.UserDao">
    <!--findAll方法-->
    <select id="findAll" resultType="com.yuan.doman.User">
        SELECT * FROM USER
    </select>
</mapper>
```

### 2.5.编写配置文件

**路径：resources/SqlMapConfig .xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--环境配置-->
        <!--默认环境-->
    <environments default="mysql">
        <!--mysql环境-->
        <environment id="mysql">
            <!--JDBC事务-->
            <transactionManager type="JDBC"></transactionManager>
            <!--POOLED连接池-->
            <dataSource type="POOLED">
            <!--四大参数-->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql:///数据库名"/>
                <property name="username" value="数据库账户"/>
                <property name="password" value="数据库密码"/>
            </dataSource>
        </environment>
    </environments>

<mappers>
    <mapper resource="com/yuan/dao/UserDao.xml"/>
</mappers>
</configuration>
```

### 2.6.注意事项

**第一个**：创建UserDao.xml 和 UserDao.java时名称是为了和我们之前的知识保持一致。
	在Mybatis中它把持久层的操作接口名称和映射文件也叫做：Mapper
	所以：UserDao 和 UserMapper是一样的
	**第二个：**在idea中创建目录的时候，它和包是不一样的
	包在创建时：com.yuan.dao它是三级结构
	目录在创建时：com.yuan.dao是一级目录
	**第三个：**mybatis的映射配置文件位置必须和dao接口的包结构相同
	**第四个：**映射配置文件的mapper标签namespace属性的取值必须是dao接口的全限定类名
	**第五个：**映射配置文件的操作配置（select），id属性的取值必须是dao接口的方法名
	**第六个：**当我们遵从了第三，四，五点之后，我们在开发中就无须再写dao的实现类。

### 2.7.测试

```java
package com.yuan.test;

import com.yuan.dao.UserDao;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;

public class UserDaoTest {
    InputStream is = null;
    SqlSessionFactory factory = null;
    SqlSession session = null;

    @Before
    public void init() throws IOException {
        //读取配置文件
        is = Resources.getResourceAsStream("SqlMapConfig.xml");
        //创建会话工厂
        factory = new SqlSessionFactoryBuilder().build(is);
        //生产SQLSession对象
        session = factory.openSession();
    }
    
    @After
    public void close() throws IOException {
        session.commit();
        session.close();
        is.close();
    }

    @Test
    public void findAllTest(){
        UserDao userDao = session.getMapper(UserDao.class);
        System.out.println(userDao.findAll());
    }

}
```

## 3.MyBatis入门之注解实现

#### 3.1.pom.xml

> 与2.1相同

#### 3.2.编写实体类

> 与2.2相同
>

#### 3.3.编写持久层接口

```java
package com.yuan.dao;

import com.yuan.doman.User;
import org.apache.ibatis.annotations.Select;

import java.util.List;

/**
 * 用户的持久层接口
 */
public interface UserDao {

    @Select("SELECT * FROM USER")
    List<User> findAll();


}
```

#### 3.4.编写配置文件

> 与2.5大致相同【唯一不同】
>
> ```xml
> <mappers>
>     <mapper class="com.yuan.dao.UserDao"/>
> </mappers>
> ```

#### 3.5.测试

> 与2.7相同

## 4.小结1

**开发时要不要写持久层实现类？**

不写，怎么简单怎么来，怎么快怎么来，怎么好维护怎么来

**开发时用注解还是用xml文件？**

老项目一般用xml，最近项目都是注解开发，同一个Dao接口不能既用注解又用xml

**项目中如何获取路径**

一般使用两种方法

1.使用类加载器，它只能读取类路径的配置文件

2.使用servletContext的getRealPath()方法

![案例分析](D:\IDEA JAVA\黑马\阶段3 1.Mybatis·\mybatis\mybatis_day01\截图\入门案例的分析.png)

## 5.基于配置文件的增删改查

### 5.1.持久层接口

```java
package com.yuan.dao;

import com.yuan.doman.User;

import java.util.List;

/**
 * 用户的持久层接口
 */
public interface UserDao {

    /**
     * 全查User
     *
     * @return
     */
    List<User> findAll();

    /**
     * 查询UserId
     *
     * @param uid
     */
    User findById(Integer uid);

    /**
     * 查询UserName
     *
     * @param username
     */
    List<User> findLikeName(String username);

    /**
     * 查询User总数
     *
     * @return int
     */
    Integer findTotalId();

    /**
     * 新增User
     *
     * @param user
     */
    void save(User user);

    /**
     * 修改User
     *
     * @param user
     */
    void update(User user);

    /**
     * 删除User
     *
     * @param uid
     */
    void delete(Integer uid);


}
```

### 5.2.映射文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.yuan.dao.UserDao">

    <!--查询全部User-->
    <select id="findAll" resultType="com.yuan.doman.User">
        SELECT * FROM USER
    </select>

    <!--查询单个User-->
    <select id="findById" resultType="com.yuan.doman.User" parameterType="int">

        SELECT * FROM USER WHERE ID = #{id}
    </select>

    <!--Like查询UserName-->
    <select id="findLikeName" resultType="com.yuan.doman.User" parameterType="string">
        SELECT * FROM USER WHERE USERNAME LIKE #{uname};
    </select>

    <!--查询User总数-->
    <select id="findTotalId" resultType="int">
        SELECT count(*) FROM USER
    </select>

    <!--新增User-->
    <insert id="save" parameterType="com.yuan.doman.User">
        <!--拿到返回的ID-->
        <selectKey keyProperty="id" keyColumn="id" resultType="int" order="AFTER">
            SELECT Last_INSERT_ID();
        </selectKey>
                INSERT INTO USER(username,birthday,sex, address) VALUE (#{username},#{birthday},#{sex},#{address});
    </insert>

<!--    更新User根据ID-->
    <update id="update" parameterType="com.yuan.doman.User">
        UPDATE USER SET USERNAME = #{username} WHERE ID = #{id}
    </update>

    <!--删除User根据ID-->
    <update id="delete" parameterType="Integer">
        delete FROM user WHERE ID = #{uid}
    </update>
    
</mapper>
```

### 5.3.测试文件

```java
package com.yuan.test;

import com.yuan.dao.UserDao;
import com.yuan.doman.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;
import java.util.Date;

public class UserDaoTest {
    InputStream is = null;
    SqlSessionFactory factory = null;
    SqlSession session = null;

    /**
     * 在Test
     * @throws IOException
     */
    @Before
    public void init() throws IOException {
        //读取配置文件
        is = Resources.getResourceAsStream("SqlMapConfig.xml");
        //创建会话工厂
        factory = new SqlSessionFactoryBuilder().build(is);
        //生产SQLSession对象
        session = factory.openSession();
    }
    
    @After
    public void close() throws IOException {
        session.commit();
        session.close();
        is.close();
    }

    @Test
    public void findAllTest(){
        UserDao userDao = session.getMapper(UserDao.class);
        System.out.println(userDao.findAll());
    }

    @Test
    public void testSave() {
        UserDao userDao = session.getMapper(UserDao.class);
        User user = new User();
        user.setUsername("GiaoWin");
        user.setBirthday(new Date());
        user.setSex("男");
        user.setAddress("张家口");

        System.out.println("保存前 = " + user);
        userDao.save(user);
        System.out.println("保存后 = " + user);
    }


    @Test
    public void testUpdate() {
        UserDao userDao = session.getMapper(UserDao.class);
        User user = new User();
        user.setUsername("AKUMA");
        user.setId(49);
        userDao.update(user);
    }
    @Test
    public void testDelete() {
        UserDao userDao = session.getMapper(UserDao.class);
        userDao.delete(49);
    }

    @Test
    public void testfindById() {
        UserDao userDao = session.getMapper(UserDao.class);
        System.out.println(userDao.findById(46));
    }

    @Test
    public void testfindAll() {
        UserDao userDao = session.getMapper(UserDao.class);
        System.out.println(userDao.findAll());
    }

    @Test
    public void testfindLikeName() {
        UserDao userDao = session.getMapper(UserDao.class);
        userDao.findLikeName("%王%");
    }

    @Test
    public void testfindTotalUser() {
        UserDao userDao = session.getMapper(UserDao.class);
        System.out.println("+++++++++++++++++++++++"+userDao.findTotalId()+"++++++++++++++++++++++++");
    }

}
```





## 6.小结2

**#{}h和${}有什么区别**

前者是占位符?，后者是字符串拼接，会受到SQL注入攻击

**如何拿到保存时的ID值**：

```xml
    <!--拿到返回的ID-->
    <selectKey keyProperty="id" keyColumn="id" resultType="int" order="AFTER">
        SELECT Last_INSERT_ID();
    </selectKey>
```
**多个查询条件组成一个对象作为参数怎么解决？**

用一个包装类给他们包装一下，然后通过NGOL表达式逐一拿出。

**数据库与实体类中的列名不一致怎么解决？**

1.改正

2.用别名【提升速度】

3.用resultMap【提升开发速度】

```xml
<resultMap id="userMap" type="com.yuan.doman.User">
    <!--    主键字段的对应    -->
    <id property="Java中的属性" column="数据库中的列"/>
    <!--    非主键字段的对应    -->
    <result property="Java中的属性" column="数据库中的列"/>
</resultMap>
<!--  使用  -->
<select id="findAll" resultMap="userMap">
    SELECT * FROM USER
</select>
```

**SqlMapConfig.xml配置文件常用标签**

```xml
<!—外部配置文件-->
<propertys>
	<property source="文件路径"/>
</propertys>

<!--别名映射-->
<typeAliases>
	<!--引入映射包就不用其他的了-->
	<package name="com.yuan.doman " />
    <typeAlias type="com.yuan.doman.User" alias="user"/>
</typeAliases>
```

**如何查看源码**

用Ctrl+Alt+B跟踪到底层实现【IDEA工具】

**代理Dao的实现源码追踪**

```java
/**

- Copyright 2009-2017 the original author or authors.
  *
- Licensed under the Apache License, Version 2.0 (the "License");
- you may not use this file except in compliance with the License.
- You may obtain a copy of the License at
  *
- http://www.apache.org/licenses/LICENSE-2.0
  *
- Unless required by applicable law or agreed to in writing, software
- distributed under the License is distributed on an "AS IS" BASIS,
- WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
- See the License for the specific language governing permissions and
- limitations under the License.
  */
  package org.apache.ibatis.binding;

import org.apache.ibatis.annotations.Flush;
import org.apache.ibatis.annotations.MapKey;
import org.apache.ibatis.cursor.Cursor;
import org.apache.ibatis.mapping.MappedStatement;
import org.apache.ibatis.mapping.SqlCommandType;
import org.apache.ibatis.reflection.MetaObject;
import org.apache.ibatis.reflection.ParamNameResolver;
import org.apache.ibatis.reflection.TypeParameterResolver;
import org.apache.ibatis.session.Configuration;
import org.apache.ibatis.session.ResultHandler;
import org.apache.ibatis.session.RowBounds;
import org.apache.ibatis.session.SqlSession;

import java.lang.reflect.Array;
import java.lang.reflect.Method;
import java.lang.reflect.ParameterizedType;
import java.lang.reflect.Type;
import java.util.*;

/**

- @author Clinton Begin
- @author Eduardo Macarron
- @author Lasse Voss
  */
  public class MapperMethod {

  private final SqlCommand command;
  private final MethodSignature method;

  public MapperMethod(Class<?> mapperInterface, Method method, Configuration config) {
    this.command = new SqlCommand(config, mapperInterface, method);
    this.method = new MethodSignature(config, mapperInterface, method);
  }

  public Object execute(SqlSession sqlSession, Object[] args) {
    Object result;
    switch (command.getType()) {
      case INSERT: {
    	Object param = method.convertArgsToSqlCommandParam(args);
        result = rowCountResult(sqlSession.insert(command.getName(), param));
        break;
      }
      case UPDATE: {
        Object param = method.convertArgsToSqlCommandParam(args);
        result = rowCountResult(sqlSession.update(command.getName(), param));
        break;
      }
      case DELETE: {
        Object param = method.convertArgsToSqlCommandParam(args);
        result = rowCountResult(sqlSession.delete(command.getName(), param));
        break;
      }
      case SELECT:
        if (method.returnsVoid() && method.hasResultHandler()) {
          executeWithResultHandler(sqlSession, args);
          result = null;
        } else if (method.returnsMany()) {
          result = executeForMany(sqlSession, args);
        } else if (method.returnsMap()) {
          result = executeForMap(sqlSession, args);
        } else if (method.returnsCursor()) {
          result = executeForCursor(sqlSession, args);
        } else {
          Object param = method.convertArgsToSqlCommandParam(args);
          result = sqlSession.selectOne(command.getName(), param);
        }
        break;
      case FLUSH:
        result = sqlSession.flushStatements();
        break;
      default:
        throw new BindingException("Unknown execution method for: " + command.getName());
    }
    if (result == null && method.getReturnType().isPrimitive() && !method.returnsVoid()) {
      throw new BindingException("Mapper method '" + command.getName() 
          + " attempted to return null from a method with a primitive return type (" + method.getReturnType() + ").");
    }
    return result;
  }
}
```

## 7.MyBatis连接池

使用了同步代码快保障了每一个线程拿到的是不同的连接，是一个线程安全的集合。

主配置文件**SqlMapConfig.xml**中的**dataSource**标签，**type**属性就是表示采用何种连接池方式。

​       1.POOLED 

​       **从池中获取连接**，采用传统的javax.sql.DataSource规范中的连接池，mybatis中有针对规范的实现

​       2.UNPOOLE

​       **每次new一个连接**，采用传统的获取连接的方式，虽然也实现Javax.sql.DataSource接口，但是并没有使用池的思想。

​       3.JNDI 

> 采用服务器提供的JNDI技术实现，来获取DataSource对象，不同的服务器所能拿到DataSource是不一样。
>
> 注意：如果不是web或者maven的war工程，是不能使用的。
>
> tomcat服务器,采用连接池就是dbcp连接池。

## 8.MyBatis事务

```java
//生产SQLSession对象 session = factory.openSession(true);
它是通过sqlsession对象的commit方法和rollback方法实现事务的提交和回滚

session.commit();
session.close();
```

## 9.动态SQL

> <where>
>
> <if>
>
> <foreach>

```xml
这是一个根据条件查询的语句，但是条件有可能哦呦有可能没有就要使用动态SQL标签了	[ids 是Vo类里的属性名]
<include><sql id="defUser"> SELECT * FROM USER </sql>
        <select id="findUserIn" resultType="User" parameterType="QueryVo">
    <include refid="defUser"/>
    <where>
        <if test="#{ids!= null and ids.size > 0 }">
            id in
            <foreach collection="ids " open="(" close=")" item="id" separator=",">
                #{id}
            </foreach>
        </if>
    </where>
</select>
```

## 10.MyBatis表与表之间的关系

### 10.1.多对一【包括一对一】（Account==>User）

#### 10.1.1.实体类

```java
package com.yuan.doman;

import java.util.Date;
import java.util.List;

public class User {
    private Integer id;
    /**
     * 用户姓名
     */
    private String  username;
    /**
     * 用户生日
     */
    private Date    birthday;

    /**
     * 用户性别
     */
    private String  sex;
    /**
     * 用户地址
     */
    private String  address;

    /**
     *角色下的账户集合
     * @return
     */
    private List<Account> roles;
//Get() Set() 方法
}
```

#### 10.1.2.映射文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.yuan.dao.AccountDao">
    <!--配置一对多关系的resultMap-->
    <resultMap id="accountUserMap" type="account">
            <!--id是唯一标识-->
            <!--type是对应的实体类-->
        <id column="aid" property="id"/>
        <!--id是主键-->
        <!--result是普通列-->
            <!--column是数据库列名-->
            <!--property是属性名-->
        <result column="uid" property="uid"/>
        <result column="money" property="money"/>
        <!--association对应主表关系-->
            <!--property表示外键属性是User-->
            <!--column表示用uid字段来获取-->
            <!--javaType表示返回的Java类型是什么-->
        <association property="user" column="uid" javaType="com.yuan.doman.User">
            <id column="id" property="id"/>
            <result column="username" property="username"/>
            <result column="birthday" property="birthday"/>
            <result column="sex" property="sex"/>
            <result column="address" property="address"/>
        </association>
    </resultMap>

    <!--查询全部Account-->
    <select id="findAll" resultMap="accountUserMap">
        SELECT a.id as AID,a.UID,a.MONEY,u.* from account a,user u where u.id = a.UID
    </select>
    
</mapper>
```

#### 10.1.3.测试

```java
package com.yuan.test;

import com.yuan.dao.AccountDao;
import com.yuan.doman.Account;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

public class AccountDaoTest {
    InputStream is = null;
    SqlSessionFactory factory = null;
    SqlSession session = null;
    AccountDao accountDao = session.getMapper(AccountDao.class);

    @Before
    public void init() throws IOException {
        //读取配置文件
        is = Resources.getResourceAsStream("SqlMapConfig.xml");
        //创建会话工厂
        factory = new SqlSessionFactoryBuilder().build(is);
        //生产SQLSession对象
        session = factory.openSession();
    }

    @After
    public void close() throws IOException {
        session.commit();
        session.close();
        is.close();
    }

    @Test
    public void finAllAccount() {
        List<Account> accountAll = accountDao.findAll();
        for (Account account : accountAll) {
            System.out.println("---------------");
            System.out.println("account = " + account);
            System.out.println(account.getUser());
        }
    }

}
```

### 10.2.一对多（User==>Account）

#### 10.2.1.实体类

```java
package com.yuan.doman;

import java.util.Date;
import java.util.List;

public class User {
    private Integer id;
    /**
     * 用户姓名
     */
    private String  username;
    /**
     * 用户生日
     */
    private Date    birthday;

    /**
     * 用户性别
     */
    private String  sex;
    /**
     * 用户地址
     */
    private String  address;

    /**
     *角色下的账户集合
     * @return
     */
    private List<Account> roles;
//Get() Set() 方法
}
```

#### 10.2.2.映射文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.yuan.dao.UserDao">

    <sql id="defUser">
SELECT u.*,a.ID as aid,A.MONEY
FROM USER u LEFT JOIN ACCOUNT a
ON a.UID = u.id
    </sql>

    <resultMap id="userAccountMap" type="user">
        <id property="id" column="id"></id>
        <result property="username" column="username"/>
        <result property="birthday" column="birthday"/>
        <result property="sex" column="sex"/>
        <result property="address" column="address"/>
        <!--账户定义集合-->
        <!--property 集合属性名-->
        <!--ofType 集合的泛型-->
        <collection property="accounts" ofType="com.yuan.doman.Account">
            <id column="aid" property="id"/>
            <result column="uid" property="uid"/>
            <result column="money" property="money"/>
        </collection>
    </resultMap>


    <!--查询全部User-->
    <select id="findAll" resultMap="userAccountMap">
        <include refid="defUser"/>
    </select>

    <!--根据ID查询User-->
    <select id="findById" resultMap="userAccountMap" parameterType="int">
        <include refid="defUser"/>
        <where>
            ID = #{id}
        </where>
    </select>


</mapper>
```

#### 10.2.3.测试

```java
package com.yuan.test;

import com.yuan.dao.UserDao;
import com.yuan.doman.Account;
import com.yuan.doman.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

public class UserDaoTest {
    InputStream is = null;
    SqlSessionFactory factory = null;
    SqlSession session = null;


    @Before
    public void init() throws IOException {
        //读取配置文件
        is = Resources.getResourceAsStream("SqlMapConfig.xml");
        //创建会话工厂
        factory = new SqlSessionFactoryBuilder().build(is);
        //生产SQLSession对象
        session = factory.openSession();
    }

    @After
    public void close() throws IOException {
        session.commit();
        session.close();
        is.close();
    }


    @Test
    public void finAllAccount() {
        UserDao userDao = session.getMapper(UserDao.class);
        List<User> userAccounts = userDao.findAll();
        for (User userAccount : userAccounts) {
            System.out.println("------------------------");
            System.out.println("userAccount = " + userAccount);
            if (!(userAccount.getAccounts().size()<=0)) {
                for (Account account : userAccount.getAccounts()) {
                    System.out.println("account = " + account);
                }
            }
        }

    }
}
```

### 10.3.多对多(User<===>Role)

#### 10.3.1.实体类

```java
package com.yuan.doman;

import java.util.List;

public class Role {
    private Integer id;
    private String role_name;
    private String role_desc;
    /**
     * 角色对应的账户结合
     */
    private List<User> users;
    //Get() Set() 方法
}
```

```java
package com.yuan.doman;

import java.util.Date;
import java.util.List;

public class User{
    private Integer id;
    /**
     * 用户姓名
     */
    private String  username;
    /**
     * 用户生日
     */
    private Date    birthday;
    /**
     * 用户性别
     */
    private String  sex;
    /**
     * 用户地址
     */
    private String  address;

    /**
     *角色下的账户集合
     * @return
     */
    private List<Role> roles;
    //Get() Set() 方法
}
```

#### 10.3.2.映射文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.yuan.dao.RoleDao">
    
    <resultMap id="roleUserMap" type="role">
        <id property="id" column="rid"></id>
        <result property="role_name" column="role_name"/>
        <result property="role_desc" column="role_desc"/>
        <collection property="users" ofType="user">
            <id property="id" column="UID"></id>
            <result property="username" column="username"/>
            <result property="birthday" column="birthday"/>
            <result property="sex" column="sex"/>
            <result property="address" column="address"/>
        </collection>
    </resultMap>


    <!--查询全部User-->
    <select id="findAll" resultMap="roleUserMap">
 SELECT
 r.id as rid
,r.ROLE_NAME
,r.ROLE_DESC
,u.id AS UID
,u.username
,u.sex
,u.address
,u.birthday
 from role r
 LEFT JOIN user_role ur ON r.ID = ur.RID
 LEFT JOIN user       u ON u.id = ur.UID
    </select>

    <!--根据ID查询User-->
    <select id="findById" resultMap="roleUserMap" parameterType="int">
    </select>


</mapper>
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.yuan.dao.UserDao">


    <resultMap id="userRolestMap" type="user">
        <id property="id" column="uid"></id>
        <result property="username" column="username"/>
        <result property="birthday" column="birthday"/>
        <result property="sex" column="sex"/>
        <result property="address" column="address"/>

        <collection property="roles" ofType="role">
            <id property="id" column="rid"></id>
            <result property="role_name" column="role_name"/>
            <result property="role_desc" column="role_desc"/>
        </collection>
    </resultMap>


    <!--查询全部User-->
    <select id="findAll" resultMap="userRolestMap">
SELECT
   u.id AS UID
  ,u.username
  ,u.sex
  ,u.address
  ,u.birthday
  ,r.id as rid
  ,r.ROLE_NAME
  ,r.ROLE_DESC
from user       u
  LEFT JOIN user_role ur ON u.ID = ur.UID
  LEFT JOIN role r ON r.ID = ur.RID
    </select>

    <!--根据ID查询User-->
    <select id="findById" resultMap="userRolestMap" parameterType="int">
    </select>


</mapper>
```

#### 10.3.3.测试

```java
package com.yuan.test;

import com.yuan.dao.RoleDao;
import com.yuan.doman.Role;
import com.yuan.doman.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

public class RoleDaoTest {
    InputStream is = null;
    SqlSessionFactory factory = null;
    SqlSession session = null;
    RoleDao roleDao = null;
    @Before
    public void init() throws IOException {
        //读取配置文件
        is = Resources.getResourceAsStream("SqlMapConfig.xml");
        //创建会话工厂
        factory = new SqlSessionFactoryBuilder().build(is);
        //生产SQLSession对象
        session = factory.openSession();
        roleDao = session.getMapper(RoleDao.class);
    }
    @After
    public void close() throws IOException {
        session.commit();
        session.close();
        is.close();
    }
    @Test
    public void finAll() {

        List<Role> roles = roleDao.findAll();
        for (Role role : roles) {
        System.out.println("-----------------");
            System.out.println("role = " + role);
            if(!(role.getUsers().size()<=0)){
                for (User user : role.getUsers()) {
                    System.out.println("user = " + user);
                }
            }
        }
    }
}
```

```java
package com.yuan.test;

import com.yuan.dao.UserDao;
import com.yuan.doman.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

public class UserDaoTest {
    InputStream is = null;
    SqlSessionFactory factory = null;
    SqlSession session = null;
    UserDao userDao = null;

    @Before
    public void init() throws IOException {
        //读取配置文件
        is = Resources.getResourceAsStream("SqlMapConfig.xml");
        //创建会话工厂
        factory = new SqlSessionFactoryBuilder().build(is);
        //生产SQLSession对象
        session = factory.openSession();
        userDao = session.getMapper(UserDao.class);
    }
    @After
    public void close() throws IOException {
        session.commit();
        session.close();
        is.close();
    }
    @Test
    public void finAllAccount() {

        List<User> users = userDao.findAll();
        for (User user : users) {
            System.out.println("user = " + user);
        }
    }
}
```
## 11.MyBatis表与表之间的关系【注解实现】

### 11.1.pom.xml

参考3.1

### 11.2.编写实体类

#### 11.2.1.User.java

```java
package com.yuan.doman;

import java.io.Serializable;
import java.util.Date;
import java.util.List;

public class User implements Serializable {

    private Integer id;
    /**
     * 用户姓名
     */
    private String  username;
    /**
     * 用户生日
     */
    private Date    birthday;
    /**
     * 用户性别
     */
    private String  sex;
    /**
     * 用户地址
     */
    private String  address;
    /**
     * 用户下的账户信息
     */
    private List<Account> accounts;

    public List<Account> getAccounts() {
        return accounts;
    }

    public void setAccounts(List<Account> accounts) {
        this.accounts = accounts;
    }

    /**
     *用户下的角色集合
     * @return
     */
    private List<Role> roles;

    public List<Role> getRoles() {
        return roles;
    }

    public void setRoles(List<Role> roles) {
        this.roles = roles;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public Date getBirthday() {
        return birthday;
    }

    public void setBirthday(Date birthday) {
        this.birthday = birthday;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    @Override
    public String toString() {
        return  "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", birthday=" + birthday +
                ", sex='" + sex + '\'' +
                ", address='" + address + '\'' +
                '}';
    }
}
```

#### 11.2.2.Account.java

```java
package com.yuan.doman;


import java.io.Serializable;

public class Account implements Serializable {
    private Integer id;
    /**
     * 用户Id
     */
    private Integer uid;
    /**
     * 账户余额
     */
    private Double money;
    /**
     * 所属用户【这个数据库中表没有】
     */
    private User user;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public Integer getUid() {
        return uid;
    }

    public void setUid(Integer uid) {
        this.uid = uid;
    }

    public Double getMoney() {
        return money;
    }

    public void setMoney(Double money) {
        this.money = money;
    }

    public User getUser() {
        return user;
    }

    public void setUser(User user) {
        this.user = user;
    }

    @Override
    public String toString() {
        return "Account{" +
                "id=" + id +
                ", uid=" + uid +
                ", money=" + money +
                ", user=" + user +
                '}';
    }
}
```

#### 11.2.3.Role.java

```java
package com.yuan.doman;

import java.io.Serializable;
import java.util.List;

public class Role implements Serializable {
    private Integer id;
    /**
     * 角色名
     */
    private String role_name;
    /**
     * 角色权限
     */
    private String role_desc;
    /**
     * 角色对应的账户结合
     */
    private List<User> users;

    public List<User> getUsers() {
        return users;
    }

    public void setUsers(List<User> users) {
        this.users = users;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getRole_name() {
        return role_name;
    }

    public void setRole_name(String role_name) {
        this.role_name = role_name;
    }

    public String getRole_desc() {
        return role_desc;
    }

    public void setRole_desc(String role_desc) {
        this.role_desc = role_desc;
    }

    @Override
    public String toString() {
        return "Role{" +
                "id=" + id +
                ", role_name='" + role_name + '\'' +
                ", role_desc='" + role_desc + '\'' +
                '}';
    }
}
```

### 11.3.编写持久层接口

#### 11.3.1.UserDao.java

```java
package com.yuan.dao;

import com.yuan.doman.User;
import org.apache.ibatis.annotations.*;

import java.util.List;

/**
 * ，用户的持久层接口
 */
public interface UserDao {

    /**
     * 查询所有用户
     *
     * @return
     */
    @Select("select * from user")
    @Results(
            id = "userMap",
            value = {
                    @Result(id = true, column = "id", property = "id"),
                    @Result(column = "username", property = "username"),
                    @Result(column = "sex", property = "sex"),
                    @Result(column = "address", property = "address"),
                    @Result(column = "birthday", property = "birthday")
            }
    )
    List<User> findAll();
    /**
     * 根据 id 查询一个用户
     *
     * @param userId
     * @return
     */
    @Select("select * from user where id = #{uid} ")
    @ResultMap("userMap")
    User findById(Integer userId);

    /**
     * 保存操作
     *
     * @param user
     * @return
     */
    @Insert("insert into user(username,sex,birthday,address)values(#{username},#{sex},#{birthday},#{address} )")
    @SelectKey(
            keyColumn = "id",
            keyProperty = "id",
            resultType = Integer.class,
            before = false,
            statement = {"select last_insert_id()"}
    )
    int saveUser(User user);

    /**
     * 更新操作
     *
     * @param user
     * @return
     */
    @Update("update user set username=#{username},address=#{address},sex=#{sex},birthday=#{birthday} where id =#{id} ")
    int updateUser(User user);

    /**
     * 删除用户
     *
     * @param userId
     * @return
     */
    @Delete("delete from user where id = #{uid} ")
    int deleteUser(Integer userId);

    /**
     * 查询使用聚合函数
     *
     * @return
     */
    @Select("select count(*) from user ")
    int findTotal();

    /**
     * 模糊查询
     *
     * @param name
     * @return
     */
    @Select("select * from user where username like #{username} ")
    List<User> findByName(String name);
}
```

#### 11.3.2.AccountDao.java

```java
package com.yuan.dao;

import com.yuan.doman.Account;

import java.util.List;

public interface AccountDao {
    /**
     * 查询所有账户
     * @return
     */
    List<Account> findAll();


    /**
     * 查询所有账户
     * @param aid
     * @return
     */
    Account findByUid(Integer aid);
}
```

#### 11.3.3.RoleDao.java

```java
package com.yuan.dao;

import com.yuan.doman.Role;
import com.yuan.doman.User;

import java.util.List;

/**
 * ，用户的持久层接口
 */
public interface RoleDao {

    /**
     * 全查User
     *
     * @return
     */
    List<Role> findAll();

    /**
     * 查询UserId
     *
     * @param rid
     */
    Role findById(Integer rid);


}
```

### 11.4.编写配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--引入外部配置文件-->
    <properties resource="jdbc.properties">
    </properties>
    <settings>
        <!-- 延迟加载的全局开关 默认关闭 -->
        <setting name="lazyLoadingEnabled" value="true"/>

<!--        <setting name="aggressiveLazyLoading" value="true"/>-->
    </settings>
    <!--别名包-->
    <typeAliases>
        <package name="com.yuan.doman"/>
    </typeAliases>
    <!--配置环境-->
    <environments default="def">
        <!--配置默认环境-->
        <environment id="def">
            <!--配置JDBC事务-->
            <transactionManager type="JDBC"></transactionManager>
            <!--配置POOLED连接池-->
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}"></property>
                <property name="url" value="${jdbc.url}"></property>
                <property name="username" value="${jdbc.username}"></property>
                <property name="password" value="${jdbc.password}"></property>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <!--引入映射文件-->
        <package name="com.yuan.dao"/>
    </mappers>
</configuration>
```

### 11.5.测试

```java
package com.yuan.test;

import com.yuan.dao.UserDao;
import com.yuan.doman.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

public class UserDaoTest {
    InputStream is = null;
    SqlSessionFactory factory = null;
    SqlSession session = null;
    UserDao userDao = null;

    @Before
    public void init() throws IOException {
        //读取配置文件
        is = Resources.getResourceAsStream("SqlMapConfig.xml");
        //创建会话工厂
        factory = new SqlSessionFactoryBuilder().build(is);
        //生产SQLSession对象
        session = factory.openSession();
        userDao = session.getMapper(UserDao.class);
    }

    @After
    public void close() throws IOException {
        session.commit();
        session.close();
        is.close();
    }

    @Test
    public void finAllTest() {
        List<User> users = userDao.findAll();
        for (User user : users) {
            System.out.println("user = " + user);
        }
    }

    @Test
    public void findByIdTest() {
        User user = userDao.findById(61);
        System.out.println(user);
    }


    @Test
    public void saveUserTest() {
        final User user = new User();
        user.setUsername("GiaoWin");
        System.out.println(user.getUsername()+"------------");
        userDao.saveUser(user);
        System.out.println(user.getId()+"------------");
    }

    @Test
    public void updateUserTest() {
        final User user = new User();
        user.setId(61);
        user.setUsername("GiaoWinner");
        userDao.updateUser(user);
        System.out.println(user.getUsername()+"------------");
    }

    @Test
    public void deleteUserTest() {

        System.out.println(userDao.deleteUser(61));
    }


    @Test
    public void findTotalTest() {

        System.out.println(userDao.findTotal());
    }

   @Test
    public void findByNameTest() {

       List<User> users = userDao.findByName("Giao%");
       for (User user : users) {
           System.out.println("user = " + user);
       }
   }
}
```

### 11.6多对一

这里只有接口文件有和测试文件变化

#### 11.6.1.接口

UserDao.java

```java
package com.yuan.dao;

import com.yuan.doman.User;
import org.apache.ibatis.annotations.*;

@CacheNamespace
public interface UserDao  {
    /**
     * 根据用户Id返回该用户
     * @param uid
     * @return
     */
    @Select("select * from user where id = #{id}")
    User findById(Integer uid);

}
```

AccountDao.java

```java
package com.yuan.dao;

import com.yuan.doman.Account;
import org.apache.ibatis.annotations.One;
import org.apache.ibatis.annotations.Result;
import org.apache.ibatis.annotations.Results;
import org.apache.ibatis.annotations.Select;
import org.apache.ibatis.mapping.FetchType;

import java.util.List;

public interface AccountDao {

    /**
     * 查询账户以及所属用户
     * @return
     */
    @Select("select * from account")
    @Results(value = {
            @Result(id = true,column = "id",property = "id"),
            @Result(column = "uid",property = "user",
                    one = @One(select = "com.yuan.dao.UserDao.findById",
                               fetchType = FetchType.EAGER)
                    )
    })
    List<Account> findAll();

}
```

#### 11.6.2.测试

```java
package com.yuan.test;

import com.yuan.dao.AccountDao;
import com.yuan.doman.Account;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

public class AccountTest {

    InputStream in;
    SqlSessionFactory factory;
    SqlSession session;
    AccountDao accountDao;


    @Before
    public void before() throws IOException {
        //读取配置文件
        in = Resources.getResourceAsStream("SqlMapConfig.xml");
        //生成Session工厂
        factory = new SqlSessionFactoryBuilder().build(in);
        //生产Session对象
        session = factory.openSession();
        //获取Dao对象
        accountDao = session.getMapper(AccountDao.class);

    }


    @After
    public void after() throws IOException {
        session.commit();
        session.close();
        in.close();
    }

    /**
     * 一对一查询
     * @throws IOException
     */
    @Test
    public void findAccountAllTest() {
        List<Account> accountAll = accountDao.findAll();
        for (Account account : accountAll) {
            System.out.println("account = " + account);
        }
    }
}
```

### 11.7.多对一

#### 11.7.1.接口

UserDao.java

```java
package com.yuan.dao;

import com.yuan.doman.User;
import org.apache.ibatis.annotations.*;
import org.apache.ibatis.mapping.FetchType;

import java.util.List;
//开启二级缓存
@CacheNamespace(blocking = true)
public interface UserDao  {

    /**
     * 查询用户及其下属账户
     * @return
     */
    @Select("select * from user")
    @Results(value = {
            @Result(id = true,column = "id",property = "id"),
            @Result(column = "id",property = "accounts",
                    //配置一对多
                    many = @Many(select = "com.yuan.dao.AccountDao.findByUid",
                                //配置延迟加载
                                fetchType = FetchType.LAZY
                    ))
                })
    List<User> findAll();

}
```

AccountDao.java

```java
package com.yuan.dao;

import com.yuan.doman.Account;
import org.apache.ibatis.annotations.Select;

import java.util.List;

public interface AccountDao {

    /**
     * 根据用户Id查询 账户信息
     * @param uid
     * @return
     */
    @Select("select * from account where uid = #{uid}")
    List<Account> findByUid(Integer uid);
}
```

#### 11.7.2.测试

```java
package com.yuan.test;

import com.yuan.dao.AccountDao;
import com.yuan.dao.UserDao;
import com.yuan.doman.Account;
import com.yuan.doman.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

public class UserTest {

    InputStream in;
    SqlSessionFactory factory;
    SqlSession session;
    UserDao userDao;


    @Before
    public void before() throws IOException {
        //读取配置文件
        in = Resources.getResourceAsStream("SqlMapConfig.xml");
        //生成Session工厂
        factory = new SqlSessionFactoryBuilder().build(in);
        //生产Session对象
        session = factory.openSession();
        //获取Dao对象
        userDao = session.getMapper(UserDao.class);

    }


    @After
    public void after() throws IOException {
        session.commit();
        session.close();
        in.close();
    }

    /**
     * 一对多查询
     */
    @Test
    public void findUserAllTest() {
        List<User> users = userDao.findAll();
        for (User user : users) {
            System.out.println("user = " + user);
            List<Account> accounts = user.getAccounts();
            System.out.println("accounts = " + accounts);
        }
    }

}
```

### 11.7常用注解

```java
@Insert:实现新增 
@Update:实现更新 
@Delete:实现删除 
@Select:实现查询 
@Result:实现结果集封装
@Results:可以与@Result 一起使用，封装多个结果集 
@ResultMap:实现引用@Results 定义的封装 
@One:实现一对一结果集封装 
@Many:实现一对多结果集封装 
@SelectProvider: 实现动态 SQL 映射 
@CacheNamespace:实现注解二级缓存的使用
```

### 11.6复杂注解

```java
@Results 注解 
代替的是标签<resultMap>  
该注解中可以使用单个@Result 注解，也可以使用@Result 集合 @Results（{@Result（），@Result（）}）或@Results（@Result（）） 

@Resutl 注解 
代替了 <id>标签和<result>标签  
@Result 中 属性介绍：
	id 是否是主键字段  
	column 数据库的列名  
	property 需要装配的属性名
	one  需要使用的@One 注解（@Result（one=@One）（）））
	many  需要使用的@Many 注解（@Result（many=@many）（））） 

@One 注解（一对一）
代替了<assocation>标签，是多表查询的关键，在注解中用来指定子查询返回单一对象。
@One 注解属性介绍：
	select  指定用来多表查询的
	sqlmapper  fetchType 会覆盖全局的配置参数 lazyLoadingEnabled。
使用格式：
	@Result(column=" ",property="",one=@One(select="")) 

@Many 注解（多对一）
	代替了<Collection>标签,是是多表查询的关键，在注解中用来指定子查询返回对象集合。
	注意：
		聚集元素用来处理“一对多”的关系。需要指定映射的 Java 实体类的属性，属性的 javaType （一般为 ArrayList）但是注解中可以不定义；
	使用格式：
		@Result(property="",column="",many=@Many(select=""))
```

## 12.MyBatis的延迟加载

1.一对一(多对一)延迟加载 

```xml
<resultMap id="accountUserMap" type="account">
    <id property="id" column="id"/>
    <result property="uid" column="uid"/>
    <result property="money" column="money"/>
<!--
一（多）对一的关系映射：配置封装user的内容
select属性指定的内容：查询用户的唯一标识：
column属性指定的内容：用户根据id查询时，所需要的参数的值
-->
<association
        property="users"
        column="uid"
        javaType="user"
        select="com.yuan.dao.UserDao.findById"
/>

</resultMap>

<!--查询全部Account-->
<select id="findAll" resultMap="accountUserMap">
    SELECT * FROM ACCOUNT
</select>
```

2.一对多延迟加载

```xml
<resultMap id="userAccountMap" type="user">
    <id property="id" column="id"></id>
    <result property="username" column="username"/>
    <result property="birthday" column="birthday"/>
    <result property="sex" column="sex"/>
    <result property="address" column="address"/>
    <!--账户定义集合-->
        <!--property 集合属性名-->
        <!--ofType 集合的泛型-->
    <!--column 用不到不写 现在的作用是把用户的ID作为参数传到账户的查询参数(一对多)-->
    <!--select 用的时候调用账户配置文件中的查询方法-->
    <collection
            property="accounts"
            column = "id"
            select="com.yuan.dao.AccountDao.findByUid"
            ofType="account"/>
</resultMap>

<!--查询全部User-->
<select id="findAll" resultMap="userAccountMap">
    SELECT * FROM USER
</select>
```

## 13.MyBatis的缓存

像大多数的持久化框架一样，Mybatis 也提供了缓存策略，通过缓存策略来减少数据库的查询次数，从而提 高性能。 Mybatis 中缓存分为一级缓存，二级缓存。

![1566983830134](C:\Users\ASUS\AppData\Local\Temp\1566983830134.png)

### 13.1.一级缓存

> 它指的是Mybatis中SqlSession对象的缓存。
>
> 当我们执行查询之后，查询的结果会同时存入到SqlSession为我们提供一块区域中。
>
>  该区域的结构是一个Map。当我们再次查询同样的数据，mybatis会先去sqlsession中
>
> 查询是否有，有的话直接拿出来用。
>
> 当SqlSession对象消失时，mybatis的一级缓存也就消失了。
>
> 一级缓存是 SqlSession 级别的缓存，只要 SqlSession 没有 flush 或 close，它就存在。 **默认开启哒！**
>
> 当调用 SqlSession 的修改，添加，删除，commit()，close()等方法时，就会清空一级缓存。

### 13.2.二级缓存

它指的是Mybatis中SqlSessionFactory对象的缓存。由同一个SqlSessionFactory对象创建的SqlSession共享其缓存。
	二级缓存(默认开启)的使用步骤：
	**第一步：**让Mybatis框架支持二级缓存（在SqlMapConfig.xml中配置）

```xml
<!--开启二级缓存-->
    <settings>
        <setting name="cacheEnabled" value="true"/>
    </settings>
```

**第二步：**让当前的映射文件支持二级缓存（在IUserDao.xml中配置）
	添加标签<cache/>【在映射文件中添加了这个标签就完事了】

**第三步：**	在select标签添加属性**useCache="true"**![1566984772574](C:\Users\ASUS\AppData\Local\Temp\1566984772574.png)

### 13.3.注意事项

当我们在使用二级缓存时，所缓存的类一定要实现 java.io.Serializable 接口，这种就可以使用序列化 方式来保存对象。 

## 小结3

**什么是延迟加载**

就是在需要用到数据时才进行加载，不需要用到数据时就不加载数据。延迟加载也称懒加载. 好处：先从单表查询，需要时再从关联表去关联查询，大大提高数据库性能，因为查询单表要比关联查询多张表速 度要快。 

**延迟加载的坏处**？

因为只有当需要用到数据时，才会进行数据库查询，这样在大批量数据查询时，因为查询工作也要消耗 时间，所以可能造成用户等待时间变长，造成用户体验下降。 

**注解方式如何开启二级缓存**

**第一步：**配置文件开启二级缓存

**第二步：**在接口文件接口名上添加注解@CacheNamespace(blocking = true)

