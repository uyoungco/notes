# async(异步) 函数变体

>  http://www.css88.com/archives/7731

- 异步函数声明： `async function foo() {}`
- 异步函数表达式： `const foo = async function () {};`
- 异步函数定义：`let obj = { async foo() {} }`
- 异步箭头函数： `const foo = async () => {};`



## async(异步) 函数总是返回 Promises

async(异步) 函数的 Promise 完成状态： 

```javascript
async function asyncFunc() {
    return 123;
}
 
asyncFunc()
.then(x => console.log(x));
    // 123
```

async(异步) 函数的 Promise 拒绝状态： 

```javascript
async function asyncFunc() {
    throw new Error('Problem!');
}
 
asyncFunc()
.catch(err => console.log(err));
    // Error: Problem!
```



## 通过 `await` 处理 async(异步) 计算的结果和错误

> `await`（只允许在 async(异步) 函数内部使用）等待其操作对象 Promise 返回： 

- 如果 Promise 是完成状态，`await` 的结果是完成态的值。
- 如果 Promise 是拒绝状态，`await` 会抛出拒绝值。



处理单个 async(异步) 返回值： 

```javascript
async function asyncFunc() {
    const result = await otherAsyncFunc();
    console.log(result);
}
 
// 等价于:
function asyncFunc() {
    return otherAsyncFunc()
    .then(result => {
        console.log(result);
    });
}
```

按顺序处理多个 async(异步) 返回值： 

```javascript
async function asyncFunc() {
    const result1 = await otherAsyncFunc1();
    console.log(result1);
    const result2 = await otherAsyncFunc2();
    console.log(result2);
}
 
// 等价于:
function asyncFunc() {
    return otherAsyncFunc1()
    .then(result1 => {
        console.log(result1);
        return otherAsyncFunc2();
    })
    .then(result2 => {
        console.log(result2);
    });
}
```

并行处理多个 async(异步) 返回值： 

```javascript
async function asyncFunc() {
    const [result1, result2] = await Promise.all([
        otherAsyncFunc1(),
        otherAsyncFunc2(),
    ]);
    console.log(result1, result2);
}
 
// 等价于:
function asyncFunc() {
    return Promise.all([
        otherAsyncFunc1(),
        otherAsyncFunc2(),
    ])
    .then([result1, result2] => {
        console.log(result1, result2);
    });
}
```

错误处理： 

```javascript
async function asyncFunc() {
    try {
        await otherAsyncFunc();
    } catch (err) {
        console.error(err);
    }
}
 
// 等价于:
function asyncFunc() {
    return otherAsyncFunc()
    .catch(err => {
        console.error(err);
    });
}
```



## 理解 async(异步) 函数

在我解释 async(异步) 函数之前，我需要解释一下如何组合使用 Promises 和 Generator ，通过看起来同步的代码来执行 async(异步) 操作。

对于能够 async(异步) 计算其一次性结果的函数，作为 ES6 一部分的 Promises 已经变得流行起来。一个例子是 [客户端 fetch API](https://fetch.spec.whatwg.org/#concept-request) ，它是 XMLHttpRequest 获取数据的替代方法。使用示例如下：

```javascript
function fetchJson(url) {
    return fetch(url)
    .then(request => request.text())
    .then(text => {
        return JSON.parse(text);
    })
    .catch(error => {
        console.log(`ERROR: ${error.stack}`);
    });
}
fetchJson('http://example.com/some_file.json')
.then(obj => console.log(obj));
```



## 通过 generator 来编写异步代码

co 是一个使用 Promise 和 generator 来实现看似同步编码的库，但与上一示例中使用的样式相同： 

```javascript
const fetchJson = co.wrap(function* (url) {
    try {
        let request = yield fetch(url);
        let text = yield request.text();
        return JSON.parse(text);
    }
    catch (error) {
        console.log(`ERROR: ${error.stack}`);
    }
});
```

​	每次回调函数（ generator 函数）产生一个 Promise 对象给 co ，回调会被暂停，只有当 Promise 执行完成后，co 才会继续执行回调 。 如果 Promise 处于完成状态，`yield` 返回完成状态的值，如果处于拒绝状态，`yield` 抛出拒绝状态的错误。此外，co 保证结果是通过回调执行完成才返回的（类似于 `then()` 所做的工作）。 



## 通过 async(异步) 函数来编写异步代码

async(异步) 函数用的特定语法基本上和 co 类似： 

```javascript
async function fetchJson(url) {
    try {
        let request = await fetch(url);
        let text = await request.text();
        return JSON.parse(text);
    }
    catch (error) {
        console.log(`ERROR: ${error.stack}`);
    }
}
```

在内部，异步函数写法更类似于 generators 。 

