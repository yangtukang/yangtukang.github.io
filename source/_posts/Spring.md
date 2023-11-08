---
title: Spring
date: 2023-06-21 14:34:53
tags: ["Spring"]
---

# Spring

## Spring体系结构

![image-20201201150858218](https://ytk-imgs.oss-rg-china-mainland.aliyuncs.com/16e33ff7e75cfb95cf8255f190566b08.png)

## Spring相关概述

*   Spring是面向Bean的编程
*   Spring的两大核心技术
    *   控制反转（IOC）控制反转是思想
    *   依赖注入（DI）依赖注入是实现
    *   面向切面编程 （AOP）

## IOC

*   IOC(控制反转)，把对象创建和对象之间的调用过程，交给 Spring 进行管理

*   使用IOC目的：为了降低耦合度

*   IOC底层原理：xml 解析、工厂模式、反射

*   IOC 思想基于 IOC 容器完成，IOC 容器底层就是对象工厂

### 使用XML配置文件注入

#### 普通Bean

创建User类

```java
public class User {
    private String name;
    private String sex;
}
```

创建XML文件 (bean.xml)

```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!--配置User对象创建-->
    <bean id="user" class="com.ytk.bean.User">

    </bean>
</beans>
```

编写测试类代码

```java
@Test
public void test01(){
    //加载Spring 使用XML配置
    ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
    User user = (User) context.getBean("user");
    System.out.println(user);
}

//User{name='null', sex='null'}
```

##### Setter注入

```XML
<bean id="user" class="com.ytk.bean.User">
    <property name="name" value="小明"></property>
    <property name="sex" value="男"></property>
</bean>
```

运行效果

```java
@Test
public void test01(){
    //加载Spring 使用XML配置
    ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
    User user = (User) context.getBean("user");
    User user2 = context.getBean("user", User.class);
    System.out.println(user);
    System.out.println(user2);
    System.out.println(user==user2);
}
//调用setName方法
//调用setSex方法
//User{name='小明', sex='男'}
//User{name='小明', sex='男'}
//true IOC管理的Bean默认是单例
```

##### 构造方法注入

```java
public class Student {
    private String sno;
    private String name;
    private String sex;
  
}
```

XML文件

```XML
<bean id="student" class="com.ytk.bean.Student">
    <constructor-arg name="sno" value="100001"></constructor-arg>
    <constructor-arg name="name" value="张三"></constructor-arg>
    <constructor-arg name="sex" value="男"></constructor-arg>
</bean>
```

测试代码

```java
@Test
public void test01(){
    //加载Spring 使用XML配置
    ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
    Student student = context.getBean("student", Student.class);
    System.out.println(student);
}

//Student{sno='100001', name='张三', sex='男'}
```

##### 接口注入

```java
public interface UserService {
    void save(User user);

}
```

```java
public class UserServiceImpl implements UserService{
    @Override
    public void save(User user) {
        System.out.println("保存用户: " + user);
    }
}
```

XML配置文件

```XML
<bean id="userService" class="com.ytk.service.UserServiceImpl">
</bean>
```

测试效果

```java
@Test
public void test01(){
    //加载Spring 使用XML配置
    ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
    User user = (User) context.getBean("user");
    System.out.println(user);
    UserService userService = (UserService)context.getBean("userService");
    userService.save(user);
}

//User{name='小明', sex='男'}
//保存用户: User{name='小明', sex='男'}
```

在配置文件中定义bean类型和返回类型不一致

### 使用Spring注解方式

开启组件扫描

*   如果扫描多个包使用逗号隔开
*   或者扫描该包的上层目录（推荐）

```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!--包扫描-->
    <context:component-scan base-package="com.ytk.annotation"/>

</beans>
```

注入属性

```java
@Component("student")
public class Student {
    private String sno;
    @Value("小刚")
    private String name;
    private String sex;
}

//基于注解
@Test
public void test02(){
    //加载Spring 使用注解配置
    ApplicationContext context = new ClassPathXmlApplicationContext("bean2.xml");
    Student student = context.getBean("student",Student.class);
    System.out.println(student);
}
//Student{sno='null', name='小刚', sex='null'}
```

创建类

```java
public interface StudentService {
    void add();
}


@Service(value = "studentService")
//等同于xml中<bean id="studentService" class="...">
public class StudentServiceImpl implements StudentService {

    @Autowired
    private StudentDao studentDao;

    @Override
    public void add() {
        System.out.println("annotation service add ...");
        studentDao.save();
    }
}

public interface StudentDao {
    void save();
}

@Repository(value="studentDao")
public class StudentDaoImpl implements StudentDao{

    @Override
    public void save() {
        System.out.println("将student保存到数据库");
    }
}
```

测试代码

```java
//基于注解
@Test
public void test02(){
    //加载Spring 使用注解配置
    ApplicationContext context = new ClassPathXmlApplicationContext("bean2.xml");
    StudentService studentService = context.getBean("studentService",StudentService.class);
    System.out.println(studentService);
    studentService.add();
}

//com.ytk.annotation.StudentServiceImpl@402e37bc
//annotation service add ...
//将student保存到数据库
```

## AOP

### 概念

AOP：面向切面编程，利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

通俗描述L不通过修改源代码的方式，在猪肝功能里面添加新功能。

### 底层原理

AOP底层使用动态地理

1.   有接口情况，使用JDK动态代理
     *   创建接口实现类代理对象，增强类的方法
2.   没有接口情况，使用CGLIB动态代理
     *   创建子类的代理对象，增强类的方法

### 使用

创建需要被代理的对象

```java
public interface UserService {
    void save();
}

@Service(value = "userService")
public class UserServiceImpl implements UserService {

    @Override
    public void save() {
        System.out.println("保存user对象");
    }
}
```

创建增强类

```java
@Component
@Aspect
public class MyAspect {

    @Before(value = "execution(* com.ytk.service.UserServiceImpl.save(..))")
    public void before(){
        System.out.println("before");
    }

    @After(value = "execution(* com.ytk.service.UserServiceImpl.save(..))")
    public void after(){
        System.out.println("after");
    }
}
```

编写XML配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">

    <!-- 开启注解扫描 -->
    <context:component-scan base-package="com.ytk"></context:component-scan>
    <!-- 开启Aspect生成代理对象-->
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
</beans>
```

测试

```java
@Test
public void test(){
    ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
    UserService userService = context.getBean("userService", UserService.class);
    userService.save();
}

//before
//保存user对象
//after
```

高级用法

*   使用前置@Before获取参数
*   使用@AfterRetuening获取返回值
*   使用@AfterThrowing获取异常通知

```java
@Component
@Aspect
public class CalculatorAspect {
    // 前置通知 @Before:表示前置通知 value增强User的add方法
    @Before(value = "execution(* com.ytk.bean.Calculator.*(..))")
    public void before(JoinPoint joinPoint) {
        System.out.println("before前置通知 ...");
        System.out.println("前置通知可以拿到参数"+ Arrays.toString(joinPoint.getArgs()));
    }

    // 最终通知
    @After(value = "execution(* com.ytk.bean.Calculator.*(..))")
    public void after() {
        System.out.println("after最终通知 ...");
    }

    // 后置通知（返回通知）
    @AfterReturning(value = "execution(* com.ytk.bean.Calculator.*(..))",returning = "result")
    public void afterReturning(JoinPoint joinPoint,Object result) {
        System.out.println("afterReturning后置通知（返回通知） ...");
        System.out.println("返回的结果为"+result);
    }

    // 异常通知
    @AfterThrowing(value = "execution(* com.ytk.bean.Calculator.*(..))")
    public void afterThrowing() {
        System.out.println("afterThrowing异常通知 ...");
    }

}
```

测试

```java
@Test
public void test2(){
    ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
    Calculator calculator = context.getBean("calculator", Calculator.class);
    calculator.add(1, 1);
    calculator.sub(1, 1);
    calculator.mul(1, 1);
    calculator.div(1, 1);
}

//afterReturning后置通知（返回通知） ...
//返回的结果为2
//before前置通知 ...
//前置通知可以拿到参数[1, 1]
//after最终通知 ...
//afterReturning后置通知（返回通知） ...
//返回的结果为0
//before前置通知 ...
//前置通知可以拿到参数[1, 1]
//after最终通知 ...
//afterReturning后置通知（返回通知） ...
//before前置通知 ...
//前置通知可以拿到参数[1, 1]
//after最终通知 ...
//afterReturning后置通知（返回通知） ...
//返回的结果为1

@Test
public void test2(){
    ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
    Calculator calculator = context.getBean("calculator", Calculator.class);
    calculator.div(1, 0);
}

//before前置通知 ...
//前置通知可以拿到参数[1, 0]
//after最终通知 ...
//afterThrowing异常通知 ...
//java.lang.ArithmeticException: / by zero

```

## SpringBean的生命周期

![bean的生命周期.drawio](https://ytk-imgs.oss-rg-china-mainland.aliyuncs.com/imgs1202306221911837.png)
