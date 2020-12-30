# State

state提供了唯一的公共数据资源，所有共享的数据都要统一的放到Store的State中进行存储

```
//创建store数据源，提供唯一的公共数据
const store=new Vuex.Store({
      state:{count :0}
})
```

## 组件访问State中数据的方式一：

```
this.$store.state.全局数据名称
```

## 组件访问State中数据的方式一：

```
1.从vuex中按需导入mapState函数
import {mapState} from 'vuex'
2.将全局的数据映射为当前组件的计算属性
computed:{
  ...mapState(['count'])// 可以传数组，也也可以传对象
  ...mapState({
       currentCount:'count'  //使用的时候使用currentCount就行了
  })
}
```

```
<template>
      <div>
        当前count值为:{{count}}
        <button>-1</button>
      </div>
    </template>

    <script>
      import {mapState} from 'vuex'
      export default {
        data(){
          return {};
        },
        computed:{
          ...mapState(['count'])
        }
      }
    </script>

<style scoped>

</style>


```

# Mutation

用于变更Store中的数据

1.只能通过mutation来变更Store中的数据，不可以直接操作Store中的数据

2.通过mutation可以集中监控所有数据的变化

## 触发mutation的方式一

```
//定义mutation
const store=new Vuex.Store({
      state:{count :0}，
      mutations:{
         add(state){
         //变更状态
          state.count++
         }
      }
})

//触发mutation
methods:{
   handle1:{
       //方式一触发 （触发哪个方法就调用哪个方法的名称）
       this.$store.commit('add')
   }
}
```

### 触发mutation的时候传递参数

```
const store=new Vuex.Store({
      state:{count :0}，
      mutations:{
         add(state,step){
         //变更状态
          state.count+=step
         }
      }
})
//触发mutation
methods:{
   handle1:{
       //方式一触发 （触发哪个方法就调用哪个方法的名称）
       this.$store.commit('add',3)
   }
}
```

## 触发mutation的方式二

```
1.从vuex中按需导入mapMutations函数
import {mapMutations} from 'vuex'
2.将指定的mutations函数，映射为当前组件的methods函数
methods:{
    ...mapMutations(['add','addN'])
}
```

```
<template>
  <div>
    当前count值为:{{count}}
    <button @click="subBtn">-1</button>
  </div>
</template>

<script>
  import {mapState,mapMutations} from 'vuex'
  export default {
    data(){
      return {};
    },
    computed:{
      ...mapState(['count'])
    },
    methods:{
      ...mapMutations(['sub']),
      subBtn(){
         this.sub()
      }
    }
  }
</script>

<style scoped>

</style>


```

# Action

用来处理异步任务

如果通过异步操作变更数据，必须通过Action,而不能使用Mutation

但是在Action里还是要通过触发Mutation的方式间接变更数据

## 触发Action的方式一

```
const store=new Vuex.Store({
    mutations:{
    //可以处理同步操作
          add(state){
               state.count++
          }
    },
    actions:{
           addAsyc(context){
           setTimeout(()=>{
              contxt.commit('add')
           },1000)
       }
    }
})

//触发Action
methods:{
   handle(){
       this.$store.dispatch('addAsyc')
   }
}


```

## 触发Action的方式二

```
1.从vuex中按需导入mapActions函数
import {mapActions} from 'vuex'
2.将指定的actions函数，映射为当前组件的methods函数
 methods:{
    ...mapActions(['addAsyc','addNAsyc'])
 }
```

# Getter

用于对Store中的数据进行加工处理形成新的数据

1.Getter可以对Store中已有的数据加工处理形成新的数据，类似Vue的计算属性

2.Store中的数据发生变化，Getter的数据也会跟着变化

```
 getters:{
     showNum(state){
        return'当前的最新数量是['+state.count+']'
     }
   }
```



## 使用Getters的第一种方式

```
this.$store.getters.名称
```



## 使用Getters的第二种方式

```
import {maoGetters} from 'vuex'
computed:{
    ...mapGetters(['showNum'])
}
```



