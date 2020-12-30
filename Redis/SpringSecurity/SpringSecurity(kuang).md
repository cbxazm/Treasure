# 简介

![image-20200524195451560](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200524195451560.png)

# 引入thymeleaf

```
	<!--引入thymeleaf-->
		<dependency>
			<groupId>org.thymeleaf</groupId>
			<artifactId>thymeleaf-spring5</artifactId>
		</dependency>
		<dependency>
			<groupId>org.thymeleaf.extras</groupId>
			<artifactId>thymeleaf-extras-java8time</artifactId>
		</dependency>
```

## 导入springSecurity

```
	<!--导入springSecurity-->
		<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-security -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-security</artifactId>
		</dependency>
```

## thymeleaf整合SpringSecurity

```
	<!--thymeleaf与springSecurity的整合-->
		<!-- https://mvnrepository.com/artifact/org.thymeleaf.extras/thymeleaf-extras-springsecurity4 -->
		<dependency>
			<groupId>org.thymeleaf.extras</groupId>
			<artifactId>thymeleaf-extras-springsecurity4</artifactId>
			<version>3.0.4.RELEASE</version>
		</dependency>
```

