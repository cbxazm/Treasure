# 安装koa

```
npm install -g koa-generator
koa2 -e koa2-learn  //-e代表一种模板引擎
cd koa2-learn
npm install  //先进入项目目录，然后进行安装
http://localhost:3000
```

![image-20200526135206045](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200526135206045.png)

# MongoDB

```
mongod --config E:\software\MongDB\etc\mongodb.conf
```

## 安装Robo 3T可视化工具

## 在koa2-learn目录下安装mongoose

```
npm i mongoose
```

![image-20200526202608046](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200526202608046.png)

# 安装中间件来处理redis

```
npm i koa-generic-session koa-redis
//在app.js里引入中间件
const session=require('koa-generic-session')
const redis =require('koa-redis')
```

# Nuxt.js工作流

![image-20200527125433896](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200527125433896.png)

## 安装

```
vue init nuxt-community/koa-template nuxt-learn //联网不行
//解决方案
vue init nuxtKoa\koa-template nuxt-learn --offline //从github上克隆 放在c盘的指定目录
C:\Users\user\.vue-templates
cd nuxt-learn
npm install //进行相关依赖的安装
npm i koa-router //安装koa-router	
npm i sass-loader node-sass
```

## 路由

文件名就是路由的地址  //在Pages目录下都是用来做路由的

# 项目准备

## nuxt.js新的创建项目的方法

```
npm install -g npx
npx create-nuxt-app 项目名
进入项目目录 npm install --update-binary  //防止项目出问题
```

## 脚手架创建的问题

不支持import ,默认没有使用babel解析

https://www.cnblogs.com/sese/p/11976250.html

## 城市服务组件

![image-20200527162822084](C:\Users\user\AppData\Roaming\Typora\typora-user-images\image-20200527162822084.png)

