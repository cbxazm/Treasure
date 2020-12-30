# 什么是Promise

## 抽象表达

Promise是js进行一步变成的新的解决方案

## 具体表达

1.从语法:Promise是一个构造函数

2.从功能:Promise对象用来封装一个异步操作，并可以获得结果

## Promise的状态改变

resolved

rejected

只有两种状态，且一个Promise对象只能改变一次

成功的数据为value，失败的数据为reason

# promise的基本使用

```
   const p=new Promise((resolve,reject)=>{
     //    执行异步任务
         setTimeout(()=>{
             const time=Date.now()
             if (time%2==0){
                 resolve('success '+time)
             } else {
                 reject('failure' +time)
             }
         },1000)
     })
     p.then(
         value => {
         //    接收成功的value数据
             console.log('成功的回调'+value)
         },
         reason => {
         //    接收失败的reason数据
             console.log('失败的回调'+reason);
         }
     )
```

# 为什么使用Promise

旧方法:必须在启动异步任务之前指定回调函数

Promise:启动异步任务=》返回Promise对象=》给Promise对象绑定回调函数

promise:可以解决回调地狱问题，因为他支持链式的调用