# JavaScirpt 笔记

## Arrows and Lexical This（箭头函数）

```javascript
// 未使用箭头函数的写法
{
  addOptions: function (options) {
    var self = this
    options.forEach(
      function (name, opts) {
        self[name] = self.addChild(name, opts);
      });
  }
}
// 使用箭头函数后的写法
{

  addOptions:function (options) {
    options.forEach((name, opts) => {
      this[name] = this.addChild(name, opts);
    });
  }
}
```

## Destructuring（解构）

```javascript
const
  bookSet = [
    'UED',
    'TB fed',
    'Not find'
  ];
const bookCollection = () => {
  return {
    book1:'UED',
    book2:'TB fed'
  };
};
// 1. 解构也可以设置默认值
const{ book1, book3 ='Not find'} = bookCollection();
// 2. 解构数组时候是可以跳过其中某几项的
const [book1, , book3] = bookSet;
// book1 = 'UED', book3 = 'Not find'
// 3. 解构可以取到指定对象的任何属性，包括它包含的方法
const { length: setLength } = bookSet;
// setLength = 3
```

## **Promise**  

> 对象用于表示一个异步操作的最终状态（完成或失败），以及其返回的值。 

### 语法

```javascript
new Promise( function(resolve, reject) {...} /* executor */  );
```

**参数**

executor 

executor是带有 `resolve` 和 `reject` 两个参数的函数 。Promise构造函数执行时立即调用`executor` 函数， `resolve` 和 `reject` 两个函数作为参数传递给`executor`（executor 函数在Promise构造函数返回新建对象前被调用）。`resolve` 和 `reject` 函数被调用时，分别将promise的状态改为*fulfilled（*完成）或rejected（失败）。executor 内部通常会执行一些异步操作，一旦完成，可以调用resolve函数来将promise状态改成*fulfilled*，或者在发生错误时将它的状态改为rejected。

如果在executor函数中抛出一个错误，那么该promise 状态为rejected。executor函数的返回值被忽略。

