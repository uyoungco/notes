# method/computed/watch的使用和区别


## methods

在vue中method就是普通意义的function域,可以定义方法来进行属性的修改,或者返回,很简单下面来看例子: 

```javascript
<template>
    <p>{{msg}}</p>
    <button v-on:click="reverseMessage">逆转消息</button>
</template>
export default {

  name: 'Home',
  data: function () {
    return {
      ......
      msg: 'this msg for vue.js!!!',
      ......
    }
  },
  methods: {
    reverseMessage: function () {
      this.msg = this.msg.split('').reverse().join('')
      return this.msg
    }
 ......
```

显而易见 就是一个对msg这个串的反转; 



## computed

其实在一定程度上 methods和computed效果是一样的.但是: 

> 计算属性(computed)是基于它们的依赖进行缓存的。计算属性只有在它的相关依赖发生改变时才会重新求值。这就意味着只要 message 还没有发生改变，多次访问 reversedMessage 计算属性会立即返回之前的计算结果，而不必再次执行函数。 

可以这样理解,computed并不是一个方法,而是依赖于属性的,就是一个属性的封装,属性的值不变化,那么不会多次调用computed,所以性能更好  

ok~ 继续抛出一个例子: 

```javascript
<template>
      <p>Computed reversed message: "{{ reversedMessage }}"</p>
</template>
export default {

  name: 'Home',
  data: function () {
    return {
      ......
    }
  },
  computed: {
    // 计算属性的 getter
    reversedMessage: function () {
      // console.log('1')
      return this.msg.split('').reverse().join('')
    }
  }
......
```



## watch

Vue 提供了一种更通用的方式来观察和响应 Vue 实例上的数据变动：侦听属性。当你有一些数据需要随着其它数据变动而变动时，你很容易滥用 watch——特别是如果你之前使用过 AngularJS。然而，通常更好的做法是使用计算属性而不是命令式的 watch 



- 下面这段代码摘自官网

```javascript
### watch

var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar',
  },
  watch: {
    firstName: function (val) {
      this.fullName = val + ' ' + this.lastName
    },
    lastName: function (val) {
      this.fullName = this.firstName + ' ' + val
    }
  }
})


### computed
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
  computed: {
    fullName: function () {
      return this.firstName + ' ' + this.lastName
    }
  }
})
```

显而易见watch在这里就显得有一些多余了  

但是watch其实他是一个侦听,就类似与java中的接口会调,可以支持异步操作 

抛一段代码: 

```javascript
<template>
    {{firstname}}
    <button @click="firstname = 'change complete'">修改txt</button>
</template>
export default {

  name: 'Home',
  data: function () {
    return {
      ......
      firstname: 'hello',
      ......
    }
  },
   watch: {
    firstname: function (newval, oldval) {
      console.log(oldval, newval)
    }
 ......
```

在div中input输入框v-model这个firstname,然后在input中输入时,watch会侦听这个firstname,进而打出newval和oldval的日志 

method/computed/watch是vue中script域中核心的使用,感谢阅读,期待一起进步 





# computed和watch的使用场景 

- **从作用机制和性质上看待methods,watch和computed的关系** 

首先要说，methods,watch和computed都是以函数为基础的，但各自却都不同  

而从作用机制和性质上看,methods和watch/computed不太一样，所以我接下来的介绍主要有两个对比：  

- 1.methods和(watch/computed)的对比
- 2.watch和computed的对比  

作用机制 

1. watch和computed都是以Vue的依赖追踪机制为基础的，它们都试图处理这样一件事情：当某一个数据（称它为依赖数据）发生变化的时候，所有依赖这个数据的“相关”数据“自动”发生变化，也就是自动调用相关的函数去实现数据的变动。  
2. 对methods:methods里面是用来定义函数的，很显然，它需要手动调用才能执行。而不像watch和computed那样，“自动执行”预先定义的函数

【总结】：methods里面定义的函数，是需要主动调用的，而和watch和computed相关的函数，会自动调用,完成我们希望完成的作用  

从性质上看  

1.methods里面定义的是函数，你显然需要像”fuc()”这样去调用它（假设函数为fuc）

2.computed是计算属性，事实上和和data对象里的数据属性是同一类的（使用上）, 

例如： 

```javascript
computed:{
  fullName: function () { return this.firstName + lastName }
}
```

你在取的时候，用this.fullName去取用，就和取data一样（不要当成函数调用！！）  



3. watch:类似于监听机制+事件机制： 

例如：

```javascript
watch: {
    firstName: function (val) { this.fullName = val + this.lastName }
}
```

firstName的改变是这个特殊“事件”被触发的条件，而firstName对应的函数就相当于监听到事件发生后执行的方法 

watch和computed的对比  

说了这么多，下面先对watch和computed 

首先它们都是以Vue的依赖追踪机制为基础的，它们的共同点是：都是希望在依赖数据发生改变的时候，被依赖的数据根据预先定义好的函数，发生“自动”的变化 

我们当然可以自己写代码完成这一切，但却很可能造成写法混乱，代码冗余的情况。Vue为我们提供了这样一个方便的接口，统一规则 

但watch和computed也有明显不同的地方：  

watch和computed各自处理的数据关系场景不同  

1. watch擅长处理的场景：一个数据影响多个数据  
2. computed擅长处理的场景：一个数据受多个数据影响 