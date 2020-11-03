---
title: IoC和DI的注解开发
date: 2020-10-19 09:12:15
tags: java
---

[TOC]

# 一、配置数据源 

## 1.1 数据源（连接池）的作用

* 数据源（连接池）是提高程序性能如何出现的 

* 事先实例化数据源，初始化部分连接资源

* 使用连接资源时从数据源中获取

* 使用完毕将连接池归还给数据源

  常见的 数据源（连接池）：<font color="red">DBCP、C3P0、BoneCP、Druid</font>

## 1.2 数据源开发步骤

- 导入数据源的坐标和数据源驱动坐标
- 创建数据源对象
- 设置数据源的基本连接数据
- 使用数据源获取数据连接资源，用完之后归还连接资源

## 1.3 数据源的手动创建

1. 导入c3p0和druid坐标

```xml
<dependency>
  <groupId>c3p0</groupId>
  <artifactId>c3p0</artifactId>
  <version>0.9.1.2</version>
</dependency>

<dependency>
  <groupId>com.alibaba</groupId>
  <artifactId>druid</artifactId>
  <version>1.1.10</version>
</dependency>
```

2. 导入mysql数据驱动坐标

```xml
<dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>8.0.21</version>
</dependency>
```

3. 创建C3P0连接池

   ```java
   @Test
   //c3p0
   public void  test1() throws Exception {
       ComboPooledDataSource dataSource = new ComboPooledDataSource();
       dataSource.setDriverClass("com.mysql.cj.jdbc.Driver");
       dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/suboDb");
       dataSource.setUser("root");
       dataSource.setPassword("123456root");
       Connection connection = dataSource.getConnection();
       System.out.println(connection);
       connection.close();
   }
   ```

4. 创建druid连接池

   ```java
   @Test
   //druid
   public void  test2() throws Exception {
       DruidDataSource dataSource = new DruidDataSource();
       dataSource.setDriverClassName("com.mysql.cj.jdbc.Driver");
       dataSource.setUrl("jdbc:mysql://localhost:3306/suboDb");
       dataSource.setUsername("root");
       dataSource.setPassword("123456root");
       DruidPooledConnection connection = dataSource.getConnection();
       System.out.println(connection);
       connection.close();
   }
   ```

5. 使用配置文件创建连接池

   创建jdbc.properties文件，并读取其中的配置

   ```java
   @Test
   //测试手动创建 c3p0 数据源(加载properties配置文件)
   public void  test3() throws Exception {
       //读取配置文件
       ResourceBundle resourceBundle = ResourceBundle.getBundle("jdbc");
       String driver = resourceBundle.getString("jdbc.driver");
       String url = resourceBundle.getString("jdbc.url");
       String username = resourceBundle.getString("jdbc.username");
       String password = resourceBundle.getString("jdbc.password");
   
       DruidDataSource dataSource = new DruidDataSource();
       dataSource.setDriverClassName(driver);
       dataSource.setUrl(url);
       dataSource.setUsername(username);
       dataSource.setPassword(password);
       DruidPooledConnection connection = dataSource.getConnection();
       System.out.println(connection);
       connection.close();
   }
   ```

## 1.4 Spring配置数据源

1. 将DataSource的创建权交给Spring

   ```xml
   <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
       <property name="driverClass" value="${jdbc.driver}"></property>
       <property name="jdbcUrl" value="${jdbc.url}"></property>
       <property name="user" value="${jdbc.username}"></property>
       <property name="password" value="${jdbc.password}"></property>
   </bean>
   ```

2. 从容器中获取数据源

   ```java
   @Test
   // 将DataSource交给Spring容器去创建
   public void  test4() throws Exception {
       ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
       DataSource dataSource = (DataSource) applicationContext.getBean("dataSource");
       Connection connection = dataSource.getConnection();
       System.out.println(connection);
   }
   ```

3. 抽取jdbc配置文件，并通过Spring的方式加载配置文件

   + 引入context命名空间和约束路径

     空间：xmlns:context="http://www.springframework.org/schema/context"

     约束路径：http://www.springframework.org/schema/context                       http://www.springframework.org/schema/context/spring-context.xsd

```xml
<!--加载外部的properties文件-->
    <context:property-placeholder location="classpath:jdbc.properties"/>
    
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${jdbc.driver}"></property>
        <property name="jdbcUrl" value="${jdbc.url}"></property>
        <property name="user" value="${jdbc.username}"></property>
        <property name="password" value="${jdbc.password}"></property>
    </bean>
```

这样同样可以通过Spring获取DataSource，但是jdbc配置已经抽取到配置文件jdbc.properties文件中了。

# 二、Spring注解开发

##  2.1 Spring原始注解

Spring是轻代码而重配置的框架，配置比较繁重，影响开发效率，所以注解开发是一种趋势，注解代替xml文件可以简化配置，提高开发效率

Spring原始注解主要是替代<bean>的配置

<table border="1">
	<th>注解</th><th>说明</th>
	<tr>
    <td>@Component</td>
    <td>使用在类上用于实例化Bean</td>
  </tr> <tr>
    <td>@Controller</td>
    <td>使用在类上用于实例化Bean</td>
  </tr><tr>
    <td> @Service</td>
    <td>使用在service层类上用于实例化Bean</td>
  </tr>
    <tr>
    <td>@Repository</td>
    <td>使用在dao层类上用于实例化Bean</td>
  </tr>  <tr>
    <td>@Autowired</td>
    <td>使用在字段上用于根据类型依赖注入</td>
  </tr><tr>
    <td>@Qualifier</td>
    <td>结合@Autowired一起使用用于根据名称进行依赖注入</td>
  </tr><tr>
    <td>@Resource</td>
    <td>相当于@Autowired+@Qualifier，按照名称进行注入</td>
  </tr> <tr>
    <td>@Value</td>
    <td>注入普通属性</td>
  </tr> <tr>
    <td>@Controller</td>
    <td>使用在类上用于实例化Bean</td>
  </tr>  <tr>
    <td>@Scope</td>
    <td>标注Bean的作用范围</td>
  </tr><tr>
    <td>@PostConstruct</td>
    <td>使用在方法上标注该方法是Bean的初始化方法</td>
  </tr>
  <tr>
    <td>@PreDestroy</td>
    <td>使用在方法上标注该方法是Bean的销毁方法</td>
  </tr>
</table>

<font color="red">注意：</font>

在使用注解时，需要在applicationContext.xml中配置组件扫描，作用是指定那个包及其子包下的Bean需要进行扫描，以便识别使用注解配置类、字段和方法。

```xml
<!--配置组件扫描-->
<context:component-scan base-package="com.syyjay"/>
```

1. 使用@Component或@Repository标识UserDaoImpl需要Spring进行实例化

   ```java
   //@Service("userDao")
   //@Component("userDao")
   @Repository("userDao")
   public class UserDaoImpl implements UserDao {
       @Override
       public void save() {
           System.out.println("UserDao Save Runing");
       }
   }
   ```

2. 使用@Compont或@Service标识UserServiceImpl需要Spring进行实例化

   使用@Autowired或者@Autowired+@Qulifier或者@Resource进行userDao的注入

   ```java
   @Component("userService")
   @Service("userService")
   public class UserServiceImpl implements UserService {
       @Autowired
   //    @Resource(name="userDao")
   //    @Qualifier("userDao") //(结合使用按照名称进行注入)
       private UserDao userDao;
   
       @Override
       public void save() {
           userDao.save();
       }
   }
   ```

3. 使用@Value进行字符串的注入

   ```java
   @Repository("userDao")
   public class UserDaoImpl implements UserDao {
       @Value("注入普通数据")
       private String str;
       @Value("${jdbc.driver}")
       private String driver;
   
       @Override
       public void save() {
           System.out.println(str);
           System.out.println(driver);
           System.out.println("UserDao Save Runing");
       }
   }
   ```

   

4. 使用@Scope标注Bean的范围

   ```java
   //@Scope("prototype")
   @Scope("singleton")
   public class UserDaoImpl implements UserDao {
      //此处省略代码
   }
   ```

5. 使用@PostConstruct标注初始化方法，使用@PreDestroy标注销毁方法

   ```java
   @PostConstruct
   public void init(){
     System.out.println("初始化方法....");
   }
   
   @PreDestroy
   public void destroy(){  
     System.out.println("销毁方法.....");
   }
   ```

## 2.2 Spring新注解

使用上面的注解还没有完全替代xml配置文件，还需要使用注解替代如下配置：

* 非自定义的Bean的配置：<bena>
* 加载properties文件的配置:`<context:property-placeholder>`
* 组件扫描：`<context:component-scan>`
* 引入其他文件：`<import>`

可以使用新注解替代

<table border="1">
  <th>注解</th><th>说明</th>
  <tr>
    <td>@Configuration</td>
    <td>用于指定当前类是一个 Spring 配置类，当创建容器时会从该类上加载注解</td>
  </tr>
  <tr>
    <td>@ComponentScan</td>
    <td>用于指定 Spring 在初始化容器时要扫描的包。
作用和在 Spring 的 xml 配置文件中的
      <context:component-scan base-package="com.syyjay"/>一样</td>
  </tr>
  <tr>
    <td>@Bean</td>
    <td>使用在方法上，标注将该方法的返回值存储到 Spring 容器中</td>
  </tr>
  <tr>
    <td>@PropertySource</td>
    <td>用于加载.properties 文件中的配置</td>
  </tr>
   <tr>
    <td>@Import</td>
    <td>用于导入其他配置类</td>
  </tr>
</table>

1. 新建一个配置类：SpringConfiguration，用来替代applicationContext.xml

   @Configuration
   @ComponentScan
   @Import

   ```java
   @Configuration
   @ComponentScan("com.syyjay")
   @Import({DataSourceConfiguration.class})
   public class SpringConfiguration {
   
   }
   ```

2. 新建DataSourceConfiguration，来获取DataSource

   @PropertySource

   @Value

   ```java
   @PropertySource("classpath:jdbc.properties")
   public class DataSourceConfiguration {
       @Value("${jdbc.driver}")
       private String driver;
       @Value("${jdbc.url}")
       private String url;
       @Value("${jdbc.username}")
       private String username;
       @Value("${jdbc.password}")
       private String password;
   
       @Bean("dataSource")
       public DataSource getDataSource() throws Exception{
           ComboPooledDataSource dataSource = new ComboPooledDataSource();
           dataSource.setDriverClass(driver);
           dataSource.setJdbcUrl(url);
           dataSource.setUser(username);
           dataSource.setPassword(password);
           return dataSource;
       }
   }
   ```

3. 通过配置类创建配置容器，并获取想要的bean

   ```java
   @Test
   public  void testAnnoConfiguration() throws  Exception{
       ApplicationContext applicationContext = new AnnotationConfigApplicationContext(SpringConfiguration.class);
       UserService userService = (UserService)applicationContext.getBean("userService");
       userService.save();
   
       DataSource dataSource = (DataSource) applicationContext.getBean("dataSource");
       Connection connection = dataSource.getConnection();
       System.out.println(connection);
   }
   ```

# 三、Spring集成Junit

##  3.1 为什么要用Spring集成Junit？

​	在单元测试时，每个测试方法都要去加载Spring配置，然后获取bean对象，必不可少

```java
ApplicationContext applicationContext = new AnnotationConfigApplicationContext(SpringConfiguration.class);
UserService userService = (UserService)applicationContext.getBean("userService");
```

为了避免这样重复的获取，可以让SpringJunit负责创建Spring容器，只需要将Spring的配置文件告诉它即可，将需要进行测试的Bean直接在测试类中进行注入

## 3.2 Spring集成Junit步骤

* 导入SpringJunit的步骤

* 使用@Runwith直接替换原来的运行期

* 使用@ContextConfiguration指定配置文件或者配置类

* 使用@Autowired注入需要测试的对象

* 创建方法进行测试

  

## 3.3 Spring集成Junit实现

1. 导入Spring集成Junit坐标

```xml
<dependencies>
  <dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
  </dependency>

  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>5.0.5.RELEASE</version>
  </dependency>
```

2. 新建一个测试类：SpringJunitTest,并使用@Runwith直接替换原来的运行期

```java
@RunWith(SpringJUnit4ClassRunner.class)
public class SpringJUnitTest {
  
}
```

​	3.  使用@ContextConfiguration指定配置文件或配置类

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = {SpringConfiguration.class})
public class SpringJUnitTest {
 
}
```

4. 使用@Autowired注入需要测试的对象

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = {SpringConfiguration.class})
public class SpringJUnitTest {
    @Autowired
    private UserService userService;

    @Autowired
    private DataSource dataSource;
}
```

5. 创建测试方法

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = {SpringConfiguration.class})
public class SpringJUnitTest {
    @Autowired
    private UserService userService;

    @Autowired
    private DataSource dataSource;

    @Test
    public void test1() throws SQLException {
        userService.save();
        System.out.println(dataSource.getConnection());
    }
}
```