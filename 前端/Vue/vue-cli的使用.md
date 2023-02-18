---
tags: [Vue]
---

在之前使用 `Vue` 的过程，我们所有的代码都是放在一个 `script` 标签中，所有的代码放在一起，势必会带来维护上的困难，另外，现如今的软件开发模式，都是多人协作，所有的代码都放在一起，也会给多人协作带来困扰，我们需要更加工程的方式来写 `Vue` 代码。

一般我们要求一个 `Vue` 组件对应一个以 `.vue` 结尾的文件，例如

```html
// Hello.vue
<template>
  <div>
    {{ msg }}
  </div>
</template>

<script>
export default {
  data() {
    return {
      msg: "Hello World!",
    };
  },
};
</script>
```

上面就定义了一个局部组件。

## 安装 vue-cli

对于上述 `.vue` 文件是无法在浏览器显示的，因为浏览器只认识 `html, css, javascript` 文件，所以要使得 `.vue` 文件能够显示在浏览器，我们需要某种工具将上述代码转化为浏览器认识的形式，这就要借助于 `vue-cli`，首先对其进行下载

```bash
npm install -g @vue/cli
```

通过 `vue --version` 可以查看所安装的 `vue-cli` 的版本

```bash
vue --version
@vue/cli 4.5.12
```

我们可以通过 `vue serve` 快速在浏览器显示一个 `.vue` 文件，不过需要先下载一个全局扩展

```bash
npm install -g @vue/cli-service-global
```

接着便可查看一个组件的原型(即在浏览器显示该组件)

```bash
vue serve Hello.vue
```

`vue-cli` 会对 `Hello.vue` 进行一番处理，接着会在控制台打印如下信息

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210401212443.png" alt="image-20210401212443197" style="zoom:50%;" />

我们可以在指定地址 `http://localhost:8080/` 查看该组件的原型，如下

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210401212555.png" alt="image-20210401212555832" style="zoom:50%;" />

## 创建一个项目

### vue-create

可以通过如下命令创建一个 `Vue` 项目

```bash
vue create 项目名
```

接着 `Vue` 便会提示使用何种预设(preset)

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210401213214.png" alt="image-20210401213214590" style="zoom:50%;" />

创建的项目结构如下所示

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210401213459.png" alt="image-20210401213459184" style="zoom:50%;" />

其中 `main.js` 便是入口文件，内容如下

```javascript
import Vue from "vue";
import App from "./App.vue";

Vue.config.productionTip = false;

new Vue({
  render: (h) => h(App),
}).$mount("#app");
```

上面代码做的事情为引入了 `App.vue` 组件，并把该组件挂载在了 `id` 为 `app` 元素上，`App.vue` 的内容如下

```html
<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png" />
    <HelloWorld msg="Welcome to Your Vue.js App" />
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld.vue";

export default {
  name: "App",
  components: {
    HelloWorld,
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```

为了理解上面的内容，我们首先说明一下 `.vue` 文件的组成，一般分为如下三部分

- `template`：定义模板内容
- `script`：定义数据，计算属性，方法，生命周期方法等等
- `style`：定义组件样式

通过 `App.vue` 的内容可以知道，在 `App.vue` 中又引入了 `HelloWorld.vue`，因为 `HelloWorld.vue` 的内容比较复杂，这里就不展示了，我们来看渲染后的页面是什么样子，在命令行输入

```bash
npm run serve
```

项目构建完成之后，我们便可以在 `http://localhost:8080/` 看到渲染后的页面

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210401214614.png" alt="image-20210401214614127" style="zoom:50%;" />

### vue ui

除了通过命令行的方式创建一个项目，我们还可以通过图形化的界面创建一个项目。在命令行中输入 `vue ui`，便会在浏览器打开一个 `UI` 界面

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210401214939.png" alt="image-20210401214939237" style="zoom:50%;" />

点击 `Create` 来创建一个新项目

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210401215201.png" alt="image-20210401215200999" style="zoom:50%;" />

填写完信息后，便会让你选择预设

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210401215324.png" alt="image-20210401215324558" style="zoom:50%;" />

选择完预设后便可创建项目了，创建完项目后便可在可视化页面启动项目

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210401215910.png" alt="image-20210401215909891" style="zoom:50%;" />

## 简单的 Vue 项目

现在我们使用 `.vue` 组件的形式来实现下面的功能

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210401224543.gif" alt="1" style="zoom:50%;" />

这个功能在之前我们已经实现过了，主要分解为一个父组件，两个子组件

- App.vue
  - CourseAdd.vue
  - CourseDisplay.vue

`App.vue` 的内容如下

```html
<template>
  <div id="app">
    <CourseAdd @addCourse="addCourse"></CourseAdd>
    <CourseDisplay :courses="courses"></CourseDisplay>
  </div>
</template>

<script>
import CourseAdd from "./components/CourseAdd";
import CourseDisplay from "./components/CourseDisplay";
export default {
  name: "App",
  components: {
    CourseAdd,
    CourseDisplay,
  },
  data() {
    return {
      courses: ["语文", "数学", "英语"],
    };
  },
  methods: {
    addCourse(course) {
      this.courses.push(course);
    },
  },
};
</script>
```

在 `App.vue` 中导入了 `CourseAdd` 与 `CourseDisplay` 两个组件，并且为 `CourseAdd` 组件绑定了自定义事件，目的是为了将数据由子组件传递到父组件，为 `CourseDisplay` 传递了 `courses` 以做展示。

`CourseAdd.vue` 的内容如下

```html
<template>
  <div>
    <p><input type="text" v-model="course" @keydown.enter="addCourse" /></p>
    <p><button @click="addCourse">添加课程</button></p>
  </div>
</template>

<script>
export default {
  name: "CourseAdd",
  data() {
    return {
      course: "",
    };
  },
  methods: {
    addCourse() {
      // 空格以及空内容不做提交
      if (this.course.trim() === "") {
        return;
      }
      this.$emit("addCourse", this.course);
      this.course = "";
    },
  },
};
</script>

<style>
input {
  outline: none;
  border: none;
  border-bottom: 2px solid black;
  padding: 5px 5px;
}
</style>
```

我们为 `input` 绑定了按键事件，当我们按下 `enter` 键时就会触发 `addCourse` 方法，在 `addCourse` 方法中，我们触发了自定义事件 `addCourse` ，并且向事件传递了数据；或者当我们点击按钮时，也可触发此事件。

`CourseDisplay.vue` 的内容如下

```html
<template>
  <div>
    <ul>
      <li v-for="course in courses" :key="course">
        {{ course }}
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: "CourseDisplay",
  props: {
    courses: Array,
  },
};
</script>
```

代码十分简单，只是将父组件传入的 `courses` 进行展示。

最后在 `main.js` 中引入 `App.vue`

```javascript
import Vue from "vue";
import App from "./App.vue";

Vue.config.productionTip = false;

new Vue({
  render: (h) => h(App),
}).$mount("#app");
```

在命令行输入

```bash
npm run serve
```

即可在浏览器观察到上述动图功能。

## 插件

Vue CLI 使用了一套基于插件的架构。如果你查阅一个新创建项目的 `package.json`，就会发现依赖都是以 `@vue/cli-plugin-` 开头的。插件可以修改 webpack 的内部配置，也可以向 `vue-cli-service` 注入命令。在项目创建的过程中，绝大部分列出的特性都是通过插件来实现的。

我们可以通过 `vue add` 命令为项目添加一个插件

```bash
vue add router
```

``` warning
因为 `vue add` 命令可能修改你现有的文件，所以推荐在安装插件之前，将当前项目的的最新状态提交。
```
后续我们使用的组件库都是通过插件的方式添加的。

``` note
我们也可以在 UI 页面为项目管理插件。
```

## 静态路径

我们可以通过相对路径来引用静态资源文件，例如在 `App.vue` 中引用 `src/assets/logo.png`

```html
<template>
  <div id="app">
    <img src="./assets/logo.png" alt="" />
  </div>
</template>
```

所有通过相对路径引入的资源都会被 `Webpack` 打包，对于图片资源会被放在根目录下面的 `img` 文件夹中，所以上面的引用实际上会被解析为

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210402112055.png" alt="image-20210402112055792" style="zoom:50%;" />

我们也可以通过绝对路径来引入资源文件，因为项目 `public` 中的文件不会经过 `Webpack` 处理，而是会被直接复制到项目的根目录中，所以通过绝对路径引用的资源，就是在 `public` 文件夹中的资源。

```html
<img src="/assets/logo.png" alt="">
```

就相当于访问 `public/assets/logo.png`。

默认情况下，`vue-cli` 假设你是部署在一个域名的根目录下，如 `https://www.app.com/`，但是如果你的项目是部署在一个子路径上，则你就需要使用 `publicPath` 指定这个子路径，例如你的应用被部署在 `https://www.app.com/demo/` 下，则应当将你的 `publicPath` 设置为 `/demo/`

```javascript
// vue.config.js
module.exports = {
  publicPath: "/demo/",
};
```

如果通过绝对路径引入资源，则需要在 `data` 中定义 `publicPath`

```javascript
data() {
  return {
    publicPath: process.env.BASE_URL，
    // ...
  }
},
```

接着在修改引入方式

```html
<img :src="`${publicPath}assets/logo.png`" alt="">
```

> 如果是通过相对路径引入的资源，则不必这么麻烦，`Webpack` 会帮我们除了好一切，我们只需要在 `vue.config.js` 中定义好 `publicPath` 即可，所以尽可能使用相对路径引入资源。

`Webpack` 为我们定义了一个别名，`@`，它表示项目的 `src` 目录，所以我们还可以通过下面的方式引用资源

```html
<img src="@/assets/logo.png" alt="">
```

## CSS

### Scoped CSS

在组件 `style` 标签中定义的 `CSS` 其实是全局 `CSS`，它会对其他组件的样式也会产生影响，例如对于如下两个组件

```html
// Component1.vue
<template>
  <div class="com"></div>
</template>

<script>
export default {
  name: "Component1",
};
</script>

<style>
.com {
  width: 100px;
  height: 100px;
  background-color: aqua;
  margin-bottom: 10px;
}
</style>
```

```html
// Component2.vue
<template>
  <div class="com"></div>
</template>

<script>
export default {
  name: "Component2",
};
</script>
```

上面定义了两个组件，`Component1.vue` 中定义了样式，`Coponent2.vue` 中没有定义样式，但是 `Component1.vue` 中的样式是全局的，所以 `Component2.vue` 组件也会受到影响，在 `App.vue` 中使用者两个组件

```html
// App.vue
<template>
  <div id="app">
    <component-1 />
    <component-2 />
  </div>
</template>

<script>
import Component1 from "./components/Component1";
import Component2 from "./components/Componnet2";
export default {
  name: "App",
  components: {
    Component1,
    Component2,
  },
};
</script>
```

运行 `npm run serve` 查看页面

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210402133949.png" alt="image-20210402133949397" style="zoom:50%;" />

要使得 `Component1` 中的样式不影响其他组件，我们需要为 `style` 标签添加 `scoped` 属性

```html
<style scoped>
.com {
  width: 100px;
  height: 100px;
  background-color: aqua;
  margin-bottom: 10px;
}
</style>
```

这时在观察页面

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210402134236.png" alt="image-20210402134236913" style="zoom:50%;" />

这时发现只有 `Component1.vue` 组件有样式。其原理是会为 `Component1.vue` 组件添加自定义属性

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210402134338.png" alt="image-20210402134338820" style="zoom:50%;" />

上面的组件是 `Component1`，下面的组件是 `Component2`，可见 `Component1` 上有一个自定义属性 `data-v-04421304`，接着使用一个属性选择器使得样式只对 `Component1` 生效

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210402134524.png" alt="image-20210402134524797" style="zoom:50%;" />

### Module CSS

另一种比较流行的方案是 `CSS in JS`，也就是 `Module CSS`，首先我们需要为 `style` 标签添加 `module` 属性

```html
<style module>
.com {
  width: 100px;
  height: 100px;
  background-color: aqua;
  margin-bottom: 10px;
}
</style>
```

添加 `module` 属性后，组件中便会有 `$style` 这个计算属性，它是一个对象，我们可以通过 `$style.类名` 的方式为组件添加样式，例如

```html
<template>
  <div :class="$style.com"></div>
</template>
```

实际的类名为

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210402140435.png" alt="image-20210402140435126" style="zoom:50%;" />

`Component1_com_2ata3`，它的组成为 `组件名_类名_哈希串`。

`Module CSS` 相对于 `Scoped CSS` 较为灵活

```html
<div :class="$style.com">Hello World</div>
<div :class="[$style.com, $style.red]">Hello World</div>
<div :class="{ [$style.red]: isRed }">Hello World</div>
```

可以通过数据来控制是否添加类名，从而控制样式，以及我们还可以在 `JS` 中访问 `$style`

```javascript
created() {
    console.log(this.$style);
}
```

```info
`Module CSS` 与 `Scoped CSS` 并无高下之分，哪个用起来舒服便使用哪个。
```

## 参考文献

- [Vue Cli 官网文档](https://cli.vuejs.org/zh/guide/)
