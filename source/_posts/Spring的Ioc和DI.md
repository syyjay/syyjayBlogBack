---
title: Spring的Ioc和DI
date: 2020-10-16 09:07:46
tags: java
Comments: true
---

[TOC]

# Spring的Ioc和DI

## 一 、将对象的创建交个Springring器（Ioc)

### 1.Spring开发步骤  

#### 	1.1 导入Spring开发的基本坐标  

```xml
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-context</artifactId>
  <version>5.2.9.RELEASE</version>
</dependency>
```
#### 1.2 编写一个javaBean(这里我使用Dao接口和实现类)

```java
public interface UserDao {
	void  save();
}

public void save() {
    System.out.println("UserDao Save Runing");
}
```
#### 1.3 创建Spring核心配置文件  

		在resources目录下创建applicationContext.xml配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?><beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:util="http://www.springframework.org/schema/util"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/util https://www.springframework.org/schema/util/spring-util.xsd">
/beans>
```
#### 1.4 在Spring配置文件中配置javaBean(UserDaoImpl)

```xml
<?xml version="1.0" encoding="UTF-8"?><beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:util="http://www.springframework.org/schema/util"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/util https://www.springframework.org/schema/util/spring-util.xsd">
      <bean id="userDao" class="com.syyjay.dao.Impl.UserDaoImpl"></bean>
</beans>
```
#### 1.5 使用Spring的API获取Bean实例

```java
@Test
public void test1(){
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
    UserDaoImpl userDao = (UserDaoImpl) applicationContext.getBean("userDao");
    userDao.save();;
}
```

	这样UserDao的就由Spring创建了一个实例对象

### 2.Spring的配置文件

####  2.1 由于配置对象已经交由Spring来创建，默认情况调用的类中的无参构造函数，如果没有无参构造函数则不能创建成功。  

基本属性：
				id：Bean实例在Spring容器中的唯一实例
				class:Bean的权限定名称(类的全名称)

#### 2.2 Bean标签范围配置

scope:指对象的作用范围，取值如下:  

<table border="1">
	  <tr>
	    <th>取值范围</th>
	    <th>说明</th>
	  </tr>
	  <tr>
	    <td>singleton</td>
	    <td>默认值，单例</td>
	  </tr>
	  <tr>
	    <td>prototype</td>
	    <td>多例的</td>
	  </tr>
	  <tr>
	    <td>request</td>
	    <td>WEB项目中，Spring创建一个Bean的对象，将对象存到request域中</td>
	  </tr>
	   <tr>
	    <td>global</td>
	    <td>WEB项目中，应用在Portlet环境，如果没有Protlet环境那么globalSession相当于session</td>
	  </tr>
	</table>  

**1）当scope取值为<font color="red">singleton</font>时**

Bean的实例化个数：1个

Bean实例化时机：当Spring核心文件被加载时，实例化配置的Bean示例

Bean的生命周期:

	对象创建：当应用加载时，创建容器时，对象就被创建了
	
	对象运行：只要容器在，对象就一直活着
	
	对象销毁：当应用卸载，销毁容器时，对象就销毁了	

**2)当scope取值为<font color="red">prototype</font>时**

Bean的实例化个数：多个 

Bean实例化时机：当调用getBean()方法时，实例化Bean

Bean的生命周期:

	对象创建：当使用对象时，创建新的对象实例
	
	对象运行：只要对象在使用中，对象就一直活着 
	
	对象销毁：当对象长时间不用时，被java的垃圾回收机制回收了

#### 2.3	Bean生命周期配置

init-method:指定类中初始化方法的名称  
destroy-method:指定类中销毁方法的名称

#### 2.4 Bean实例化的三种方式 

无参构造函数实例化
工厂静态方法实例化
工厂动态方法实例化

1）使用无参构造函数方法实例化  
			它会根据默认无参构造函数方法来创建实例，如果bean中没有默认的无参构造函数，将会创建失败(在applicationContext.xml中添bean对象)   

```xml
 <!--    构造函数获取示例-->
 <bean id="userDao" class="com.syyjay.dao.Impl.UserDaoImpl"></bean>
```
2）工厂的静态实例化

工厂的静态方法返回Bean实例

```java
public class StaticFactoryBean {
    public static UserDao createUserDao(){
        return  new UserDaoImpl();
    }
}
```

```xml
<!--静态工厂获取实例-->
<bean
        id="userDao" class="com.syyjay.factory.StaticFactoryBean" factory-method="createUserDao">
</bean>
```

3）工厂实例方法实例化

工厂的非静态方法返回Bean实例

```java
public class DynamicFactoryBean {
    public UserDao createUserDao(){
        return new UserDaoImpl();
    }
}
```

```xml
<!--工厂非静态方法获取实例-->
<bean id="factoryBean" class="com.syyjay.factory.DynamicFactoryBean"></bean>
<bean id="userDao" factory-bean="factoryBean" factory-method="createUserDao"></bean>
```



## 二、依赖注入（DI）

UserServices中调用UserDao的save方法，这时候可以在UserServices中注入UserDao实例。

### 1.创建UserServices，在UserServices中调用userDao的save方法，如果不通过注入，调用方式如下：

```java
public class UserServicesImpl implements UserSevices {
    public void save() {
        ApplicationContext applicationContext= new ClassPathXmlApplicationContext("applicationContext.xml");
        UserDao userDao = (UserDao)applicationContext.getBean("userDao");
        userDao.save();
    }
}
```

### 2.将UserServicesImpl的创建交个Spring容器

```xml
<bean id="userServices" class="com.syyjay.services.Impl.UserServicesImpl"></bean>
```

从Spring容器中获取UserServices

```java
ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
UserService userService = (UserService) applicationContext.getBean("userService");
userService.save();
```

### 3.UserDao和UserServices都在Spring容器中，所以在使用UserServices时，可以将Spring容器中的 UserDao设置到UserServices内部

### 4.怎样将UserDao注入到UserServices中呢

	构造方法
	
	set方法

#### 4.1 set方法注入

			在UserServiceImpl中添加setUserDao方法

```java
public class UserServicesImpl implements UserSevices {
    private UserDao userDao;
    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }
    public void save() {
        userDao.save();
    }
}
```

			配置Spring容器调用set方法进行注入

```xml
<bean id="userDao" class="com.syyjay.dao.Impl.UserDaoImpl"></bean>
<bean id="userServices" class="com.syyjay.services.Impl.UserServicesImpl">
    <property name="userDao" ref="userDao"></property>
</bean>
```

			也可以使用p标签进行set方法进行注入，具体配置如下，首先引入p命名空间：

```xml
xmlns:p="http://www.springframework.org/schema/p"
```

其次修改注入方式：

```xml
<bean id="userServices" class="com.syyjay.services.Impl.UserServicesImpl" p:userDao-ref="userDao"></bean>
```

#### 4.2 构造函数注入

		首先创建有参构造函数：

```java
public UserServicesImpl(UserDao userDao){
    this.userDao = userDao;
}
```

		其次修改注入方式：

```xml
<bean id="userServices" class="com.syyjay.services.Impl.UserServicesImpl" >
    <constructor-arg name="userDao" ref="userDao"></constructor-arg>
</bean>
```

#### 4.3 Bean依赖注入的数据类型

	除了上面注入的引用Bean之外，还可以注入普通数据类型，集合等，总共可以注入三种数据类型如下：

- 普通数据类型
- 引用数据类型
- 集合数据类型

其中引用数据类型就是我们上面使用，这里不在做介绍，下面以set方法注入普通数据类型和集合数据类型

##### 	4.3.1 普通数据类型的注入

```java
public class UserDaoImpl implements UserDao {

    private String company;
    private int age;

    public void setCompany(String company) {
        this.company = company;
    }

    public void setAge(int age) {
        this.age = age;
    }
    public void save() {
        System.out.println(company + "===" + age);
    }
}
```

```xml
<bean id="userDao" class="com.syyjay.dao.Impl.UserDaoImpl">
    <property name="age" value="18"></property>
    <property name="company" value="baidu"></property>
</bean>
```

##### 	4.3.2 集合类型的注入

###### 	4.3.2.1 集合类型List<String>的注入

```java
public class UserDaoImpl implements UserDao {

    private String company;
    private int age;
    private List<String> stringList;

    public void setCompany(String company) {
        this.company = company;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public void setStringList(List<String> stringList) {
        this.stringList = stringList;
    }

    public void save() {
        System.out.println(company + "===" + age);
        System.out.println(stringList);
    }
}
```

```xml
<bean id="userDao" class="com.syyjay.dao.Impl.UserDaoImpl">
    <property name="age" value="18"></property>
    <property name="company" value="baidu"></property>
    <property name="stringList">
        <list>
            <value>aaa</value>
            <value>bbb</value>
            <value>ccc</value>
        </list>
    </property>
</bean>
```

###### 	4.3.2.2 集合类型List<User>的注入

```java
public class UserDaoImpl implements UserDao {

    private String company;
    private int age;
    private List<String> stringList;
    private List<User> userList;

    public void setCompany(String company) {
        this.company = company;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public void setStringList(List<String> stringList) {
        this.stringList = stringList;
    }

    public void setUserList(List<User> userList) {
        this.userList = userList;
    }

    public void save() {
        System.out.println(company + "===" + age);
        System.out.println(stringList);
        System.out.println(userList);
    }
}
```

```xml
    <bean id="u1" class="com.syyjay.domain.User" ></bean>
    <bean id="userDao" class="com.syyjay.dao.Impl.UserDaoImpl">
        <!--普通数据注入-->
        <property name="age" value="18"></property>
        <property name="company" value="baidu"></property>
        <!--集合类型数据之 List<String>注入-->
        <property name="stringList">
            <list>
                <value>aaa</value>
                <value>bbb</value>
                <value>ccc</value>
            </list>
        </property>
        <!--集合类型数据之 List<User>注入-->
        <property name="userList">
            <list>
                <bean class="com.syyjay.domain.User" ></bean>
                <ref bean="u1"></ref>
            </list>
        </property>
    </bean>

    <bean id="userServices" class="com.syyjay.services.Impl.UserServicesImpl" >
       <property name="userDao" ref="userDao"></property>
    </bean>
```

###### 	4.3.2.3 集合类型Map<String,User>的注入

```java
public class UserDaoImpl implements UserDao {

    private String company;
    private int age;
    private List<String> stringList;
    private List<User> userList;
    private Map<String,User> userMap;

    public void setCompany(String company) {
        this.company = company;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public void setStringList(List<String> stringList) {
        this.stringList = stringList;
    }

    public void setUserList(List<User> userList) {
        this.userList = userList;
    }

    public void setUserMap(Map<String, User> userMap) {
        this.userMap = userMap;
    }

    public void save() {
        System.out.println(company + "===" + age);
        System.out.println(stringList);
        System.out.println(userList);
        System.out.println(userMap);
    }
}
```

```xml
 <bean id="u1" class="com.syyjay.domain.User" ></bean>
    <bean id="userDao" class="com.syyjay.dao.Impl.UserDaoImpl">
        <!-- 普通数据注入-->
        <property name="age" value="18"></property>
        <property name="company" value="baidu"></property>
        <!-- 集合类型数据之 List<String>注入-->
        <property
                name="stringList">
            <list>
                <value>aaa</value>
                <value>bbb</value>
                <value>ccc</value>
            </list>
        </property>
        <!--集合类型数据之 List<User>注入-->
        <property name="userList">
            <list>
                <bean class="com.syyjay.domain.User" ></bean>
                <ref bean="u1"></ref>
            </list>
        </property>
        <!--集合类型之Map<String,User>-->
        <property name="userMap">
            <map>
                <entry key="user1" value-ref="u1"></entry>
            </map>
        </property>
    </bean>

    <bean id="userServices" class="com.syyjay.services.Impl.UserServicesImpl" >
       <property name="userDao" ref="userDao"></property>
    </bean>
```

###### 	4.3.2.4 集合类型Properties的注入

```java
public class UserDaoImpl implements UserDao {
  private String company;
  private int age;
  private List<String> stringList;
  private List<User> userList;
  private Map<String,User> userMap;
  private Properties properties;

  public void setCompany(String company) {
      this.company = company;
  }

  public void setAge(int age) {
      this.age = age;
  }

  public void setStringList(List<String> stringList) {
      this.stringList = stringList;
  }

  public void setUserList(List<User> userList) {
      this.userList = userList;
  }

  public void setUserMap(Map<String, User> userMap) {
      this.userMap = userMap;
  }

  public void setProperties(Properties properties) {
      this.properties = properties;
  }

  public void save() {
      System.out.println(company + "===" + age);
      System.out.println(stringList);
      System.out.println(userList);
      System.out.println(userMap);
      System.out.println(properties);
  }
}
```
```xml
<bean id="u1" class="com.syyjay.domain.User" ></bean>
<bean id="userDao" class="com.syyjay.dao.Impl.UserDaoImpl">
    <!-- 普通数据注入-->
    <property name="age" value="18"></property>
    <property name="company" value="baidu"></property>
    <!-- 集合类型数据之 List<String>注入-->
    <property
            name="stringList">
        <list>
            <value>aaa</value>
            <value>bbb</value>
            <value>ccc</value>
        </list>
    </property>
    <!--集合类型数据之 List<User>注入-->
    <property name="userList">
        <list>
            <bean class="com.syyjay.domain.User" ></bean>
            <ref bean="u1"></ref>
        </list>
    </property>
    <!--集合类型之Map<String,User>-->
    <property
            name="userMap">
        <map>
            <entry key="user1" value-ref="u1"></entry>
        </map>
    </property>
    <!--集合类型之Properties-->
    <property
            name="properties">
        <props>
            <prop key="p1">aaa</prop>
            <prop key="p2">aaa</prop>
        </props>
    </property>
</bean>

<bean id="userServices" class="com.syyjay.services.Impl.UserServicesImpl" >
   <property name="userDao" ref="userDao"></property>
</bean>
```

##### 	4.3.3 引入其他配置文件

实际开发中，Spring的配置内容非常多，这就导致Spring配置很繁杂且体积很大，所以，可以将部分配置拆解到其他配置文件中，而在Spring主配置文件通过import标签进行加载

```xml
<import resource="applicationContext-xxx.xml"/>
```

## 三、知识要点

Spring的重点配置

```xml
<bean>标签
    id属性:在容器中Bean实例的唯一标识，不允许重复
    class属性:要实例化的Bean的全限定名
    scope属性:Bean的作用范围，常用是Singleton(默认)和prototype
    <property>标签：属性注入
        name属性：属性名称
        value属性：注入的普通属性值
        ref属性：注入的对象引用值
        <list>标签
        <map>标签
        <properties>标签
    <constructor-arg>标签
<import>标签:导入其他的Spring的分文件
```

## 四、SpringAPI