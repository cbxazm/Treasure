

官网：https://mp.baomidou.com/

# QuickStart

## 添加依赖

```
<dependency>
			<groupId>com.baomidou</groupId>
			<artifactId>mybatis-plus-boot-starter</artifactId>
			<version>3.0.5</version>
		</dependency>
```

## 编写配置

```
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/mybatisplus?useSSL=false&useUnicode=true&characterEncoding=utf-8
#如果使用mysql8一定要再加上一个时区的配置 serverTimeZone=GMT
spring.datasource.username=root
spring.datasource.password=123

```

## 编写mapper

```
package com.cbx.mybatisplus.mapper;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.cbx.mybatisplus.pojo.User;
import org.apache.ibatis.annotations.Mapper;
import org.springframework.stereotype.Repository;

@Mapper
public interface UserMapper extends BaseMapper<User> {

}

```

## 编写pojo

```
package com.cbx.mybatisplus.pojo;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private int id;
    private int age;
    private String name;
    private String email;
}
	
```



## 启动类扫描

```
package com.cbx.mybatisplus;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
//扫描mappera文件夹

@SpringBootApplication
@MapperScan("com.cbx.mybatisplus.mapper")
public class MybatisplusApplication {

	public static void main(String[] args) {
		SpringApplication.run(MybatisplusApplication.class, args);
	}

}
	
```



## 编写测试类

```
package com.cbx.mybatisplus;
	
	import com.cbx.mybatisplus.mapper.UserMapper;
	import com.cbx.mybatisplus.pojo.User;
	import org.junit.jupiter.api.Test;
	import org.junit.runner.RunWith;
	import org.springframework.beans.factory.annotation.Autowired;
	import org.springframework.boot.test.context.SpringBootTest;
	import org.springframework.test.context.junit4.SpringRunner;
	
	import java.util.List;
	
	@SpringBootTest
	class MybatisplusApplicationTests {
	//   继承了BaseMapper,所有的方法都来自父类
	//	可以自己进行拓展方法
		@Autowired
		private UserMapper userMapper;
		@Test
		void contextLoads() {
			List<User> users = userMapper.selectList(null);
			System.out.println(users);
		}
	
	}
	
```

## 配置日志

我们配置的sql现在是不可见的，我们希望知道它是怎么执行的，所以我们必须看日志

![image-20200601142756395](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200601142756395.png)

配置完日志，可以看到自动生成的sql

# 基本操作扩展

## 插入操作

```
//		测试插入
		@Test
		void insert(){
			User user=new User();
			user.setAge(18);
			user.setName("zhangsan");
			user.setEmail("111@aa.com");
			userMapper.insert(user);
			
		}
```

![image-20200601143912421](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200601143912421.png)

### 主键生成策略

分布式系统唯一生成id: https://www.cnblogs.com/haoxinyue/p/5208136.html

雪花算法:

snowflake是Twitter开源的分布式ID生成算法，结果是一个long型的ID。其核心思想是：使用41bit作为毫秒数，10bit作为机器的ID（5个bit是数据中心，5个bit的机器ID），12bit作为毫秒内的流水号（意味着每个节点在每毫秒可以产生 4096 个 ID），最后还有一个符号位，永远是0。具体实现的代码可以参看https://github.com/twitter/snowflake。

### 主键自增

1.在实体类字段上 

```
@TableId(type = IdType.AUTO)
private Long id;
```

2.数据库的字段一定配上自增（不然会报错）

![image-20200601144731663](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200601144731663.png)

3.再次插入就会自增了

![image-20200601144857213](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200601144857213.png)

### IdType解释

```
public enum IdType {
    AUTO(0), //数据库Id自增
    NONE(1), //未设置主键
    INPUT(2), //手动输入
    ASSIGN_ID(3), 
    ASSIGN_UUID(4),
    /** @deprecated */
    @Deprecated
    ID_WORKER(3), //默认的全局唯一id
    /** @deprecated */
    @Deprecated
    ID_WORKER_STR(3), //全局默认id的字符串表示法
    /** @deprecated */
    @Deprecated
    UUID(4);
    }
```

## 更新操作

```
//		测试更新
		@Test
		void update(){
			User user=new User();
//			user.setAge(1);
			user.setId(1L);
			user.setAge(18);
			user.setName("lisi");
			user.setEmail("111@aa.com");
			userMapper.updateById(user);

		}
```

## 自动填充

创建时间，修改时间！这些操作一般都是自动化完成的，不希望是手动配置

阿里开发手册:gmt_created,gmt_modified几乎要配置在所有的表上

### 方式1 数据库级别（工作中不允许修改数据库）

在表中新增字段 create_time ,update_time

![image-20200601151901178](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200601151901178.png)

更新时，会自动更改那个update_time

### 方式2 代码级别

数据库字段没有默认值和更新操作

为字段加上填充内容

```
 @TableField(fill = FieldFill.INSERT)
    private Date createTime;
    @TableField(fill=FieldFill.INSERT_UPDATE)
    private Date updateTime;
}
```

#### 编写处理器处理注解

```
package com.cbx.mybatisplus.handler;

import com.baomidou.mybatisplus.core.handlers.MetaObjectHandler;
import lombok.extern.slf4j.Slf4j;
import org.apache.ibatis.reflection.MetaObject;
import org.springframework.stereotype.Component;

import java.util.Date;

@Component
@Slf4j
public class MyMetaObjectHandler implements MetaObjectHandler {
//    插入式的填充策略
    @Override
    public void insertFill(MetaObject metaObject) {
        log.info("start insert fill ...");
         this.setFieldValByName("createTime",new Date(),metaObject);
         this.setFieldValByName("updateTime",new Date(),metaObject);
    }
//更新时的填充策略
    @Override
    public void updateFill(MetaObject metaObject) {
        log.info("start  update fill ...");
        this.setFieldValByName("updateTime",new Date(),metaObject);
    }
}
```

插入的时候(会自动填充)

![image-20200601154106313](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200601154106313.png)

## 乐观锁操作

- 取出记录时，获取version

- 更新时，带上这个version

- 执行更新时，set version=new version where version =oldversion

- 如果version不对，更新失败

  ```
  --A线程
  update user set name="kuangshen",version=version +1 where id =2 and version=1
  --B线程 抢先完成，这个时候version=2,会导致A修改失败
  update user set name ="zhangsan" ，version=version+1 where id =2 and version=1
  ```

  

### 增加version字段

默认值为1

![image-20200601160818576](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200601160818576.png)

```
@Version //乐观锁的注解
private Integer version;
```

### 注册组件

```
package com.cbx.mybatisplus.config;

import com.baomidou.mybatisplus.extension.plugins.OptimisticLockerInterceptor;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.transaction.annotation.EnableTransactionManagement;
 //可以把启动类的扫描放在这里
@Configuration
@MapperScan("com.cbx.mybatisplus.mapper")
@EnableTransactionManagement
public class MybatisPlusConfig {
//    注册乐观锁插件
    @Bean
     public OptimisticLockerInterceptor optimisticLockerInterceptor(){
          return new OptimisticLockerInterceptor();
     }
}
```

## 测试

### 测试成功

```
//     测试乐观锁成功
      @Test
       void testLockSucc(){
          User user = userMapper.selectById(1L);
          user.setName("wangwang");
          user.setEmail("2222@cn.com");
          userMapper.updateById(user); 
       }
```

version 会改变，增加了1

![image-20200601162124702](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200601162124702.png)

### 测试失败

```
    //    测试乐观锁失败
      @Test
      void testLockFail(){
         User user = userMapper.selectById(1L);
         user.setName("wangwang");
         user.setEmail("2222@cn.com");
         User user2 = userMapper.selectById(1L);
         user.setName("miaomiao");
         user.setEmail("3333@cn.com");
         userMapper.updateById(user2);  //会把version+1,与之前获取到的version不一样，下面的update语句也就会失效
//       还没有更新之前被修改version
         userMapper.updateById(user);
      }
```

## 查询操作

### 批量查询

```
//     测试批量查询
      @Test
      void testSelectByBatchId(){
         List<User> users = userMapper.selectBatchIds(Arrays.asList(1, 2, 3));
         System.out.println(users);
      }
```

### 条件查询

```
//     条件查询
      @Test
      void testSelectByMap(){
         Map<String,Object> map=new HashMap<>();
         map.put("name","wangwang");
         map.put("age",100);
         List<User> users = userMapper.selectByMap(map);
         System.out.println(users);
      }
```

### 分页查询

#### 配置拦截器组件

```

@Configuration
@MapperScan("com.cbx.mybatisplus.mapper")
@EnableTransactionManagement
public class MybatisPlusConfig {
    //    注册乐观锁插件
    @Bean
    public OptimisticLockerInterceptor optimisticLockerInterceptor() {
        return new OptimisticLockerInterceptor();
    }

    @Bean
    public PaginationInterceptor paginationInterceptor() {
        PaginationInterceptor paginationInterceptor = new PaginationInterceptor();
        // 设置请求的页面大于最大页后操作， true调回到首页，false 继续请求  默认false
        // paginationInterceptor.setOverflow(false);
        // 设置最大单页限制数量，默认 500 条，-1 不受限制
        // paginationInterceptor.setLimit(500);
        // 开启 count 的 join 优化,只针对部分 left join
        paginationInterceptor.setCountSqlParser(new JsqlParserCountOptimize(true));
        return paginationInterceptor;
    }
}

```

```
//     测试分页查询
      @Test
      void testPage(){
//       参数1:当前页
//       参数2：页面显示的数据数量
         Page<User> page=new Page<>(1,5);
         Page<User> page1 = userMapper.selectPage(page,null);
         List<User> records = page1.getRecords();
         System.out.println(records);
      }
```

## 删除操作

### 基本删除操作

```
  //id删除
      void deleteById(){
         userMapper.deleteById(1L);
      }
//    批量删除
      void deleteBatchId(){
         userMapper.deleteBatchIds(Arrays.asList(1,2,3));
      }
//    按条件删除
      void deleteByMap(){
         Map<String,Object> map=new HashMap<>();
         map.put("name","zahngsan");
         map.put("age",15);
         userMapper.deleteByMap(map);
      }
```

### 逻辑删除

物理删除:从数据库中直接删除

逻辑删除:在数据库中没有删除，而是通过一个变量来让他失效 delete=0=>delete=1

管理员可以查看被删除的记录！！就是这个原理，防止数据的丢失

#### 在数据表中增加deleted字段

默认值是0

![image-20200601200423657](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200601200423657.png)



#### 实体类表中增加属性

```
@TableLogic  //逻辑删除字段
private Integer deleted;
```

#### 配置(3.3.1开始不需要配置组件注册bean)

```

mybatis-plus.global-config.db-config.logic-delete-value=1 #默认删除为1
mybatis-plus.global-config.db-config.logic-not-delete-value=0  #0代表未删除
```

#### 编写测试类

```
//id删除
        @Test
   void deleteByIdaa(){
      userMapper.deleteById(1L);
   }
```

不会删除掉，会在数据库的deleted字段改为1，表示已经删除

删除的时候其实走的是更新操作，并不是删除操作

![image-20200601202847553](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200601202847553.png)

![image-20200601202701297](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200601202701297.png)

# 条件构造器

Wrapper

我们写复杂的sql就可以使用它来代替

## 测试1

```
    @Test
    void run(){
//        参数是Wrapper
//        查询name不为空的用户，并且邮箱不为空的用户，年龄大于等于25
        QueryWrapper<User> wrapper=new QueryWrapper<>();
        wrapper
                .isNotNull("name")
                .isNotNull("email")
                .ge("age",25);
        List<User> users = userMapper.selectList(wrapper);
        System.out.println(users);
    }
```

## 测试2

```
  @Test
    void run2(){
//        找出名字为Tom的数据
        QueryWrapper<User> wrapper=new QueryWrapper<>();
        wrapper
                .eq("name","Tom");
        User user = userMapper.selectOne(wrapper);
        System.out.println(user);
    }
```

## 测试3

```
    @Test
    void run3(){
//         查询年龄在20-50之间的人
        QueryWrapper<User> wrapper=new QueryWrapper<>();
        wrapper
                .between("age",20,30);
        List<User> users = userMapper.selectList(wrapper);
        Integer integer = userMapper.selectCount(wrapper);
        System.out.println(integer);
        System.out.println(users);
    }
```

## 测试4

```
    @Test
    void run4(){
          //模糊查询
//        查询名字里不包含n的 并且邮箱号是以t开头的
        QueryWrapper<User> wrapper=new QueryWrapper<>();
//        左和右代表 %在左还是右 %
        wrapper.notLike("name","n")
                .likeRight("email","t");
        List<User> users = userMapper.selectList(wrapper);
        System.out.println(users);

    }
```

# 自动代码生成器

dao,service,controller,pojo都可以自动生成

## 添加依赖

```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-generator</artifactId>
    <version>3.3.2</version>
</dependency>
```

```
package com.cbx.mybatisplus;

import com.baomidou.mybatisplus.annotation.DbType;
import com.baomidou.mybatisplus.annotation.FieldFill;
import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableField;
import com.baomidou.mybatisplus.generator.AutoGenerator;
import com.baomidou.mybatisplus.generator.config.DataSourceConfig;
import com.baomidou.mybatisplus.generator.config.GlobalConfig;
import com.baomidou.mybatisplus.generator.config.PackageConfig;
import com.baomidou.mybatisplus.generator.config.StrategyConfig;
import com.baomidou.mybatisplus.generator.config.po.TableFill;
import com.baomidou.mybatisplus.generator.config.rules.DateType;
import com.baomidou.mybatisplus.generator.config.rules.NamingStrategy;

import java.util.ArrayList;

/**
 * 代码生成器
 */
public class AutoGenerate {
    public static void main(String[] args) {
        AutoGenerator mpg = new AutoGenerator();
//        全局配置
        GlobalConfig gc = new GlobalConfig();
        String projectPath = System.getProperty("user.dir"); //F:\Project\springbootProject\mybatisplus
        gc.setOutputDir(projectPath+"/src/main/java");
        gc.setAuthor("cbx");
        gc.setOpen(false);  //生成完是否打开文件夹
        gc.setFileOverride(false); //是否覆盖
        gc.setServiceName("%sService");//去掉service的I前缀
        gc.setIdType(IdType.AUTO);
        gc.setDateType(DateType.ONLY_DATE);
        gc.setSwagger2(true);
        mpg.setGlobalConfig(gc);
        /**
         * 设置数据源
         */
        DataSourceConfig dataSourceConfig = new DataSourceConfig();
        dataSourceConfig.setUrl("jdbc:mysql://localhost:3306/mybatisplus?useSSL=false&useUnicode=true&characterEncoding=utf-8");
        dataSourceConfig.setDbType(DbType.MYSQL);
        dataSourceConfig.setDriverName("com.mysql.jdbc.Driver");
        dataSourceConfig.setPassword("123");
        dataSourceConfig.setUsername("root");
        mpg.setDataSource(dataSourceConfig);
        /**
         * 包的配置
         */
        PackageConfig packageConfig = new PackageConfig();
        packageConfig.setModuleName("blog");
        packageConfig.setParent("com.kuang");
        packageConfig.setController("controller");
        packageConfig.setEntity("pojo");
        packageConfig.setMapper("mapper");
        packageConfig.setService("service");
        mpg.setPackageInfo(packageConfig);
        /**
         * 策略配置
         */
        StrategyConfig strategyConfig = new StrategyConfig();
        strategyConfig.setInclude("user"); //设置要映射的表名
        strategyConfig.setNaming(NamingStrategy.underline_to_camel);
        strategyConfig.setColumnNaming(NamingStrategy.underline_to_camel);
        strategyConfig.setEntityLombokModel(true);//自动lombook
        strategyConfig.setLogicDeleteFieldName("deleted");
        //自动填充配置
        TableFill create_time = new TableFill("create_time", FieldFill.INSERT);
        TableFill update_time = new TableFill("update_time", FieldFill.INSERT_UPDATE);
        ArrayList<TableFill> tableFills = new ArrayList<>();
        tableFills.add(create_time);
        tableFills.add(update_time);
        strategyConfig.setTableFillList(tableFills);
//        配置乐观锁
        strategyConfig.setVersionFieldName("version");
        strategyConfig.setRestControllerStyle(true);
        strategyConfig.setControllerMappingHyphenStyle(true); //http://xxxx/get_id_2
        mpg.setStrategy(strategyConfig);

        mpg.execute();
    }
}

```

