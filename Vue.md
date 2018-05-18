# Vue 学习笔记

## computed

> 模板内的表达式非常便利，但是设计它们的初衷是用于简单运算的。在模板中放入太多的逻辑会让模板过重且难以维护

> 在这个地方，模板不再是简单的声明式逻辑。你必须看一段时间才能意识到，这里是想要显示变量 `message` 的翻转字符串。当你想要在模板中多次引用此处的翻转字符串时，就会更加难以处理。

> 所以，对于任何复杂逻辑，你都应当使用**计算属性**。

- 计算属性和侦听器
- 组件渲染 计算 缓存

## watch

> 一个对象，键是需要观察的表达式，值是对应回调函数。值也可以是方法名，或者包含选项的对象。Vue 实例将会在实例化时调用 `$watch()`，遍历 watch 对象的每一个属性。

- 绑定时候不会执行 变化后再执行
- 比如监听到某个值的变化 给后台发请求
- 监听到某一个值的变化 做某一个事情
- deep 为真时 深入观察
- 监听了去生成某个值  不要监听了修改里面的值

```javascript
watch: {    
	obj: {
      handler () {
        console.log('obj.a changed')
      },
      immediate: true
      deep: true
    }
```



## methods

- 数据更新更 实时更新

```javascript
  methods: {
    getName () {
      console.log('getName invoked')
      return `${this.firstName} ${this.lastName}`
    }
  }
```



## Methods vs Computed
> https://www.w3cplus.com/vue/working-with-methods-in-vue.html


- `computed`是以Vue的依赖追踪机制为基础的，其试图处理这样的一件事情：当某一个数据（依赖数据）发生变化的时候，所有依赖这个数据的**相关数据**会**自动**发生变化，也就是自动调用相关的函数去实现数据的变动著作权归作者所有。
- `methods`里面是用来定义函数的，它需要手动调用才能执行，而不像`computed`那样，可以自动执行预先定义的函数著作权归作者所有。

> `methods`里面定义的函数是需要主动调用的，而`computed`里定义的函数会自动调用，完成我们希望完成的作用著作权归作者所有。 

从**性质上**看：

- `computed`是计算属性，事实上和`data`对象里的数据属性是同一类的著作权归作者所有。
- `methods`里面定义的是函数，要像JavaScript中的`function()`函数这样去调用它著作权归作者所有。

例如：

```javascript
computed: { 
    fullName: function () {
        return this.firstName + ' ' + this.lastName 
    } 
}
```

你在取用的时候，用`this.fullName`去取用就和取`data`一样（不要当成函数调用）。

