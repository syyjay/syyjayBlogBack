---
layout: spring
title: JdbcTemplate基本使用
date: 2020-11-02 10:15:38
Tags: java
---

# JdbcTemplate概述

它是Spring框架中提供的一个对象，是对原始繁琐的JdbcAPI对象的简单封装。spring框架为我们提供了很多操作模板类，例如：操作关系型数据的JdbcTemplate和HibernateTemplate，操作nosql数据库的RedisTemplate，操作消息队列的JmsTemplate等等

# Jdbc开发步骤

1. 导入spring-jdbc和spring-tx坐标
2. 创建数据库表和实体
3. 创建JdbcTemplate对象
4. 执行数据库操作

## 导入坐标

```xml
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-jdbc</artifactId>
  <version>5.0.5.RELEASE</version>
</dependency>

<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-tx</artifactId>
  <version>5.0.5.RELEASE</version>
</dependency>
```

## 创建数据库表和实体

![jdbcTemplate](/Users/nathan/Desktop/syyjay.github.io/source/_posts/JdbcTemplate基本使用/jdbcTemplate.jpg)

```java
public class User {
   	private String name;
    private double money;
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public double getMoney() {
        return money;
    }
    public void setMoney(double money) {
        this.money = money;
    }
}
```

## 创建JdbcTemplate对象，执行数据库操作

```java
ComboPooledDataSource dataSource = new ComboPooledDataSource();
dataSource.setDriverClass("com.mysql.jdbc.Driver");
dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/suboDb");
dataSource.setUser("root");
dataSource.setPassword("123456root");

JdbcTemplate jdbcTemplate = new JdbcTemplate();
jdbcTemplate.setDataSource(dataSource);
jdbcTemplate.update("insert into  tb_user (name,money) values(?,?)","张三","100");
```

# Spring使用JdbcTemplate

首先导入响应的坐标

```xml
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

<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-context</artifactId>
  <version>5.0.5.RELEASE</version>
</dependency>

<dependency>
  <groupId>c3p0</groupId>
  <artifactId>c3p0</artifactId>
  <version>0.9.1.2</version>
</dependency>

<dependency>
  <groupId>mysql</groupId>
  <artifactId>mysql-connector-java</artifactId>
  <version>8.0.21</version>
</dependency>
```

然后将JdbcTemplate的创建权交给Spring,将DataSource的创建权也交给Spring，在Spring内部将数据源注入到JdbcTemplate模板对象中：

```xml
<!--    数据源-->
    <bean
    id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property
                name="driverClass" value="com.mysql.jdbc.Driver"></property>
        <property
                name="jdbcUrl" value="jdbc:mysql://localhost:3306/suboDb"></property>
        <property
                name="user" value="root"></property>
        <property
                name="password" value="123456root"></property>
     </bean>

<!--    Jdbc-->
    <bean
    id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property
                name="dataSource" ref="dataSource"></property>
    </bean>
```

然后进行JdbcTemplate的常用操作：

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
public class JdbcTemplateCRUDTest {
    @Autowired
    private JdbcTemplate jdbcTemplate;
    @Test
  	//修改
    public void  testUpdate(){
        jdbcTemplate.update("update tb_user  set money = ? where name = ?","1000","lisi");
    }

    @Test
  	//删除
    public void testDelete(){
        jdbcTemplate.update("delete  from tb_user where name = ?","lisi");
    }
  
    @Test
  	//查询所有
    public void testQueryAll(){
        List<User> users = jdbcTemplate.query("select * from tb_user", new BeanPropertyRowMapper<User>(User.class));
        for (User user : users){
            System.out.println(user.getName());
        }
    }

    @Test
  	//查询单个
    public  void testQueryOne(){
        User user = jdbcTemplate.queryForObject("select * from tb_user where name = ?", new BeanPropertyRowMapper<User>(User.class), "张三");
        System.out.println(user.getName());
    }

    @Test
  	//查询数量
    public void  testQueryCount(){
        Long l = jdbcTemplate.queryForObject("select count(*) from tb_user",Long.class);
        System.out.println(l);
    }
}
```

