# 概念

## promise是什么？

- 主要用于处理异步请求。
- 可以将异步操作队列化，按照期望执行，并且返回预期的结果。

## 解决问题

- 回调地狱。即一层网络请求中嵌套着一层网络请求，循环往复。
- 避免页面冻结。

# 最简单的栗子

- 我们现在有三个延迟执行任务，分别是A，B，C。必须是A执行完毕后B执行，B执行完毕后C执行。

```js
setTimeout(()=>{
    // 任务A
    console.log("I love beijing")
    setTimeout(()=>{
        // 任务B
        console.log("I love Shanghai");
        setTimeout(()=>{
            // 任务C
            console.log("I love Chengdu");
        },1000)
    },2000)
},1000)
```

由于这段代码即为简单，看官可能会觉得好像看起来逻辑也还算清晰。然而一旦任务A或者任务B或者任务C其中有一个任务的代码量达到百行，那么这段代码将十分难以维护。单单是寻找边界可能都要花费大量时间。

- 优化后代码

```js
new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve()
    }, 1000)
}).then(() => {
    // 任务A
    console.log("I love beijing")
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve()
        }, 2000)
    })
}).then(() => {
    // 任务B
    console.log("I love Shanghai");
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve()
        }, 1000)
    })
}).then(() => {
    // 任务C
    console.log("I love Chengdu");
})
```

尽管代码量有所增加，但是代码的结构反而更加清晰了。哪里属于任务A，哪里属于任务B，哪里属于任务C一目了然。

# 参数结构

## resolve

此函数主要用于异步请求成功后处理回调。一般是在`then`中传入对应的回调函数。

## reject

此函数主要用于异步请求失败后处理回调。一般是在`catch`中传入相关的回调函数。一旦发生异常，会立刻执行catch函数。

# 关于代码优化

## resolve

在<u>最简单的栗子</u>中我们提到了`promise`代码。但是代码仍然是过分冗长。Promise官方也提供了一些优化方案。

### `Promise.resolve`

```js
new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve()
    }, 1000)
}).then(() => {
    console.log("I love beijing")
    return Promise.resolve(()=>{
        setTimeout(() => {
            resolve()
        }, 2000)
    })
}).then(() => {
    console.log("I love Shanghai");
    return Promise.resolve(()=>{
        setTimeout(() => {
            resolve()
        }, 1000)
    })
}).then(() => {
    console.log("I love Chengdu");
})
```

### 更简单的return ？

```js
new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve()
    }, 1000)
}).then(() => {
    console.log("I love beijing")
    return "I love Shanghai"
}).then((data) => {
    console.log(data);
    return "I love Chengdu"
}).then((data) => {
    console.log(data);
})
```

这种写法更适合只返回对象。如果依然存在异步操作，建议使用`Promise.reslove`

## reject

### `Promise.reject`

```js
new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve()
    }, 1000)
}).then(() => {
    console.log("I love beijing")
    return Promise.reject("error msg")
}).then((data) => {
    console.log(data);
    return "I love Chengdu"
}).then((data) => {
    console.log();
}).catch(data => console.log(data))
```

### 简单粗暴的throw

```js
new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve()
    }, 1000)
}).then(() => {
    console.log("I love beijing")
    throw "error msg"
}).then((data) => {
    console.log(data);
    return "I love Chengdu"
}).then((data) => {
    console.log();
}).catch(data => console.log(data))
```

# all

## 场景

在某种情况下，我们可能需要两种请求同时返回之后在进行代码操作。

## 举个栗子

- 优化前模拟代码

```js
        let firstRequest = false;
        let secondRequest = false;

        setTimeout(() => {
            firstRequest = true
            handler()
        }, 500)

        setTimeout(() => {
            secondRequest = true
            handler()
        }, 1000)

        function handler() {
            if (firstRequest && secondRequest) {
                console.log("success!");
            }
        }
```

如果出现上述情况，我们可以使用上述方法。但是肉眼可见的麻烦。Promise为我们提供了`all`方法处理这样的场景

- 使用`all`方法

```js
Promise.all([
    new Promise((resovle,reject) =>{
        setTimeout(()=>{
            resovle("first")
        },1000)
    }).then(data => {
        return data
    }),
    new Promise((resovle,reject) =>{
        setTimeout(()=>{
            resovle("second")
        },2000)
    }).then(data => {
        return data
    }),
]).then(result =>{
    console.log(result)
})
```

打印结果

```js
(2) ["first", "second"]
```

