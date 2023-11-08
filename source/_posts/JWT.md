---
title: JWT
date: 2023-07-01 20:03:46
tags:
---

# JWT

## 设置相关变量

```java
//设置过期时间
private long time = 1000 * 60 * 60 * 1;
//设置签名
private String sign = "yangtukang";
```

## 创建JWT

```java
/**
 * 创建jwt
 */
@Test
public void creatJWT(){
    JwtBuilder jwtBuilder = Jwts.builder();
    String jwtToken = jwtBuilder
            //头部
            .setHeaderParam("typ","JWT")
            .setHeaderParam("alg","HS256")
            //Payload 载荷
            .claim("username","tom")
            .claim("role","admin")
            .setSubject("admin-test")
            //设置过期时间
            .setExpiration(new Date(System.currentTimeMillis()+time))
            //设置ID
            .setId(UUID.randomUUID().toString())
            //设置加密算法和签名
            .signWith(SignatureAlgorithm.HS256,sign)
            //使用“.”来连接一个完整的字符串
            .compact();
    System.out.println(jwtToken);
    //eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VybmFtZSI6InRvbSIsInJvbGUiOiJhZG1pbiIsInN1YiI6ImFkbWluLXRlc3QiLCJleHAiOjE2ODgyMTcxODYsImp0aSI6IjA5MWViNzY0LWEzYjgtNDgxYi1iZTUyLWJhODQxMTRkZjI4ZiJ9.cZG5bu6eN0A201vvIljZhhkD4j1Gm-XVialo0ZqyvLM
}
```

## 验证JWT

```java
/**
 * 验证jwt
 */
@Test
public void checkJWT(){
    String token = "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VybmFtZSI6InRvbSIsInJvbGUiOiJhZG1pbiIsInN1YiI6ImFkbWluLXRlc3QiLCJleHAiOjE2ODgyMTcxODYsImp0aSI6IjA5MWViNzY0LWEzYjgtNDgxYi1iZTUyLWJhODQxMTRkZjI4ZiJ9.cZG5bu6eN0A201vvIljZhhkD4j1Gm-XVialo0ZqyvLM";
    boolean result = Jwts.parser().isSigned(token);
    System.out.println(result);
}
```

## 解析JWT

```java
/**
* 解析JWT
*/
@Test
public void parseJWT(){
    String token = "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VybmFtZSI6InRvbSIsInJvbGUiOiJhZG1pbiIsInN1YiI6ImFkbWluLXRlc3QiLCJleHAiOjE2ODgyMTcxODYsImp0aSI6IjA5MWViNzY0LWEzYjgtNDgxYi1iZTUyLWJhODQxMTRkZjI4ZiJ9.cZG5bu6eN0A201vvIljZhhkD4j1Gm-XVialo0ZqyvLM";
    //获取JWT解析对象
    JwtParser jwtParser = Jwts.parser();
    jwtParser.setSigningKey(sign);
    //将JWT转换成Key Value的结构
    Jws<Claims> claimsJws = jwtParser.parseClaimsJws(token);
    System.out.println(claimsJws);
    //获取Jws兑现中的数据
    String username = claimsJws.getBody().get("username",String.class);
    System.out.println("username:"+username);
    String role = claimsJws.getBody().get("role", String.class);
    System.out.println("role:"+role);
}

//header={typ=JWT, alg=HS256},body={username=tom, role=admin, sub=admin-test, exp=1688217186, jti=091eb764-a3b8-481b-be52-ba84114df28f},signature=cZG5bu6eN0A201vvIljZhhkD4j1Gm-XVialo0ZqyvLM
//username:tom
//role:admin
```

