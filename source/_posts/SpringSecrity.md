---
title: SpringSecrity
date: 2023-06-23 17:38:28
tags:
---

# SpringSecurity

## 引入依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

使用SpringSecurity直接访问会跳转至登陆页(SpringSecurity会生成一个默认的登陆页)



配置默认用户名密码

```yaml
#application.yaml

spring:
  security:
    user:
      user: user
      password: 123456
```

## SpringSecurity完整流程

![SpringSecurity流程](https://ytk-imgs.oss-rg-china-mainland.aliyuncs.com/SpringSecurity%E6%B5%81%E7%A8%8B-1683554638821-2.png)

核心过滤器，其他核心过滤器没有展示。

### UsernamePasswordAuthentication：

​	负责处理我们在登陆页面填写了用户名和密码后的登陆请求。

### ExceptionTranslationFilter：

​	处理过滤器链中抛出的任何AccessDeniedException和AuthenticationException。

### FilterSecurityInterceptor：

​	负责权限校验的过滤器。

### 默认的过滤器链

![image-20230508221731884](https://ytk-imgs.oss-rg-china-mainland.aliyuncs.com/image-20230508221731884.png)

### 入门案例认证流程

![SpringSecurity入门案例认证流程](https://ytk-imgs.oss-rg-china-mainland.aliyuncs.com/SpringSecurity%E5%85%A5%E9%97%A8%E6%A1%88%E4%BE%8B%E8%AE%A4%E8%AF%81%E6%B5%81%E7%A8%8B.png)

### Authencation接口：

​	他的实现类表示当前访问系统的用户，封装了用户相关信息。

### AuthencationManager接口：

​	定义了认证Authencation的方法

### UserDetailService接口：

​	加载用户特征数据的核心接口。里面定义了一个根据用户名查询用户信息的方法。

### UserDetail接口：

​	提供核心用户信息。通过UserDetailsService根据用户名获取处理用户信息要封装成UserDetails对象返回。然后将这些信息封装到Authencation对象中。

## 自定义登陆校验流程

![自定义登陆校验流程](https://ytk-imgs.oss-rg-china-mainland.aliyuncs.com/%E8%87%AA%E5%AE%9A%E4%B9%89%E7%99%BB%E9%99%86%E6%A0%A1%E9%AA%8C%E6%B5%81%E7%A8%8B.png)

### 思路分析

#### 登陆

	1. 自定义登陆接口 调用Providermanager的authencate方法进行认证
	
	认证通过生成jwt吧用户信息存入redis中
	
	2. 自定义UserDetailsService
	
	在这个实现查询数据库

#### 校验

 	1. 自定义jwt认证过滤器
 	 	1. 获取token
 	 	2. 解析token 从中获取到userid
 	 	3. 通过userid在redis中获取用户信息
 	 	4. 存入SecurityContextHolder

## 实现从数据库中查询用户

### 引入依赖

```xml
<!--mybatisPlus-->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.4.3</version>
</dependency>
<!--jdbc-->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.31</version>
</dependency>
```

### 配置Applicationyaml

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/数据库名?characterEncoding=utf-8&serverTimezone=UTC
    username: 用户名
    password: 密码
    driver-class-name: com.mysql.cj.jdbc.Driver
```

### 自定义实现UserDetailsService

```java
@Service
public class UserDetailServiceImpl implements UserDetailsService {

    @Resource
    private UserMapper userMapper;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {

        //查询用户信息
        LambdaQueryWrapper<User> queryWrapper = new LambdaQueryWrapper<>();
        queryWrapper.eq(User::getUsername,username);
        User user = userMapper.selectOne(queryWrapper);
        //如果没有查询到用户就抛出异常
        if(Objects.isNull(user)){
            //因为存在ExceptionTranslationFilter捕获异常
            throw new RuntimeException("用户名或密码错误");
        }


        //TODO 查询对应的权限信息

        //把数据封装成UserDetails返回
        return new LoginUser(user);
    }
}
```

### 自定义LoginUser

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class LoginUser implements UserDetails {

    private User user;

    //权限信息
    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return null;
    }

    //密码
    @Override
    public String getPassword() {
        return user.getPassword();
    }

    //用户名
    @Override
    public String getUsername() {
        return user.getUsername();
    }

    //账户是否未过期
    @Override
    public boolean isAccountNonExpired() {
        return true;
    }

    //账户是否锁定
    @Override
    public boolean isAccountNonLocked() {
        return true;
    }

    //凭证是否过期
    @Override
    public boolean isCredentialsNonExpired() {
        return true;
    }

    //是否启用
    @Override
    public boolean isEnabled() {
        return true;
    }
}
```

### 数据库

![image-20230509152814500](https://ytk-imgs.oss-rg-china-mainland.aliyuncs.com/image-20230509152814500.png)

密码如果是明文存储则需要加上`{noop}`

## 密码加密存储

​	实际项目中我们不会把密码明文存储在数据库中

​	默认使用的是PasswordEncoder要求数据库中的密码格式为：{id}password。他会根据id去判断密码的加密方式。但是我们一般不会采用这种方式。所以需要替换掉PasswordEncoder

​	我们一般使用SpringSecurity为我们提供的BCryptPasswordEncoder。

​	我们只需要把BCryptEncoder对象注入到Spring容器中，SpringSecurity就会使用PassworEncoder来进行密码校验。

​	我们可以定义一个SpringSecurity的配置类，SpringSecurity要求这个配置类继承WebSecurityConfigurationAdapter（已被弃用）**使用@EnableWebSecurity代替继承**

### BCryptPasswordEncoder会加盐（一样的值会加密后会对应不同的结果）

![image-20230509160034257](https://ytk-imgs.oss-rg-china-mainland.aliyuncs.com/image-20230509160034257.png)
