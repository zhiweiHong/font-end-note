#### 概念

###### 官方解释

 `Vuex` 是一个专为 `Vue.js` 应用程序开发的**状态管理模式**。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。 

###### 我的定义

说白了，`Vuex`就是一个全局变量管理器，诸如用户信息，地理位置这些可以在全局中使用又不想单独在每个组件中定义的变量，可以定义在`Vuex`当中。

#### 响应式流程

这个状态自管理应用包含以下几个部分：

- **state**，驱动应用的数据源；
- **view**，以声明方式将 **state** 映射到视图；
- **actions**，响应在 **view** 上的用户输入导致的状态变化。

以下是一个表示“单向数据流”理念的简单示意：

![img](E:\font-end-note\vuejs\flow.png)

以下是整个交互流程图

 ![vuex](E:\font-end-note\vuejs\vuex.png) 

#### 组成

##### state

###### 概念

所有变量的集合定义，类似于数据源的存在

###### 特点

- 单一状态树
  - `Vue`定义每个应用只允许定义一个store

###### 代码

- `Vuex`
  - 代码位于`store`文件夹下

```js
import Vuex from 'vuex'
import Vue from "vue"

Vue.use(Vuex)

const vuex = new Vuex.Store({
    state:{
        counter : 1
    }
})

export default vuex
```



- main.js

```js
import Vue from 'vue'
import App from './App.vue'
import store from './store'

Vue.config.productionTip = false


new Vue({
  render: h => h(App),
  // 使用Vuex
  store
  
}).$mount('#app')
```

- `App.vue`

```vue
<template>
  <div id="app">
     <h1>{{$store.state.counter}}</h1>
  </div>
</template>

<script>
export default {
  name: 'app',
}
</script>
```

###### 注意

- mapState函数

  当一个组件需要获取多个状态时候，将这些状态都声明为计算属性会有些重复和冗余。为了解决这个问题，我们可以使用 `mapState` 辅助函数帮助我们生成计算属性，让你少按几次键： 

```js
// 在单独构建的版本中辅助函数为 Vuex.mapState
import { mapState } from 'vuex'

export default {
  // ...
  // 本质上是语法糖
  computed: mapState({
    // 箭头函数可使代码更简练
    count: state => state.count,

    // 传字符串参数 'count' 等同于 `state => state.count`
    countAlias: 'count',

    // 为了能够使用 `this` 获取局部状态，必须使用常规函数
    countPlusLocalState (state) {
      return state.count + this.localCount
    }
  })
}
```

##### Getters

###### 概念

有时候我们需要从 store 中的 state 中派生出一些状态，但是每一次都从state中取出这个数据又显得不是很理想。

 `Vuex` 允许我们在 store 中定义“getter”（可以认为是 store 的计算属性）。就像计算属性一样，getter 的返回值会根据它的依赖被缓存起来，且只有当它的依赖值发生了改变才会被重新计算。 

###### 参数

- state

  接受state作为第一参数。这个state就是当前store定义的state

###### 代码

- store代码

```js
import Vuex from 'vuex'
import Vue from "vue"

Vue.use(Vuex)

const vuex = new Vuex.Store({
    state:{
        counter : 1
    },
    getters:{
        getCounter(state){
            return state.counter
        }
    },
    mutations:{
        increase(context){
            context.state.counter ++
        }
    }
})

export default vuex
```

- 组件中调用

```vue
<template>
  <div id="app">
      // 使用getters属性进行获取
     <h1>{{$store.getters.getCounter}}</h1>
  </div>
</template>
```

##### Mutation

###### 概念

更改 `Vuex` 的 store 中的状态的唯一方法是提交 mutation。`Vuex` 中的 mutation 非常类似于事件：每个 mutation 都有一个字符串的 **事件类型 (type)** 和 一个 **回调函数 (handler)**。这个回调函数就是我们实际进行状态更改的地方，并且它会接受 state 作为第一个参数。

但是我们不能直接调用一个mutation handler。Vuex中的mutation 更像是事件注册。而事实上，mutation 中定义的也仅仅只是事件类型。如果想要调用，必须使用`commit`

```js
store.commit('increment')
```

###### 提交携带参数

- 普通携带

```js
import Vuex from 'vuex'
import Vue from "vue"

Vue.use(Vuex)

const vuex = new Vuex.Store({
    state:{
        counter : 1
    },
    getters:{
        getCounter(state){
            return state.counter
        }
    },
    mutations:{
        increase(state,index){
            state.counter = state.counter + index
        }
    }
})

export default vuex
```

```vue
<template>
  <div id="app">
     <button @click="increment(1)">increment</button>
     <h1>{{$store.state.counter}}</h1>
  </div>
</template>

<script>
import { mapState } from 'vuex'

export default {
  name: 'app',
  data(){
    return {
      localCount : 1
    }
  },
  methods:{
    increment(index){
      this.$store.commit("increase",index)
    }
  }
}
</script>

```

- payload携带方式
  - 此种方式可以携带一个对象
    - 对象中可以存储变量或者方法

```js
import Vuex from 'vuex'
import Vue from "vue"

Vue.use(Vuex)

const vuex = new Vuex.Store({
    state:{
        counter : 1
    },
    getters:{
        getCounter(state){
            return state.counter
        }
    },
    mutations:{
        increase_payload(state,payload){
            console.log(payload)
            state.counter = state.counter + payload.index
        },
    }
})

export default vuex
```

```vue
<template>
  <div id="app">
     <button @click="increment_payload()">increment_payload</button>
     <h1>{{$store.state.counter}}</h1>
  </div>
</template>

<script>
import { mapState } from 'vuex'

export default {
  name: 'app',
  data(){
    return {
      localCount : 1
    }
  },
  methods:{
    increment_payload(){
       this.$store.commit("increase_payload",{index:1})
    }
  }
}
</script>

```

###### 提交风格

提交 mutation 的另一种方式是直接使用包含 `type` 属性的对象：

```js
store.commit({
  type: 'increment',
  // 表示参数是amount，值等于10
  amount: 10
})
```

当使用对象风格的提交方式，整个对象都作为载荷传给 mutation 函数，因此 handler 保持不变：

```js
mutations: {
  increment (state, payload) {
    state.count += payload.amount
  }
}
```

###### 注意事项

既然 `Vuex` 的 store 中的状态是响应式的，那么当我们变更状态时，监视状态的 Vue 组件也会自动更新。这也意味着 `Vuex` 中的 mutation 也需要与使用 `Vue` 一样遵守一些注意事项：

1. 最好提前在你的 store 中初始化好所有所需属性。
2. 当需要在对象上添加新属性时，你应该

- 使用 `Vue.set(obj, 'newProp', 123)`, 或者
- 以新对象替换老对象。

###### 使用常量替代Mutation事件类型

使用常量替代 mutation 事件类型在各种 Flux 实现中是很常见的模式。这样可以使 linter 之类的工具发挥作用，同时把这些常量放在单独的文件中可以让你的代码合作者对整个 app 包含的 mutation 一目了然：

```js
// mutation-types.js
export const SOME_MUTATION = 'SOME_MUTATION'
// store.js
import Vuex from 'vuex'
import { SOME_MUTATION } from './mutation-types'

const store = new Vuex.Store({
  state: { ... },
  mutations: {
    // 我们可以使用 ES2015 风格的计算属性命名功能来使用一个常量作为函数名
    [SOME_MUTATION] (state) {
      // mutate state
    }
  }
})
```

##### Action

###### 概念

Action 类似于 mutation，不同在于：

- Action 提交的是 mutation，而不是直接变更状态。
- Action 可以包含任意异步操作。
- 使用`dispatch`进行调用

###### 参数

-  Action 函数接受一个与 store 实例具有相同方法和属性的 context 对象，因此你可以调用 `context.commit` 提交一个 mutation，或者通过 `context.state` 和 `context.getters` 来获取 state 和 getters。

###### 代码

- `vuex`

```js
import Vuex from 'vuex'
import Vue from "vue"
import constants  from "./constant"

Vue.use(Vuex)

const vuex = new Vuex.Store({
    state:{
        counter : 2
    },
    mutations:{
        [constants.UPDATTE](state,counter){
            state.counter = counter
        }
    },
    actions:{
        power(context){
            window.setTimeout(()=>{
                let power = context.state.counter * context.state.counter
                context.commit(constants.UPDATTE,power)
            })
        },
    }
})

export default vuex
```

- `App.vue`

```vue
<template>
  <div id="app">
     <button @click="action()">action</button>
     <h1>{{$store.state.counter}}</h1>
  </div>
</template>

<script>
import { mapState } from 'vuex'
import constants from "./store/constant"
export default {
  name: 'app',
  data(){
    return {
      localCount : 1
    }
  },
  methods:{
    action(index){
      this.$store.dispatch("power")
    },
  }
}
</script>
```

###### 映射方法

```js
import { mapActions } from 'vuex'

export default {
  // ...
  methods: {
    ...mapActions([
      'increment', // 将 `this.increment()` 映射为 `this.$store.dispatch('increment')`

      // `mapActions` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.dispatch('incrementBy', amount)`
    ]),
    ...mapActions({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.dispatch('increment')`
    })
  }
}
```

##### Module

###### 概念

由于使用单一状态树，应用的所有状态会集中到一个比较大的对象。当应用变得非常复杂时，store 对象就有可能变得相当臃肿。 

```js
const moduleA = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: { ... },
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态

```

###### 注意

- `Vuex`中的模块的getter，mutation，action对于开发者而言，与之前调用没有任何区别。