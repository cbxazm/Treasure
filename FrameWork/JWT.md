![image-20200831152827294](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200831152827294.png)

# JWT的结构

![image-20200831154424184](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200831154424184.png)

![image-20200831154438635](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200831154438635.png)

# 使用JWT

## 引入依赖

```
<!-- https://mvnrepository.com/artifact/com.auth0/java-jwt -->
<dependency>
    <groupId>com.auth0</groupId>
    <artifactId>java-jwt</artifactId>
    <version>3.4.0</version>
</dependency>

```

## 生成token

```
		Calendar calendar=Calendar.getInstance();
		calendar.add(Calendar.SECOND,20); //设置20秒后的时间
		HashMap<String,Object> map=new HashMap<>(); //存放header(有默认值)
//			base64enc({
//					"alg":"HS256",
//		            "typ":"JWT"
//			})
		String token = JWT.create()
				.withHeader(map)   //header部分 可以默认不写
				.withClaim("userId", 21)
				.withClaim("username", "ccc")  //payload
				.withExpiresAt(calendar.getTime()) //指定过期时间
				.sign(Algorithm.HMAC256("abcd"));//签名
		System.out.println(token);
```

## 验证token

```
		System.out.println(token);
		JWTVerifier jwtVerifier = JWT.require(Algorithm.HMAC256("abcd")).build();
		DecodedJWT verify = jwtVerifier.verify("eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE1OTg4NjIwMzYsInVzZXJJZCI6MjEsInVzZXJuYW1lIjoiY2NjIn0.8H_oG5hdrukHq66n7hHRRYgi_C81sfpwbQnRIRuU33w");
	
//		System.out.println(verify.getClaim("userId").asInt());
//		System.out.println(verify.getClaim("username").asString());
		System.out.println(verify.getClaims().get("userId").asInt());
		System.out.println(verify.getClaims().get("username").asString());
```

## 封装Jwt的工具类

```
package com.cbx.springbootjwt.utils;

import com.auth0.jwt.JWT;
import com.auth0.jwt.JWTCreator;
import com.auth0.jwt.JWTVerifier;
import com.auth0.jwt.algorithms.Algorithm;
import com.auth0.jwt.interfaces.DecodedJWT;

import java.util.Calendar;
import java.util.HashMap;
import java.util.Map;

/**
 * @Author:cbx
 * @Date:2020/08/31/16:22
 */
public class JWTUtils {
    /**
     * 生成token  header.payload.sign
     */
    private static final String SIGN="abcdef";
    public static String getToken(Map<String,String> map){
        Calendar calendar=Calendar.getInstance();
        calendar.add(Calendar.DATE,7); //设置7天过期
        JWTCreator.Builder builder = JWT.create();
        map.forEach((k,v)->{
             builder.withClaim(k,v);
        });
        builder.withExpiresAt(calendar.getTime());
        String token = builder.sign(Algorithm.HMAC256(SIGN));
        System.out.println(token);
        return token;
    }
    /**
     * 验证token
     */
    public static DecodedJWT verify(String token){
        JWTVerifier jwtVerifier = JWT.require(Algorithm.HMAC256(SIGN)).build();
        DecodedJWT verify = jwtVerifier.verify(token);
       return verify;
    }


}

```

## 整合springboot

```
<dependencies>
		<!-- https://mvnrepository.com/artifact/com.auth0/java-jwt -->
		<dependency>
			<groupId>com.auth0</groupId>
			<artifactId>java-jwt</artifactId>
			<version>3.4.0</version>
		</dependency>
		<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
			<version>2.1.0</version>
		</dependency>
		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			<version>1.18.0</version>
		</dependency>
		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>druid</artifactId>
			<version>1.1.23</version>
		</dependency>
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<version>5.1.8</version>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
			<exclusions>
				<exclusion>
					<groupId>org.junit.vintage</groupId>
					<artifactId>junit-vintage-engine</artifactId>
				</exclusion>
			</exclusions>
		</dependency>
	</dependencies>
```

## 整合shiro

