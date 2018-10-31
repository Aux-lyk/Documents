## 一、mixin简介

1. 有一种很常见的情况：有两个非常相似的组件，他们的基本功能是一样的，但他们之间又存在着足够的差异性，此时的你就像是来到了一个分岔路口：我是把它拆分成两个不同的组件呢？还是保留为一个组件，然后通过props传值来创造差异性从而进行区分呢？
2. 两种解决方案都不够完美：如果拆分成两个组件，你就不得不冒着一旦功能变动就要在两个文件中更新代码的风险，这违背了 DRY 原则。反之，太多的props传值会很快变得混乱不堪，从而迫使维护者（即便这个人是你）在使用组件的时候必须理解一大段的上下文，拖慢写码速度。
3. 使用Mixin。Vue 中的Mixin对编写函数式风格的代码很有用，因为函数式编程就是通过减少移动的部分让代码更好理解。Mixin允许你封装一块在应用的其他组件中都可以使用的**函数**。如果使用姿势得当，他们不会改变函数作用域外部的任何东西，因此哪怕执行多次，只要是同样的输入你总是能得到一样的值。不论样式，**逻辑一样**就可以用。

## 二、vue中mixin用法

为了结构规整可以新建一个`mixins`目录（类似于components目录）。我们创建的这个文件含有`.js`扩展名（跟`.vue`相对，就像我们的其他文件），为了使用Mixin我们需要输出一个对象。

```javascript
//   @/mixins/toggle.js
export const toggle = {
  data(){
    return {
      bool:true,
    }
  },
  methods:{
    changeFn(){
      this.bool=!this.bool;
    }
  }
}
```

然后在你需要用到这个mixin的对应的vue文件中使用

```vue
//index.vue
<template>
	...,
</template>

<script>
  ...,
  import {toggle} from '@/mixins/toggle'  //引入
  
  export default {
    ...,
    mixins: [toggle],  //调用
    ...,
  }
    
</script>

<style scoped>
	...,
</style>

```

## 三、与父子组件的区别

1. 组件在引用之后相当于在父组件内开辟了一块单独的空间，来根据父组件props过来的值进行相应的操作，单本质上两者还是泾渭分明，相对独立。

2. 而mixins则是在引入组件之后，则是将组件内部的内容如data等方法、method等属性与父组件相应内容进行合并。相当于在引入后，父组件的各种属性方法都被扩充了。

3. 单纯组件引用：

   ​          父组件 + 子组件 >>> 父组件 + 子组件

   mixins：

   ​          父组件 + 子组件 >>> new父组件

4.  当我们在组件上应用mixin的时候，有可能组件与mixin中都定义了相同的生命周期钩子，这时候钩子的执行顺序的问题凸显了出来。默认mixin上会首先被注册，组件上的接着注册，这样我们就可以在组件中按需要重写mixin中的语句。**组件拥有最终发言权。**

   首先是没有冲突的情况

   ```javascript
   //   @/mixins/toggle.js
   export const toggle = {
     data(){
       return {
         ...,
       }
     },
     methods:{
      ...,
     },
     mounted() {
       console.log('hello from mixin!')
     }
   }
   ```

   ```html
   //index.vue
   <script>
     ...,
     import {toggle} from '@/mixins/toggle'  //引入
     
     export default {
       ...,
       mixins: [toggle],  //调用
       ...,
       mounted() {
         console.log('hello from Vue instance!')
       }
     }
   </script>
   ```

   ```javascript
   //  Output in console
   > hello from mixin!
   > hello from Vue instance!
   ```

   这是由于mixin和组件的都有自己的生命周期钩子，而且可以看出mixin先注册

   有冲突的情况

   ```javascript
   //   @/mixins/toggle.js
   export const toggle = {
     data(){
       return {
         ...,
       }
     },
     methods:{
      sayHello() {
         console.log('hello from mixin!')
       }
     },
     mounted() {
       this.sayHello()
     }
   }
   ```

   ```vue
   //index.vue
   <script>
     ...,
     import {toggle} from '@/mixins/toggle'  //引入
     
     export default {
       ...,
       mixins: [toggle],  //调用
       methods: {
         sayHello() {
           console.log('hello from Vue instance!')
         }
       },
       mounted() {
         this.sayHello()
       }
     }
   </script>
   ```

   ```javascript
   // Output in console
   > hello from Vue instance!
   > hello from Vue instance!
   ```

   可以分析得到mixin的方法和属性没有被销毁，输出了两次不是只有一次，它只是被重写了。

   调用了两次`sayHello()`函数。

5. mixin与组件并不同时共享、同时处理这些变量，两者之间除了合并，是不会进行任何通信的。不是一种向下的类似`vuex`的数据共享方案。

## 四、全局mixin

1. 全局mixin被注册到了**每个单一组件上**。我们要对正在做事情的充满警惕，尤其当你打算为应用增加通用功能的时候
2. 为了创建一个全局实例，我们可以把它放在Vue实例之上。在一个典型的 Vue-cli 初始化的项目中，它可能在你的`main.js`文件中。

```javascript
//main.js

import Vue from 'vue'

import ...

Vue.use(ElementUI, { locale })

Vue.config.productionTip = false

Vue.mixin({   //mixin全局实例
  data(){
    return {}
  },
  methods:{
    ...,
  },
  mounted() {
    console.log('hello from mixin!')
  },
  ...,
})

new Vue({
  el: '#app',
  router,
  ...,
  store,
  template: '<App/>',
  components: { App }
})

```

## 五、总结

1. mixin对于封装一小段想要复用的代码来讲是有用的。
2. mixin当然不是唯一可行的选择：比如说高阶组件就允许你组合相似函数，mixin只是的一种实现方式。
3. mixin不需要传递状态，但是这种模式当然也可能会被滥用。所以，仔细思考下哪种选择对应用最有意义。