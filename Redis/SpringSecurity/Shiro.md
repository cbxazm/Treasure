测试案列

```
import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.*;
import org.apache.shiro.config.IniSecurityManagerFactory;
import org.apache.shiro.mgt.SecurityManager;
import org.apache.shiro.session.Session;
import org.apache.shiro.subject.Subject;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class QuickStart {
    private static final transient Logger log = LoggerFactory.getLogger(QuickStart.class);

    public static void main(String[] args) {
        log.info("My First Apache Shiro Application");

        IniSecurityManagerFactory factory = new IniSecurityManagerFactory("classpath:shiro.ini");
        SecurityManager securityManager = factory.getInstance();
        SecurityUtils.setSecurityManager(securityManager);

        // get the currently executing user:
//        获取当前的用户对象
        Subject currentUser = SecurityUtils.getSubject();

        // Do some stuff with a Session (no need for a web or EJB container!!!)
//        通过当前对象拿到session
        Session session = currentUser.getSession();
        session.setAttribute("someKey", "aValue");
        String value = (String) session.getAttribute("someKey");
        if (value.equals("aValue")) {
            log.info("Retrieved the correct value! [" + value + "]");
        }

        // let's login the current user so we can check against roles and permissions:
        //判断当前用户是否被认证
        if (!currentUser.isAuthenticated()) {
//            Token:令牌
            UsernamePasswordToken token = new UsernamePasswordToken("lonestarr", "vespa");
            token.setRememberMe(true);//设置记住我
            try {
                currentUser.login(token);
            } catch (UnknownAccountException uae) {
                log.info("There is no user with username of " + token.getPrincipal());
            } catch (IncorrectCredentialsException ice) {
                log.info("Password for account " + token.getPrincipal() + " was incorrect!");
            } catch (LockedAccountException lae) {
                log.info("The account for username " + token.getPrincipal() + " is locked.  " +
                        "Please contact your administrator to unlock it.");
            }
            // ... catch more exceptions here (maybe custom ones specific to your application?
            catch (AuthenticationException ae) {
                //unexpected condition?  error?
            }
        }

        //say who they are:
        //print their identifying principal (in this case, a username):
        log.info("User [" + currentUser.getPrincipal() + "] logged in successfully.");

        //test a role:
        //测试角色
        if (currentUser.hasRole("schwartz")) {
            log.info("May the Schwartz be with you!");
        } else {
            log.info("Hello, mere mortal.");
        }

        //test a typed permission (not instance-level)
        if (currentUser.isPermitted("lightsaber:wield")) {
            log.info("You may use a lightsaber ring.  Use it wisely.");
        } else {
            log.info("Sorry, lightsaber rings are for schwartz masters only.");
        }

        //a (very powerful) Instance Level permission:
        if (currentUser.isPermitted("winnebago:drive:eagle5")) {
            log.info("You are permitted to 'drive' the winnebago with license plate (id) 'eagle5'.  " +
                    "Here are the keys - have fun!");
        } else {
            log.info("Sorry, you aren't allowed to drive the 'eagle5' winnebago!");
        }
      //注销
        //all done - log out!
        currentUser.logout();
        System.exit(0);
    }
}

```

## 核心

![image-20200606152749267](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200606152749267.png)

用户   Subject

管理所有用户 SecurityManager

连接数据  Realm

```
//        获取当前的用户对象
        Subject currentUser = SecurityUtils.getSubject();
//        通过当前对象拿到session
        Session session = currentUser.getSession();
//判断当前用户是否被认证     
        currentUser.isAuthenticated()
//测试角色
        currentUser.hasRole("schwartz")
//测试权限
        currentUser.isPermitted("lightsaber:wield")
//退出
          currentUser.logout();
```

# Springboot集成shiro

## 引入依赖

```
<dependency>
 //整合thymeleaf
			<groupId>com.github.theborakompanioni</groupId>
			<artifactId>thymeleaf-extras-shiro</artifactId>
			<version>2.0.0</version>
		</dependency>
<dependency>
  //整合springboot
			<groupId>org.apache.shiro</groupId>
			<artifactId>shiro-spring</artifactId>
			<version>1.5.1</version>
		</dependency>
```

## 编写配置类

```
package com.cbx.shirospringboot.config;

import at.pollux.thymeleaf.shiro.dialect.ShiroDialect;
import org.apache.shiro.spring.web.ShiroFilterFactoryBean;
import org.apache.shiro.web.mgt.DefaultWebSecurityManager;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.util.HashMap;
import java.util.LinkedHashMap;
import java.util.Map;

@Configuration
public class ShiroConfig {
//
    /**
     * 第三步
     * shiroFilterFactoryBean
     */
    @Bean
    public ShiroFilterFactoryBean getshiroFilterBean(@Qualifier("getDefaultWebSecurityManager") DefaultWebSecurityManager defaultWebSecurityManager){
        ShiroFilterFactoryBean shiroFilterFactoryBean=new ShiroFilterFactoryBean();
//        设置安全管理器
        shiroFilterFactoryBean.setSecurityManager(defaultWebSecurityManager);
        /**
         * 添加shiro的内置过滤器
         * anon:不需认证
         * authc：必须认证了才能访问
         * user:必须有记住我才能访问
         * perms:拥有对某个资源的权限才能访问
         * role:拥有某个角色权限才能访问
         */
        Map<String,String> filterChainDefinitionMap=new LinkedHashMap<>();
        /**
         * 授权操作,正常情况下，没有授权会跳转到未授权的页面
         */
        filterChainDefinitionMap.put("/user/add","perms[user:add]");
//        filterChainDefinitionMap.put("/user/add","anon");
        filterChainDefinitionMap.put("/user/update","authc");
        shiroFilterFactoryBean.setFilterChainDefinitionMap(filterChainDefinitionMap);

        /**
         * 如果没有权限
         * 就会去登录的请求页面
         */
        shiroFilterFactoryBean.setLoginUrl("/toLogin");
        /***
         * 设置未授权的请求
         */
        shiroFilterFactoryBean.setUnauthorizedUrl("/noauth");
        return shiroFilterFactoryBean;
    }
//
    /**
     * 第二步
     * DefaultWebSecurityManager
     * @Qualifier可以获取bean，参数为方法名
     * 或者在@Bean("指定名字“)，后面的Qualifier就用那个指定的名字
     */
    @Bean
    public DefaultWebSecurityManager getDefaultWebSecurityManager(@Qualifier("userRealm") UserRealm userRealm){
        DefaultWebSecurityManager securityManager=new DefaultWebSecurityManager();
        securityManager.setRealm(userRealm);
        return securityManager;
    }

    /**
     * 第一步
     * //    创建Realm对象，需要自定义
     * @return
     */

     @Bean
     public UserRealm userRealm(){
         return new UserRealm();
     }
//  整合shiroDialect：用来整合Shiro thymeleaf
     @Bean
     public ShiroDialect getShiroDialect(){
        return new ShiroDialect();
     }
}

```

```
	package com.cbx.shirospringboot.config;

import com.cbx.shirospringboot.pojo.User;
import com.cbx.shirospringboot.service.UserService;
import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.*;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.authz.SimpleAuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.session.Session;
import org.apache.shiro.subject.PrincipalCollection;
import org.apache.shiro.subject.Subject;
import org.springframework.beans.factory.annotation.Autowired;

//自定义的UserRealm
public class UserRealm extends AuthorizingRealm {
    @Autowired
    private UserService userService;
    /**
     * 授权
     * @param principalCollection
     * @return
     */
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        System.out.println("执行了==>授权doGetAuthorizationInfo");
        SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();
//        info.addStringPermission("user:add");
        //拿到当前登录的这个对象
        Subject subject = SecurityUtils.getSubject();
        User currentUser = (User) subject.getPrincipal();
        info.addStringPermission(currentUser.getPerms());
//
    return info;
    }

    /**
     * 认证
     * @param authenticationToken
     * @return
     * @throws AuthenticationException
     */
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        System.out.println("执行了==>认证doGetAuthenticationInfo");
       //用户名，密码 从数据库中取
//        String name="root";
//        String password="123";
        UsernamePasswordToken usernamePasswordToken = (UsernamePasswordToken) authenticationToken;
        User user = userService.queryUserByName(usernamePasswordToken.getUsername());
//        if (!usernamePasswordToken.getUsername().equals(name)){
//            return null; //抛出异常 ，UnknownAccountException
//        }
        if (user==null){
            return null; //抛出异常 ，UnknownAccountException
        }
        //密码认证,shiro来做
        /**
         * 第一个参数放了user 上面的授权方法就可以通过subject.getPrincipal()来获取
         */

        Subject subject = SecurityUtils.getSubject();
        Session session = subject.getSession();
        session.setAttribute("loginUser",user);
        return new SimpleAuthenticationInfo(user,user.getPassword(),"");
    }
}
	
```

