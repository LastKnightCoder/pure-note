---
tags: [Vue, VueRouter]
---

`Vue` 经常被用来构建单页应用，这意味着需要在前端完成在多个视图之间的切换，想要做到这一点，目前的解决方案通过监控路由，根据路由来切换不同的组件，以达到多视图的切换。`Vue` 官方为我们提供了 `Vue Router` 来帮助我们实现这个目的。

## 安装

如果你的项目只是简单的 `HTML` 页面，那么可以通过 `CDN` 引入 `Vue Router`

```html
<script src="https://unpkg.com/vue-router/dist/vue-router.js"></script>
```

如果你的项目是一个 `npm` 项目，那么可以通过 `npm` 下载 `Vue Router`

```bash
npm install --save vue-router
```

如果你是通过 `vue-cli` 创建的项目，则你可通过 `vue add` 来添加 `Vue Router`

```bash
vue add router
```

## 基本使用

首先看入口组件 `App.vue`

```html
// App.vue
<template>
  <div id="app">
    <router-link to="/">Index</router-link>
    <router-link to="/about">About</router-link>

    <router-view></router-view>
  </div>
</template>
```

其中牵涉到两个新标签：

- `<router-link>`：默认会被渲染为超链接，当点击时会跳转到 `to` 属性指定的地址(路由)
- `<router-view>`：根据路由被渲染为特定的组件

接下来我们便要定义路由与组件之间的对应关系。我们将对应关系写在单独的文件中，以便后期维护，新建文件夹 `router`，接着在其中新建文件 `index.js`，在其中定义路由规则

```javascript
// router/index.js
import Vue from 'vue'
import VueRouter from 'vue-router'

import Index from '@/views/Index.vue'
import About from '@/views/About.vue'

Vue.use(VueRouter);

const routes = [{
    path: '/',
    component: Index
}, {
    path: '/about',
    component: About
}]

const router = new VueRouter({
    routes
})

export default router
```

除开引入组件的代码，首先我们通过 `Vue.use(VueRouter)` 使用了 `VueRouter` 插件，接着我们定义的路由与组件的对应关系

| 路由   | 组件  |
| ------ | ----- |
| /      | Index |
| /about | About |

当路由为 `/` 时，`<router-view>` 便会被渲染为 `<Index>`，当路由为 `/about` 时，`<router-view>` 便会被渲染为 `<About>`。`Index.vue` 与 `About.vue` 的内容如下

```html
// Index.vue
<template>
    <div>
        <h1>Welcome to Index page!</h1>
    </div>
</template>
```

```html
// About.vue
<template>
    <div>
        <h1>This is About page!</h1>
    </div>
</template>
```

内容都十分的简单。接着我们根据路由对应关系创建了 `VueRouter` 实例 `router`，并将其导出。在入口文件 `main.js` 中我们使用该 `router`

```javascript
// main.js
import Vue from 'vue'
import App from './App.vue'
import router from './router'

Vue.config.productionTip = false

new Vue({
  router,
  render: h => h(App)
}).$mount('#app')
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210404133346.gif" alt="1" style="zoom:50%;" />

当我们点击链接时，地址栏发生变化，且渲染的组件也顺应变化。

```` tip
`VueRouter` 有两种导航模式：

- `hash`
- `history`

注意到地址栏中出现了 `#`，表示此时 `VueRouter` 是 `hash` 模式，地址栏中有 `#` 未免不好看，我们可以在创建 `VueRouter` 实例时设置 `mode` 选项为 `'history'`，这时 `VueRouter` 便是 `history` 模式了
```javascript
// router/index.js
const router = new VueRouter({
    mode: 'history',
    routes
})
```
<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210404134333.gif" alt="2" style="zoom:50%;" />

这时地址栏上便没有 `#` 了。
````

有的时候路径的拼写比较复杂，我们可以为路由命名，然后在 `to` 属性直接指定跳转的路由名称。首先我们在 `routes` 数组中为路由指定名称

```javascript
// router/index.js
const routes = [{
    name: 'index',
    path: '/',
    component: Index
}, {
    name: 'about',
    path: '/about',
    component: About
}]
```

上面我们通过 `name` 属性为路由指定了名称，接着我们修改 `App.vue` 中的导航链接

```html
// App.vue
<template>
  <div id="app">
    <router-link :to="{name: 'index'}">Index</router-link>
    <router-link :to="{name: 'about'}">About</router-link>

    <router-view></router-view>
  </div>
</template>
```

## 动态路由匹配

有的时候我们可能需要为组件传递额外的参数，以显示不同的信息，例如 `/user/1` 显示 `id = 1` 的用户的信息，`/user/2` 显示 `id = 2` 用户的信息，参数通过路由传递，这种路由我们称为动态路由。为了能够接收到参数，我们需要如下定义路由与组件的关系

```javascript
// router/index.js
const routes = [{
    path: '/user/:id',
    component: User
}]
```

注意到动态路由的定义方式 `path: '/user/:id'`，接着我们便可在 `User` 组件中通过计算属性 `$route` 来访问到传入的参数，`$route` 包含如下属性

- fullpath
- path
- query
- params
- name

我们可以通过 `$route.params.id` 来访问到传入的参数，`User.vue` 如下

```html {4}
// User.vue
<template>
	<div>
		<p>User: {{$route.params.id}}</p>
	</div>
</template>
```

`App.vue` 的内容如下

```html {4-5}
// App.vue
<template>
  <div id="app">
    <router-link to="/user/1">user1</router-link>
    <router-link to="/user/2">user2</router-link>

    <router-view></router-view>
  </div>
</template>
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210404143251.gif" alt="1" style="zoom:50%;" />

有一点需要注意的是，动态路由组件进行切换时会进行组件复用，当路由从 `/user/1` 变为 `/user/2` 时并没有重新创建一个新的组件，而是复用了之前的组件。我们可以在 `created` 方法中打印信息，如果在切换组件时，信息始终只打印了一次，就说明组件只被创建了一次，即组件复用了

```html {10-12}
// User.vue
<template>
	<div>
		<p>User: {{$route.params.id}}</p>
	</div>
</template>

<script>
export default {
    created() {
        console.log('created')
    },
}
</script>
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210404144349.gif" alt="2" style="zoom:50%;" />

组件复用能够带来性能上的优势，但是有的时候我们需要根据路由参数请求数据，因为组件复用的关系，无法通过生命周期钩子请求数据，这时我们可以通过监听器来监听 `$watch`，当路由发生变化时，我们便发起请求

```html
// User.vue
<template>
	<div>
		<p>User: {{$route.params.id}}</p>
	</div>
</template>

<script>
export default {
	watch: {
		$route: {
			immediate: true,
			handler() {
				console.log('route change!', this.$route.params.id);
			}
		}
	}
}
</script>
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210404145423.gif" alt="3" style="zoom:50%;" />

在组件中直接使用 `$route` 会使得组件与路由形成高度的耦合，我们希望路由参数能够通过 `props` 传递给组件。首先需要在路由定义中添加 `props` 属性，并声明为 `true`

```javascript {5}
// router/index.js
const routes = [{
    path: '/user/:id',
    component: User,
    props: true
}]
```

接着在 `User` 组件中对 `props` 属性进行声明

```html {10}
// User.vue
<template>
    <div>
        <p>User: {{id}}</p>
    </div>
</template>

<script>
export default {
    props: ['id']
}
</script>
```

## 嵌套路由

假设在 `User` 组件中还嵌套这一层路由

```html
// User.vue
<template>
    <div>
        <p>User: {{$route.params.id}}</p>
        <router-view></router-view>
    </div>
</template>
```

例如当路由为 `/user/:id/foo` 时，`User` 组件中的 `<router-view>` 渲染为 `Foo` 组件，当路由为 `/user:id/bar` 时，渲染为 `Bar` 组件，当路由为 `/user/:id/` 时，渲染为 `UserHome` 组件。为了达到这个目的，我们需要修改 `router/index.js`

```javascript {5-14}
// router/index.js
const routes = [{
    path: '/user/:id',
    component: User,
    children: [{
        path: '',
        component: UserHome
    }, {
        path: 'foo',
        component: Foo
    }, {
        path: 'bar',
        component: Bar
    }]
}]
```

注意到我们添加了 `children` 属性，这是嵌套路由的关键。修改 `App.vue` 中的跳转链接

```html {4-6}
// App.vue
<template>
  <div id="app">
    <router-link to="/user/1/foo">user/1/foo</router-link>
    <router-link to="/user/1/bar">user/1/bar</router-link>
    <router-link to="/user/1/">user/1/</router-link>

    <router-view></router-view>
  </div>
</template>

<style>
a {
  margin-right: 10px;;
}
</style>
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210404153620.gif" alt="4" style="zoom:50%;" />

## 编程式的导航

除了通过链接的方式对路由进行更改，我们还可以通过 `$router` 计算属性更改路由，如

- `$router.push()`
- `$router.replace()`

方法的参数可以为字符串，也可以是一个对象

```javascript
// 字符串
router.push('home')

// 对象
router.push({ path: 'home' })

// 命名的路由
router.push({ name: 'user', params: { userId: '123' }})

// 带查询参数，变成 /register?plan=private
router.push({ path: 'register', query: { plan: 'private' }})
```

```` tip
**注意：如果提供了 `path`，`params` 会被忽略，上述例子中的 `query` 并不属于这种情况。取而代之的是下面例子的做法，你需要提供路由的 `name` 或手写完整的带有参数的 `path`：**

```javascript
const userId = '123'
router.push({ name: 'user', params: { userId }}) // -> /user/123
router.push({ path: `/user/${userId}` }) // -> /user/123
// 这里的 params 不生效
router.push({ path: '/user', params: { userId }}) // -> /user
```
````

`$router.replace` 接收的参数同 `$router.push` 相同，唯一不同在于 `replace` 是替换当前的历史记录，而 `push` 是添加一条历史记录，它的声明式写法为

```html
<router-link :to="..." replace>
```

我们还可以通过 `$router.go` 方法在历史记录之间跳转，该方法接收一个整数 $n$，正数表示向前跳转多少步，负数表示向后跳转多少步

```javascript
// 在浏览器记录中前进一步，等同于 history.forward()
router.go(1)

// 后退一步记录，等同于 history.back()
router.go(-1)

// 前进 3 步记录
router.go(3)

// 如果 history 记录不够用，那就默默地失败呗
router.go(-100)
router.go(100)
```

## 重定向与别名

重定向就是当你访问路由 `/a` 时，会自动跳转到路由 `/b`，如下配置

```javascript
const routes = [{
    path: '/a',
    redirect: '/b'
}]
```

重定向也可以指定为一个命名的路由

```javascript
const routes = [{
    path: '/a',
    redirect: {
        name: 'index'
    }
}]
```

假设 `/b` 是 `/a` 的别名，当我们访问 `/b` 时，路由不变，但是会使用 `/a` 对应的组件，配置如下

```javascript
const routes = [{
    path: '/a',
    component: A,
    alias: '/b'
}]
```

## 路由守卫

当我们访问一个路由前，我们首先是要对身份进行验证，是否有权限访问这个路由，`Vue Router` 提供了路由守卫来做这件事情，路由守卫主要用来跳转或者取消的方式来守卫路由。路由守卫分为三种：

- 全局
- 路由
- 组件

### 全局前置守卫

可以通过 `router.beforeEach` 注册一个全局前置守卫

```javascript
const router = new VueRouter({ ... })

router.beforeEach((to, from, next) => {
  // ...
})
```

当我们进入一个路由之前会触发此守卫方法，该守卫方法接收三个参数：

- `to`：即将要进入的目标 路由对象
- `from`：当前导航正要离开的路由
- `next:`：
  - `next()`：放行
  - `next(false)`：中断当前导航，如果 URL 改变了，则 URL 会被重置到 `from` 路由对应的地址
  - `next('/')` 或者 `next({ path: '/' })`：跳转到一个不同的地址。当前的导航被中断，然后进行一个新的导航。你可以向 `next` 传递任意位置对象，且允许设置诸如 `replace: true`、`name: 'home'` 之类的选项
  - `next(error)`：如果传入 `next` 的参数是一个 `Error` 实例，则导航会被终止且该错误会被传递给 `router.onError()` 注册过的回调

```javascript
router.beforeEach((to, from, next) => {
  if (to.name !== 'Login' && !isAuthenticated) next({ name: 'Login' })
  else next()
})
```

### 路由守卫

可以在 `routes` 中配置路由守卫 `beforeEnter`

```javascript
const routes = [{
    path: '/a',
    component: A,
    beforeEnter: (to, from, next) => {
        // ...
    }
}]
```

方法参数同上。

### 组件内的守卫

也可以直接在组件内部定义路由守卫，有三个：

- `beforeRouteEnter(to, from, next)`：进入该组件时触发，在其中不能获得组件实例 `this`
- `beforeRouteUpdate(to, from, next)`：路由发生改变，且组件被复用时触发，即动态路由
- `beforeRouteLeave(to, from, next)`：离开当前组件对应的路由时触发

## 组件缓存

当我们在不同的路由之间进行切换时，路由对应的组件会被销毁与重建(动态路由组件可能会被复用)，频繁的创建组件可能会对性能带来影响，我们可以组件加上缓存，当再次来到某个路由时，不是重新创建一个组件，而是使用缓存的组件。

为 `<router-view>` 的外层加上 `<keep-alive>` 可以将组件进行缓存，为了验证组件是否有缓存，我们在 `created` 钩子函数中打印消息，如果在多次切换路由时只打印一次消息，说明组件被缓存了，并没有重新创建组件


```html
// Index.vue
<template>
    <div>
        <h1>Welcome to Index page!</h1>
    </div>
</template>

<script>
export default {
    created() {
        console.log('Index.vue created!')
    },
}
</script>
```

```html
// About.vue
<template>
    <div>
        <h1>This is About page!</h1>
    </div>
</template>

<script>
export default {
    created() {
        console.log('About.vue created!')
    },
}
</script>
```

```html {7-9}
// App.vue
<template>
  <div id="app">
    <router-link to="/">Index</router-link>
    <router-link to="/about">About</router-link>

    <keep-alive>
      <router-view></router-view>
    </keep-alive>
  </div>
</template>

<style>
a {
  margin-right: 10px;;
}
</style>
```

```javascript
const routes = [{
    name: 'index',
    path: '/',
    component: Index
}, {
    name: 'about',
    path: '/about',
    component: About
}]
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210404172547.gif" alt="5" style="zoom:50%;" />

当我们多次改变路由时，组件只被创建了一次，可见组件被缓存了。

## 参考链接

- [Vue Router 官方文档](https://router.vuejs.org/zh/guide/)

