

# 初识SAAS

# 云服务的三种模式

## 1.1Iaas(基础设施即服务)

IaaS（Infrastructure as a Service），即基础设施即服务。提供给消费者的服务是对所有计算基础设施的利用，包
括处理CPU、内存、存储、网络和其它基本的计算资源，用户能够部署和运行任意软件，包括操作系统和应用程
序。消费者不管理或控制任何云计算基础设施，但能控制操作系统的选择、存储空间、部署的应用，也有可能获得
有限制的网络组件（例如路由器、防火墙、负载均衡器等）的控制

## 1.2Paas(平台即服务)

PaaS（Platform-as-a-Service），即平台即服务。提供给消费者的服务是把客户采用提供的开发语言和工具（例如
Java，python, .Net等）开发的或收购的应用程序部署到供应商的云计算基础设施上去。客户不需要管理或控制底
层的云基础设施，包括网络、服务器、操作系统、存储等，但客户能控制部署的应用程序，也可能控制运行应用程
序的托管环境配置

## 1.3Saas(软件即服务)

SaaS（Software-as-a-Service），即软件即服务。提供给消费者完整的软件解决方案，你可以从软件服务商处以租
用或购买等方式获取软件应用，组织用户即可通过 Internet 连接到该应用（通常使用 Web 浏览器）。所有基础结
构、中间件、应用软件和应用数据都位于服务提供商的数据中心内。服务提供商负责管理硬件和软件，并根据适当
的服务协议确保应用和数据的可用性和安全性。SaaS 让组织能够通过最低前期成本的应用快速建成投产。

# 系统架构

![image-20200607213844963](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200607213844963.png)

# 搭建父工程

![image-20200607215210041](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200607215210041.png)

![image-20200607215528663](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200607215528663.png)

pom.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.cbx</groupId>
    <artifactId>ihrm_parent</artifactId>
    <version>1.0-SNAPSHOT</version>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.5.RELEASE</version>
    </parent>
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>URF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
        <fastjson.version>1.2.47</fastjson.version>
    </properties>
   <dependencies>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-web</artifactId>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-logging</artifactId>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-test</artifactId>
           <scope>test</scope>
       </dependency>
       <dependency>
           <groupId>com.alibaba</groupId>
           <artifactId>fastjson</artifactId>
           <version>${fastjson.version}</version>
       </dependency>
       <dependency>
           <groupId>org.projectlombok</groupId>
           <artifactId>lombok</artifactId>
           <version>1.18.8</version>
       </dependency>
   </dependencies>
    <repositories>
        <repository>
            <id>spring-snapshots</id>
            <name>Spring Snapshots</name>
            <url>https://repo.spring.io/snapshot</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>

    <pluginRepositories>
        <pluginRepository>
            <id>spring-snapshots</id>
            <name>Spring Snapshots</name>
            <url>https://repo.spring.io/snapshot</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </pluginRepository>
        <pluginRepository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </pluginRepository>
    </pluginRepositories>
    <build>
        <plugins>
            <!--编译插件-->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.1</version>
                <configuration>
                    <source>${java.version}</source>
                    <target>${java.version}</target>
                </configuration>
            </plugin>

            <!--单元测试插件-->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>2.12.4</version>
                <configuration>
                    <skipTests>true</skipTests>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

# 构建公共子模块 ihrm_common

![image-20200607223054633](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200607223054633.png)

# 分布式id生成器

![还有](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200608131415080.png)

## 使用雪花算法生成id

![image-20200608132023313](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200608132023313.png)

# 搭建公共子模块ihrm_common_model

![image-20200608132249815](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200608132249815.png)

## 引入坐标

```
  <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>com.cbx</groupId>
            <artifactId>ihrm_common</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
```

# 搭建模块 ihrm_company

![image-20200608135428075](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200608135428075.png)

## 引入坐标

```
<dependencies>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>
    <dependency>
        <groupId>com.cbx</groupId>
        <artifactId>ihrm_common</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
    <dependency>
        <groupId>com.cbx</groupId>
        <artifactId>ihrm_common_model</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
</dependencies>
```

## 配置文件 application.yml

```
#服务配置
server:
  port: 9001
#spring配置
spring:
  #1.应用配置
  application:
    name: ihrm-company #指定服务名
  #2.数据库连接池
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/ihrm?useUnicode=true&characterEncoding=utf8
    username: root
    password: root
  redis:
    host: 127.0.0.1
    port: 6379
  #3.JPA
  jpa:
    database: MySQL
    show-sql: true
    open-in-view: true
```

## 配置启动类CompanyApplication.java

```
package com.cbx.company;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.domain.EntityScan;

//配置springboot的包扫描
@SpringBootApplication(scanBasePackages = "com.cbx.company")
//配置jpa的注解扫描
@EntityScan(value = "com.cbx.domain.company")
public class CompanyApplication {
//    配置启动方法
   public static void main(String[] args) {
       SpringApplication.run(CompanyApplication.class,args); 
}
}
```

# 多租户的sass平台数据库解决方案

![image-20200608170313198](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200608170313198.png)

## 独立数据库

![image-20200608170617679](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200608170617679.png)

## 共享数据库、独立Schema

![image-20200608171633348](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200608171633348.png)

![image-20200608171542944](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200608171542944.png)

## 共享数据库，共享数据表

节省了成本  ，加大了开发量

![image-20200608204236969](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200608204236969.png)

# RBAC表结构设计

权限再分类型： 1.菜单   2.页面元素(按钮。。。等)   3. api接口

![image-20200609170731436](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200609170731436.png)

# 搭建微服务模块

![image-20200609171836493](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200609171836493.png)

# 用户权限表结构

![image-20200610092753889](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200610092753889.png)

# Jwt

## 引入依赖

```
  <!-- https://mvnrepository.com/artifact/io.jsonwebtoken/jjwt -->
        <!--引入jwt依赖-->
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt</artifactId>
            <version>0.6.0</version>
        </dependency>
```

## 生成jwt token

```

public class createjwtTest {
    public static void main(String[] args) {
        JwtBuilder jwtBuilder = Jwts.builder().setId("88").setSubject("cbx")
                .setIssuedAt(new Date())
                .signWith(SignatureAlgorithm.HS256, "ihrm")
//                设置自定义的内容
                .claim("companyId","ccc")
                .claim("companyName","ddd")
                ;
        String compact = jwtBuilder.compact();
        System.out.println(compact);
    }
}
```

## 解析jwt token 

```

public class parseJwtTest {
    public static void main(String[] args) {
        String    token="eyJhbGciOiJIUzI1NiJ9.eyJqdGkiOiI4OCIsInN1YiI6ImNieCIsImlhdCI6MTU5MTc3MTEwMH0.-eqDKb7WiWlHj6oDSpk_tT2XaOMyMOMn6HornMnhjO0";
        Claims ihrm = Jwts.parser().setSigningKey("ihrm").parseClaimsJws(token).getBody();
//        88
//        cbx
//        Wed Jun 10 14:38:20 CST 2020
//        {jti=88, sub=cbx, iat=1591771100}
        System.out.println(ihrm.getId());
        System.out.println(ihrm.getSubject());
        System.out.println(ihrm.getIssuedAt());
        System.out.println(ihrm);
        //获取自定义的内容
        System.out.println(ihrm.get("companyId"));
        System.out.println(ihrm.get("companyName"));
    }
}
```

## 定义jwt工具类

```
package com.cbx.common.utils;

import io.jsonwebtoken.Claims;
import io.jsonwebtoken.JwtBuilder;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;
import lombok.Data;
import org.springframework.boot.context.properties.ConfigurationProperties;

import java.util.Date;
import java.util.Map;
@Data
@ConfigurationProperties("jwt.config")
public class JwtUtils {
//    签名私钥
    private String key;
//    签名的失效时间
    private Long ttl;

    /**
     * 设置认证token
     * id ：登录用户的id
     * subject:登录用户的subject
     */
    public String createJwt(String id, String name, Map<String,Object> map){
//        1.设置失效时间
        long now=System.currentTimeMillis();  //当前毫秒
        long exp=now + ttl;
//        2.创建jwtBuilder
        JwtBuilder jwtBuilder = Jwts.builder().setId(id).setSubject(name)
                .setIssuedAt(new Date())
                .signWith(SignatureAlgorithm.HS256,key);
//        根据map设置claims
        jwtBuilder.setClaims(map);
//        设置失效时间
        jwtBuilder.setExpiration(new Date(exp));
        String token = jwtBuilder.compact();
         return token;
    }

    /**
     * 解析token字符串获取claims
     */
    public Claims parseJwt(String token){
        Claims claims = Jwts.parser().setSigningKey(key).parseClaimsJws(token).getBody();
        return claims;
    }

    }


```

# 配置JWT token登录的拦截器

## 编写拦截器类

```
package com.cbx.common.interceptor;

import com.cbx.common.entity.ResultCode;
import com.cbx.common.exception.CommonException;
import com.cbx.common.utils.JwtUtils;
import io.jsonwebtoken.Claims;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import org.springframework.util.StringUtils;
import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.handler.HandlerInterceptorAdapter;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * 自定义拦截器
 */

/**
 * preHandle ：在进入方法之前执行的内容
 * postHandle:在进入方法之后执行的内容
 * afterCompletion:响应结束之前珍执行的内容
 * 1.简化token数据的代码编写
 *     统一的用户权限验证(是否登录)
 * 2.判断用户是否具有当前访问接口的权限
 *
 */
@Component
public class JwtInterceptor extends HandlerInterceptorAdapter {
    @Autowired
    private JwtUtils jwtUtils;
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        /**
         *1.通过拦截器获取token数据
         *2.从token中解析获取claims
         *3.将claims绑定到request域中
         */
        String authorization = request.getHeader("Authorization");
//        判断请求头信息是否为空
        if(!StringUtils.isEmpty(authorization)&&authorization.startsWith("Bearer")){
            String token = authorization.replace("Bearer ", "");
            Claims claims = jwtUtils.parseJwt(token);
            if (claims!=null){
                request.setAttribute("user_claims",claims);
                return true;
            }
        }
        throw new CommonException(ResultCode.UNAUTHENTICATED);
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        super.postHandle(request, response, handler, modelAndView);
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        super.afterCompletion(request, response, handler, ex);
    }
}

```

## 配置拦截器类

```
package com.cbx;

import com.cbx.common.interceptor.JwtInterceptor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurationSupport;

@Configuration
public class SystemConfig extends WebMvcConfigurationSupport {
    @Autowired
    private JwtInterceptor jwtInterceptor;

    @Override
    protected void addInterceptors(InterceptorRegistry registry) {
//          添加自定义的拦截器
              registry.addInterceptor(jwtInterceptor)
              .addPathPatterns("/**")    //指定拦截器的url
              .excludePathPatterns("/sys/login","/frame/register/**");  //指定不拦截的地址
    }
}
```

# 基于jwt的Api权限验证的解决方案

![image-20200612095110557](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200612095110557.png)





# shiro

基本配置可以参照

F:\Project\springbootProject\employee_kuangshen\springboot_shiro\shiro-springboot

## 开启shiro注解的支持

```
   @Bean
     public AuthorizationAttributeSourceAdvisor authorizationAttributeSourceAdvisor(SecurityManager securityManager){
         AuthorizationAttributeSourceAdvisor advisor=new AuthorizationAttributeSourceAdvisor();
         advisor.setSecurityManager(securityManager);
         return advisor;
     }
```

## shiro中的会话管理

**默认就是使用的第二个，存在httpSession中**

![image-20200612160920928](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200612160920928.png)

## 分布式下sessionId的使用

![image-20200612161741829](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200612161741829.png)

## shiro整合redis统一会话管理

![image-20200612161947201](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200612161947201.png)

### 构建环境

```
<dependency>
			<groupId>org.crazycake</groupId>
			<artifactId>shiro-redis</artifactId>
			<version>3.0.0</version>
		</dependency>
```

### application.yml

```
spring:
 redis:
    host: 127.0.0.1
    port: 6379
```

### 自定义SessionManager

```
package com.cbx.shirospringboot.session;

import org.apache.shiro.web.servlet.ShiroHttpServletRequest;
import org.apache.shiro.web.session.mgt.DefaultWebSessionManager;
import org.apache.shiro.web.util.WebUtils;
import org.springframework.util.StringUtils;

import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import java.io.Serializable;

/**
 * 自定义sessionManager
 */
public class CustomSessionManager extends DefaultWebSessionManager {
    /**
     * 请求头 Authorization sessionid
     * @param request
     * @param response
     * @return
     */
    @Override
    protected Serializable getSessionId(ServletRequest request, ServletResponse response) {
//        获取请求头的信息
        String id= WebUtils.toHttp(request).getHeader("Authorization");
        if (StringUtils.isEmpty(id)){
//            没有sessionid就生成一个返回
            return super.getSessionId(request,response);
        }else {
//            返回sessionid
            request.setAttribute(ShiroHttpServletRequest.REFERENCED_SESSION_ID_SOURCE,"header");
            request.setAttribute(ShiroHttpServletRequest.REFERENCED_SESSION_ID,id);
            request.setAttribute(ShiroHttpServletRequest.REFERENCED_SESSION_ID_IS_VALID,Boolean.TRUE);
            return id;
        }
    }
}

```

### 在ShiroConfiguration里进行配置

```
/**
 * 会话管理
 * 1.redis的控制器，操作redis
 * 2.sessionDao
 * 3.会话管理器
 * 4.缓存管理器
 */
@Value("${spring.redis.host}")
private String host;
@Value("${spring.redis.port}")
private String port;
  //1.redis
 public RedisManager redisManager(){
     RedisManager redisManager=new RedisManager();
     redisManager.setHost(host);
     redisManager.setPort(Integer.valueOf(port));
     return redisManager;
 }

 //2.sessionDao
public RedisSessionDAO redisSessionDAO(){
     RedisSessionDAO redisSessionDAO=new RedisSessionDAO();
     redisSessionDAO.setRedisManager(redisManager());
     return redisSessionDAO;
}
  //3.会话管理器
public DefaultWebSessionManager defaultWebSessionManager(){
    CustomSessionManager manager=new CustomSessionManager();
    manager.setSessionDAO(redisSessionDAO());
    return  manager;
}

  //4.缓存管理器
public RedisCacheManager redisCacheManager(){
     RedisCacheManager redisCacheManager=new RedisCacheManager();
     redisCacheManager.setRedisManager(redisManager());
     return redisCacheManager;
}
```

**然后在安全管理器里必须声明**

![image-20200612165640176](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200612165640176.png)

**然后User类必须实现一个接口，会自动把数据放入到redis中**

![image-20200612165824808](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200612165824808.png)

## jwt跟shiro认证授权对比

存到redis  key==>sessionId value==>安全数据

![image-20200612171647458](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200612171647458.png)

