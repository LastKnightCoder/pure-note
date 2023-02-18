---
tags: [Vue]
---

本篇文章的目的是快速的入门 Vue.js，力求能够通过本篇文章了解 Vue.js 的基本用法，但是并不会覆盖 Vue.js 的方方面面，在读完本篇文章之后，便可阅读 Vue.js 的官方教程，进一步的了解 Vue 的进阶内容。

## Vue 带来的开发思维的改变

假设有这么一个要求，要我们根据一个数组渲染出一个列表，对于下面的数组

```javascript
const courses = ["语文", "数学", "英语"];
```

要求渲染出下面的 HTML 结构

```html
<ul>
  <li>语文</li>
  <li>数学</li>
  <li>英语</li>
</ul>
```

所以我们会写出这样的代码

```javascript
const courses = ["语文", "数学", "英语"];

const ulEle = document.createElement("ul");
courses.forEach((item) => {
  const liEle = document.createElement("li");
  liEle.innerText = item;
  ulEle.append(liEle);
});

document.body.append(ulEle);
```

如果数组的内容发生改变，我们还需要**手动**更新上述的 HTML 结构。所以传统的开发方式为更新数据，根据数据操作 DOM 树。

那么使用 Vue 会有什么不同，来看一个使用 Vue 的方式来实现上述的功能

```html
<div id="app">
  <ul>
    <li v-for="course in courses" :key="course">{{course}}</li>
  </ul>
</div>
<script src="vue.js"></script>
<script>
  new Vue({
    el: "#app",
    data() {
      return {
        courses: ["语文", "数学", "英语"],
      };
    },
  });
</script>
```

上面的程序可能看不懂，毕竟还没有开始学，所以不必担心。从代码量上看，二者似乎相差不大，可能还有点多，但是当我们更新数组时，我们不必操作 DOM 树来更新页面了，当数据发生变化时，Vue 自动地帮我们更新页面，这种模式我们称为 MVVM，其中的 V 表示 View，表示的视图，而 M 表示 Model，表示的是数据，而 `Vue` 充当的角色是 VM，它将数据与视图进行了绑定，当数据发生变化时，视图也会相应的**自动**更新，如下图

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210325225532.png" style="zoom:50%;" />

所以 Vue 给我们带来开发思维上的改变就是，**我们只需要操作数据即可，更新页面的工作 Vue 会自动的帮我们完成**

## Hello World

### 安装 Vue

安装 Vue 当然是选择[官网](https://cn.vuejs.org/js/vue.js)了，当然我们也可以选择通过 CDN 引入文件，例如

```javascript
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
```

### Hello World

下面就是经典的 Hello World 程序，新建一个 HTML 文件，内容如下

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/carbon2022-06-09-20-44-50.svg" />

<!-- ```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <div id="app">{{message}}</div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
      const vm = new Vue({
        el: "#app",
        data() {
          return {
            message: "Hello World!",
          };
        },
      });
    </script>
  </body>
</html>
``` -->

页面上显示的内容如下：

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210326190835.png" style="zoom:50%;" />

### 发生了什么

我们先简单的捋一捋发生了什么，一切先从下面的代码说起

```javascript
const vm = new Vue({
  el: "#app",
  data() {
    return {
      message: "Hello World!",
    };
  },
});
```

上面我们创建了一个 Vue 的实例 `new Vue()`，并且向其中传入一个对象，包括这么两个属性：

- el：绑定的 DOM 元素
- data：返回一个对象的函数，返回对象中的数据与视图进行了绑定，当修改数据时，相应的视图也会发生更新

当我们创建一个 Vue 实例时，首先它会根据 el 属性指定的选择器找到相应的 DOM 元素，我们称这个 DOM 结果为模板(template)，例如上例根据 `#app` 选择器我们找到

```html
<div id="app">{{message}}</div>
```

接着便会解析该模板，例如上面将 `{{message}}` 替换为了 `data` 中定义的数据 `message` 的对应的值，即 `Hello World!`，解析完毕后便会渲染页面，我们便在页面上看到了 `Hello World!`。

因为 `Vue` 对 `data` 属性中定义的数据进行了拦截，一旦我们改变对象属性的值，Vue 便会对用到该属性的模板进行解析、渲染。

> 拦截数据是通过 `Object.definePropety()` 来做到的，可以通过 [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty) 来了解该方法的使用。

### Vue 实例

上面我们使用变量 `vm` 接收了 `new Vue()` 返回的 `Vue` 实例，现在我们看看里面有什么。

#### $el

模板经过 `Vue` 解析、渲染以后，然后根据该模板生成一个 DOM 元素挂载在页面中，而这个 DOM 元素我们可以通过 `vm.$el` 进行访问得到

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210326204150.png" style="zoom:50%;" />

#### $data

通过 `vm.$data` 可以得到 `data` 属性返回的对象

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210326204437.png" style="zoom:50%;" />

我们可以通过 `vm.$data` 来修改数据

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210326204658.png" style="zoom:50%;" />

上面我们修改数据 `message` 为 `Hello Vue`，页面便发生了变化，进一步证实了数据与视图的绑定。

为了方便通过 `vm` 操作数据，所有的数据都被挂载到了 `vm` 上，即我们可以直接通过 `vm` 访问以及修改数据，而不必通过 `vm.$data`

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210326204947.png" style="zoom:50%;" />

上面我们通过 `vm.message` 直接修改了数据，页面也立即发生了变化。

> 无论我们是通过 `vm.message` 还是 `vm.$data.message` 修改数据，它们之间是互相影响的。即当我们通过 `vm.xxx` 修改数据，那么 `vm.$data.xxx` 的值也会发生改变，反之亦然。

## 模板语法

本小节主要讲如何在模板中引用在 `data` 属性中定义的数据。

### 插值

在模板中通过 `{{}}` 插值语法便可引用在 `data` 中定义的数据，正如上例中的 `{{message}}`，除此以外，`{{}}` 内部可以是任何的 JavaScript 表达式，如

```javascript
{{ 1 + 2 }}
{{ message === "xxx" ? "foo" : "bar"}}
{{ message.split("").reverse().join("") }}
```

`{{}}` 中的内容会被解析，然后被替换为相应的内容。

### 指令

#### v-bind

如果我们想让模板中的属性与数据进行动态绑定，我们便需要借助于 `v-bind` 属性，有下面的模板以及数据

```html
<div id="app">
  <p title="message">Hello World</p>
</div>
```

```javascript
const vm = new Vue({
  el: "#app",
  data() {
    return {
      message: "Hello World!",
    };
  },
});
```

当我们将鼠标放置在 `p` 标签上时，显示的 `title` 是 `message`，而不是数据 `Hello World!`，说明 `title` 属性并没有与 `message` 进行绑定，因为图比较难截，所以自己试验一下。不过从渲染后的 DOM 元素可以证明这一点

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210326205826.png" style="zoom:50%;" />

我们希望 `p` 标签的 `title` 属性与 `data` 中定义的 `massage` 进行绑定，我们只需要在属性 `title` 签名加上 `v-bind:` 即可

```html
<div id="app">
  <p v-bind:title="message">Hello World</p>
</div>
```

这时将鼠标放在 `p` 标签，这时显示的便是 `Hello World!`，从渲染后的 DOM 元素可以证明这一点

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210326210212.png" style="zoom:50%;" />

并且这时我们对数据进行更改，相应的数据也会发生变化

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210326210323.png" style="zoom:50%;" />

因为 `v-bind:` 比较常用，所以它有一个缩写 `:`，上述模板可以改为

```html
<div id="app">
  <p :title="message">Hello World</p>
</div>
```

`class` 属性与 `style` 属性也可以使用 `v-bind` 绑定属性，不过类与样式实在太过特殊，所以 Vue 对其有做一些特殊的扩展，可以参见[官网](https://cn.vuejs.org/v2/guide/class-and-style.html)。

#### v-on

指令 `v-on` 可以绑定一个事件

```html
<div id="app">
  <p v-on:click="clear">{{message}}</p>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
  const vm = new Vue({
    el: "#app",
    data() {
      return {
        message: "Hello World!",
      };
    },
  });
</script>
```

在上面我们为 `p` 标签添加了一个点击事件 `v-on:click="clear"`，当我们点击 `p` 标签时会触发一个叫 `clear` 的方法，该方法需要在 `methods` 选项中进行定义，如下

```javascript
const vm = new Vue({
  el: "#app",
  data() {
    return {
      message: "Hello World!",
    };
  },
  methods: {
    clear() {
      this.message = "";
    },
  },
});
```

`clear` 的方法会将 `message` 数据设置为空字符串

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210327202201.gif" style="zoom:50%;" />

当我们点击 `p` 标签时，`message` 数据变为空字符串，相应的页面也会发生改变。

绑定事件也是一个很常见的操作，所以也有缩写，上面的 `v-on:` 可以替换为 `@`

```html
<div id="app">
  <p @click="clear">{{message}}</p>
</div>
```

除了使用 `v-on` 指令绑定 `methods` 中的事件，除此之外，我们也可以内联操作数据

```html
<div id="app">
  <p>
    <!-- 直接内联修改数据 -->
    <button @click="message = 'Hello'">Hello</button>
    <button @click="message = 'Hi'">Hi</button>
  </p>
  <p>{{message}}</p>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
  const vm = new Vue({
    el: "#app",
    data() {
      return {
        message: "Hello Vue!",
      };
    },
  });
</script>
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210329131030.gif" alt="3" style="zoom:50%;" />

> 在内联的写法中，我们可以通过 `$event` 访问到 `event` 事件对象。

在事件处理程序中调用 `event.preventDefault()` 或 `event.stopPropagation()` 是非常常见的操作，Vue 为 `v-on` 提供了事件修饰符

- `.stop`：阻止事件继续传播
- `.prevent`：阻止默认行为
- `.capture`：使用捕获模式
- `.once`：事件只执行一次

在监听键盘事件时，我们一般需要检测按下了哪个键，Vue 可以在监听事件时添加按键修饰符

```html
<input @keyup.enter="submit" />
```

处理函数只会在 `event.key` 等于 `enter` 被调用，Vue 提供如下按键修饰符

- `.enter`
- `.tab`
- `.delete`
- `.esc`
- `.space`
- `.up`
- `.down`
- `.left`
- `.right`

在监听鼠标点击事件时，有时也会判断按下了鼠标的哪个按钮，Vue 也提供了相应的鼠标修饰符

- `.left`
- `.right`
- `.middle`

在日常的使用，我们经常使用快捷键进行快捷操作，设置快捷键一般需要系统按键符配合，如 `ctrl`，因此 `Vue` 也为我们提供了系统按键符，包括

- `.ctrl`
- `.alt`
- `.shift`

```html
<!-- Alt + C -->
<input @keyup.alt.67="clear" />

<!-- Ctrl + Click -->
<div @click.ctrl="doSomething">Do something</div>
```

`.exact` 修饰符允许你控制由精确的系统修饰符组合触发的事件

```html
<!-- 即使 Alt 或 Shift 被一同按下时也会触发 -->
<button v-on:click.ctrl="onClick">A</button>

<!-- 有且只有 Ctrl 被按下的时候才触发 -->
<button v-on:click.ctrl.exact="onCtrlClick">A</button>

<!-- 没有任何系统修饰符被按下的时候才触发 -->
<button v-on:click.exact="onClick">A</button>
```

#### v-if、v-show

`v-if` 和 `v-show` 指令可以控制元素是否渲染

```html
<div id="app">
  <p v-if="show">{{message}}</p>
  <button @click="toggle">toggle</button>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
  const vm = new Vue({
    el: "#app",
    data() {
      return {
        message: "Hello World!",
        show: true,
      };
    },
    methods: {
      toggle() {
        this.show = !this.show;
      },
    },
  });
</script>
```

上面我们为 `p` 标签使用了 `v-if` 指令，指定了它的值为 `show`，当数据 `show` 为 `true` 才会显示标签 `p`，为 `false` 时则不会显示。在页面中还有一个按钮，当点击按钮时，会触发 `toggle` 方法，而该方法是将 `show` 进行取反，也就是说当我们点击按钮时，如果 `p` 标签显示，那么点击按钮时它会从页面消失，反之亦然

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210327204302.gif" style="zoom:50%;" />

使用 `v-show` 也会达到相同的效果，我们将模板中的 `v-if` 修改为 `v-show`

```html
<div id="app">
  <p v-show="show">{{message}}</p>
  <button @click="toggle">toggle</button>
</div>
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210327204536.gif" style="zoom:50%;" />

那么二者有何不同? `v-show` 是控制元素的 `display` 属性来控制元素是否显示与否的，而 `v-if` 则是从 `DOM` 树上移除元素或者添加元素，并且 `v-if` 是惰性的，所谓惰性指的是如果一开始渲染条件为 `false`，那么什么也不做，只有当条件第一次为真时，才会开始渲染。

所以当元素频繁切换时，`v-if` 每次都需要创建元素，相对于 `v-show` 来说有较大的开销，所以对于这种场景，我们选择 `v-show`，如果在运行时条件很少改变，因此 `v-if` 相当于 `v-show` 有**更低的初始渲染开销**，所以这时我们选择 `v-if`。

另外 `v-if` 可以与 `v-else-if` 以及 `v-else` 配合使用，参考下面的代码

```html
<div id="app">
  <p v-if="count % 3 === 0">{{count}}: 3n</p>
  <p v-else-if="count % 3 === 1">{{count}}: 3n + 1</p>
  <p v-else>{{count}}: 3n + 2</p>
  <button @click="inc">INC</button>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
  const vm = new Vue({
    el: "#app",
    data() {
      return {
        count: 0,
      };
    },
    methods: {
      inc() {
        this.count++;
      },
    },
  });
</script>
```

在 `data` 数据中，我们定义了一个 `count` 变量，并且定义了一个方法 `inc`，该方法对数据 `count` 进行递增。在模板中

```html
<p v-if="count % 3 === 0">{{count}}: 3n</p>
<p v-else-if="count % 3 === 1">{{count}}: 3n + 1</p>
<p v-else>{{count}}: 3n + 2</p>
```

我们对三个标签使用了 `v-if` `v-else-if` `v-else` 三个指令，根据 `count` 对 `3` 余数的值来决定显示哪一个

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210327214103.gif" alt="4" style="zoom:50%;" />

#### v-for

假设有一个数组数据，我们需要将它渲染为一个列表，尝试如下写法

```html
<div id="app">
  <ul>
    <li>{{courses[0]}}</li>
    <li>{{courses[1]}}</li>
    <li>{{courses[2]}}</li>
  </ul>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
  const vm = new Vue({
    el: "#app",
    data() {
      return {
        courses: ["语文", "数学", "英语"],
      };
    },
  });
</script>
```

虽然上面的代码可以达到我们的功能，但是实在不够灵活：

1. 数据发生改变时，页面不能发生改变，例如像 `courses` 数组中添加元素时，页面的内容不会发生改变
2. 代码重复，上面我们是一项一项的写出要显示的内容，如果数组很大，有一万个元素，难道我们要写一万行吗，不仅难以维护，而且很丑

使用 `v-for` 可以轻易的解决上述的问题，我们修改模板代码如下

```html
<div id="app">
  <ul>
    <li v-for="course in courses" :key="course">{{course}}</li>
  </ul>
</div>
```

我们在 `li` 标签上面添加了 `v-for` 指令，`Vue` 会帮我们从数组中取出元素，然后对于每一个元素都渲染出一个 `li` 标签，这样一来，当数组发生改变时，页面也会相应的改变，另外，无论数组中有多少项，代码都不需要更改。

> 仔细观察，我们还为 `li` 标签绑定了一个 `key` 属性，它是作为 `li` 属性的唯一标识，那么它有什么用呢?
>
> 假设我们向更改了数组(增加、删除、改变元素的值)，我们需要重新渲染所所有的 `li` 标签吗? 当然不需要，我们只要渲染那些更改了 `li` 标签就可以了，而识别哪些标签更改了，正是需要通过 `key` 属性办到，所以当我们使用 `v-for` 指令时，最好同时设置 `key` 属性。

官网推荐不要同时使用 `v-for` 与 `v-if` 指令，如果同时使用了 `v-for` 与 `v-if`，那么 `v-for` 的优先级更高。

#### v-model

`v-model` 指令一般用于表单，它可以将 `data` 中定义的数据与表单的 `value` 值进行双向绑定，所谓的双向绑定指的是：

1. 数据改变，表单的值也会发生变化
2. 表单接收用户的输入，表单的值发生改变，使得数据也会发生改变

```html
<div id="app">
  <p><input type="text" v-model="message" /></p>
  <p><button @click="reverse">reverse</button></p>
  {{message}}
</div>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
  const vm = new Vue({
    el: "#app",
    data() {
      return {
        message: "",
      };
    },
    methods: {
      reverse() {
        this.message = this.message.split("").reverse().join("");
      },
    },
  });
</script>
```

上面我们在 `input` 中使用了 `v-model` 指定，它与数据 `message` 进行了绑定，这就意味着当我们输入字符时，数据 `message` 会发生改变，当我们改变数据 `message` 时，输入框中的内容也会发生改变

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210328102741.gif" alt="2" style="zoom:50%;" />

`v-model` 其实就是一个语法糖

```html
<input type="text" v-model="message" />
```

相当于

```html
<input type="text" :value="message" @input="message = $event.target.value" />
```

## 计算属性与监听器

### 计算属性

我们可以在模板的插值中使用任何的 `JavaScript` 表达式，这可以使得我们的代码更加的灵活

```html
<div id="app">
  <p>{{message.split('').reverse().join('')}}</p>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
  const vm = new Vue({
    el: "#app",
    data() {
      return {
        message: "Hello World",
      };
    },
  });
</script>
```

在上面的代码中，我们在模板的插值中使用了一个较为复杂的表达式，可能需要一定的时间才能明白我们做的事情：翻转字符串。在模板中使用比较复杂的插值表达式，会使得模板过重，难以维护，我们可以使用计算属性来完成上面的目的

```html
<div id="app">
  <p>message: {{message}}</p>
  <p>reversedMessage: {{reversedMessage}}</p>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
  const vm = new Vue({
    el: "#app",
    data() {
      return {
        message: "Hello World",
      };
    },
    computed: {
      reversedMessage() {
        return this.message.split("").reverse().join("");
      },
    },
  });
</script>
```

在创建 `Vue` 实例时，我们新增了一个 `computed` 属性，该属性包含多个方法，这些方法我们称为计算属性，我们可以直接在模板中直接引用这些属性，例如我们在 `p` 标签中直接引用了该计算属性

```html
<p>reversedMessage: {{reversedMessage}}</p>
```

当 `Vue` 解析模板时，会使用 `reversedMessage` 方法的返回值来替代模板中引用的计算属性。在计算属性中我们依赖了在 `data` 中定义的数据，当 `data` 中的数据发生改变时，计算属性也会相应的发生改变，所以计算属性也是与视图进行绑定的。

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210328100839.png" alt="image-20210328100839231" style="zoom:50%;" />

另外值得一提的是，计算属性是有缓存的，如果在多处访问计算属性，只会调用一次 `computed` 中定义的方法，然后将其结果缓存起来 ，如果在模板中有多处使用计算属性，除了第一次需要计算，后面直接使用缓存即可，所以对于一些计算复杂，耗时的任务我们便可以使用计算属性

```html
<div id="app">
  <p>reversedMessage: {{reversedMessage}}</p>
  <p>reversedMessage: {{reversedMessage}}</p>
  <p>reversedMessage: {{reversedMessage}}</p>
  <p>{{count}}</p>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
  const vm = new Vue({
    el: "#app",
    data() {
      return {
        message: "Hello World",
        count: 0,
      };
    },
    computed: {
      reversedMessage() {
        this.count++;
        return this.message.split("").reverse().join("");
      },
    },
  });
</script>
```

在上面的代码中，我们在模板中引用了三次计算属性

```html
<p>reversedMessage: {{reversedMessage}}</p>
<p>reversedMessage: {{reversedMessage}}</p>
<p>reversedMessage: {{reversedMessage}}</p>
```

并且我们新增了一个数据项 `count`，当我们每次调用 `computed` 属性中的 `reversedMessage` 便会对 `count` 进行递增，虽然我们在模板中引用了三次计算属性，但是因为计算属性有缓存，所以 `count` 的计数应该为 1，页面显示如下

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210328100526.png" alt="image-20210328100526820" style="zoom:50%;" />

计算属性也会被挂载到 `Vue` 实例 `vm` 上，所以我们可以直接通过 `vm.xxx` 的形式访问计算属性

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210328101008.png" alt="image-20210328101008233" style="zoom:50%;" />

### 监听器

#### handler

在创建 `Vue` 实例时，我们还可以添加一个选项 `watch`，它可以监听 `data` 中定义的数据，当监听的数据发生变化时，便会执行相应的操作

```html
<div id="app">
  <p><input type="text" v-model="question" /></p>
  {{message}}
</div>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
  const vm = new Vue({
    el: "#app",
    data() {
      return {
        question: "",
        message: "",
      };
    },
    watch: {
      question: {
        handler() {
          this.message = this.question.split("").reverse().join("");
        },
      },
    },
  });
</script>
```

上面的代码我们监听了数据 `question`

```javascript
watch: {
    question: {
        handler() {
            this.message = this.question.split('').reverse().join('')
        }
    }
}
```

当我们向输入框输入数据时，`question` 就会发生改变，就会执行上述 `question` 中的 `handler` 方法，在这个方法中我们将数据 `message` 设置为 `question` 的翻转

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210328111522.gif" alt="3" style="zoom:50%;" />

监听器还可以接收两个参数，分别为更新前的值与更新后的值

```javascript
watch: {
    question: {
        handler(oldValue, newValue) {
            this.message = this.question.split('').reverse().join('')
        }
    }
}
```

如果监听的数据只有 `handler` 方法，则可以简写为如下

```javascript
watch: {
    question() {
        this.message = this.question.split('').reverse().join('')
    }
}
```

#### immediate

继续看一个很简单的代码

```html
<div id="app">
  <p>{{message}}</p>
  <p>{{reversedMessage}}</p>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
  const vm = new Vue({
    el: "#app",
    data() {
      return {
        message: "Hello World!",
        reversedMessage: "",
      };
    },
    watch: {
      message() {
        this.reversedMessage = this.message.split("").reverse().join("");
      },
    },
  });
</script>
```

上面的代码很简单，我们设置了两个属性 `message` 与 `reversedMessage`，并且我们监听了 `message`，当 `message` 发生变化时，便会将 `reversedMessage` 设置为 `message` 的翻转。此时的页面显示为

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210328112812.png" alt="image-20210328112812520" style="zoom:50%;" />

`reversedMessage` 的内容为空，并不是 `message` 的翻转，这是因为只有当 `message` 发生变化时，`watch` 中的方法才会执行。如果我们希望立即执行，那么我们便要设置 `immediate` 参数为 `true`

```javascript
watch: {
    message: {
        immediate: true,
        handler() {
            this.reversedMessage = this.message.split('').reverse().join('')
        }
    }
}
```

此时页面显示为

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210328113030.png" alt="image-20210328113030563" style="zoom:50%;" />

#### deep

当我们监听一个对象或数组时，可能需要用到 `deep` 属性。考虑下面的场景

```html
<div id="app">
  <p @click="changeName">username: {{user.username}}</p>
  <p>gender: {{user.gender}}</p>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

<script>
  const vm = new Vue({
    el: "#app",
    data() {
      return {
        user: {
          username: "Alice",
          gender: "female",
        },
      };
    },
    methods: {
      changeName() {
        this.user.username = "Bob";
      },
    },
    watch: {
      user: {
        handler() {
          this.user.gender = "male";
        },
      },
    },
  });
</script>
```

在 `data` 中我们定义了一个数据 `user`，它是一个对象，包含两个字段 `username` 和 `gender`；在模板中，我们使用插值语法使用了这两个数据，并且当我们点击 `username` 时，会修改 `username` 的值；在 `watch` 中我们监听了 `user`，当 `user` 发生改变时，会设置 `gender` 的值

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210328123613.gif" alt="1" style="zoom:50%;" />

上面我们点击 `username` 时，`username` 的值的确发生了改变，但是 `gender` 并没有发生改变，这是为什么? 这个也很容易理解，我们使用 `watch` 监听 `user`，但是当我们修改 `user.username` 的时候，`user` 根本没有发生改变，如果我们希望修改对象的属性也能被监听到，那么我们应该设置 `deep` 属性为 `true`

```javascript
watch: {
    user: {
        deep: true,
        handler() {
            this.user.gender = 'male'
        }
    }
}
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210328124118.gif" alt="2" style="zoom:50%;" />

> 计算属性与监听器：
>
> - 监听器的作用是监听数据的变化，然后触发一个行为，例如异步操作、请求数据
> - 计算属性是根据当前数据计算得到一个值
>
> 由于计算属性具有缓存功能，所以我们一般考虑使用计算属性。

## 生命周期

对于一个 `Vue` 实例，从创建到被销毁，会经历一系列的阶段，就如同人一样，从出生到死亡，会经历幼年、青年、中年、老年等一系列的阶段。`Vue` 为我们提供了一些钩子函数，例如 `beforeCreate` 函数，当初始化数据之前会调用这个方法，又如 `created` 函数，当初始化数据之后会调用这个函数。

`Vue` 实例的完整生命周期如下图所示，现在你不需要弄懂所有的东西，但随着对 `Vue` 使用的深入，它的参考价值会越来越高

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210328130008.png" alt="lifecycle" style="zoom:50%;" />

上图牵涉到如下钩子函数：

- beforeCreate：初始化数据之前调用该函数，在该函数中不能通过 `vm.xxx` 访问数据
- created：初始数据之后调用该函数，这时可以访问到数据
- beforeMount： 渲染 `$el` 之前调用该函数
- mounted：渲染 `$el` 之后调用该函数
- beforeUpdate：数据更新之前执行该函数
- updated：数据更新之后执行该函数
- beforeDestroy：Vue 实例销毁之前执行该函数
- destroyed：Vue 实例销毁之后执行该函数

```html
<div id="app">{{message}}</div>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
  const vm = new Vue({
    el: "#app",
    data() {
      return {
        message: "Hello Vue!",
      };
    },
    beforeCreate() {
      // 数据未初始化，无法访问
      console.log("在 beforeCreate 中访问数据：", this.message); // undefined
    },
    created() {
      // 数据初始化完毕，可以访问
      console.log("在 created 中访问数据：", this.message); // Hello Vue!
      // 此时还没创建 $el，所以无法访问到 $el
      console.log("在 created 中访问 $el", this.$el); // undefined
    },
    beforeMount() {
      // 模板语法中的表达式未被渲染
      console.log("在 beforeMount 中访问 $el", this.$el);
    },
    mounted() {
      // 模板语法中的表达式已经被替换为了数据
      console.log("在 mounted 中访问 $el", this.$el);
    },
  });
</script>
```

上述代码中的钩子函数会按照

- beforeCreate
- created
- beforeMount
- mounted

的顺序执行，控制台打印结果如下

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210328132440.png" alt="image-20210328132440613" style="zoom:50%;" />

当我们更新数据的时候，会重新渲染页面，在渲染页面之前会调用 `beforeUpdate` 函数，渲染之后会调用 `updated` 函数(这两个钩子函数的用处不大，很少使用)。

> 经过实验我发现**无法在 `updated` 钩子函数中访问 `$el`。**

比较常用的钩子函数有两个：

- created：这里可以向后端请求数据
- mounted：在这里可以操作 DOM 元素

## 组件

一个页面一般由很多个部分组成，例如 `header` `main` `footer` 等部分

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210328140752.png" alt="components" style="zoom:50%;" />

我们把这些部分称之为组件，通过使用组件搭建页面，就像搭积木一样搭建页面，组件可以在不同的页面直接复用(包括样式以及逻辑)，复用性得到了增强，并且易于维护。

### 定义组件、使用组件

组件分为两种：

- 全局组件
- 局部组件

通过 `Vue.component` 可以构建一个全局组件，下面给出一个示例

```javascript
Vue.component("my-list", {
  template: `<ul><li v-for="c in courses" :key="c">{{c}}</li></ul>`,
  data() {
    return {
      courses: ["语文", "数学"],
    };
  },
  methods: {},
});
```

第一个参数 `my-list` 是组件的名称，我们可以在其他组件中通过

```html
<my-list></my-list>
```

使用该组件。第二个参数是一个对象，包含如下选项：

- template：该组件对应的模板
- data：组件包含的数据
- methods：组件包含的方法
- ...

该对象需要设置的字段与我们创建 `Vue` 实例设置的字段差不多。下面给出一个使用该组件的例子

```html
<div id="app">
  <!-- 使用组件 -->
  <my-list></my-list>
</div>

<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
  // 创建组件
  Vue.component("my-list", {
    template: `<ul><li v-for="c in courses" :key="c">{{c}}</li></ul>`,
    data() {
      return {
        courses: ["语文", "数学", "英语"],
      };
    },
    methods: {},
  });

  const vm = new Vue({
    el: "#app",
  });
</script>
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210328142601.png" alt="image-20210328142540521" style="zoom:50%;" />

我们还可以通过如下声明一个局部组件

```javascript
const myList = {
  template: `<ul><li v-for="c in courses" :key="c">{{c}}</li></ul>`,
  data() {
    return {
      courses: ["物理", "化学", "生物"],
    };
  },
};
```

要使用局部组件，需要事先在 `components` 属性进行声明

```javascript
const vm = new Vue({
  el: "#app",
  components: {
    "my-list": myList,
  },
});
```

这样才可以在模板中使用组件 `my-list`

```html
<div id="app">
  <my-list></my-list>
</div>
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210328143258.png" alt="image-20210328143258065" style="zoom:50%;" />

### props

在上面我们创建一个 `my-list` 组件，`my-list` 组件展示的数据是在其内部的 `data` 中定义的，但是它作为一个通用组件，它展示的内容应该由使用该组件的组件传入，本节讲述的便是如何向组件传入数据。

> **父子组件**
>
> 如果一个组件 `A` 在其内部使用了组件 `B` ，那么我们就称 `A` 是 `B` 的父组件，`B` 是 `A` 的子组件。

#### 传递数据

如果我们想要给组件 `my-list` 传入数据，我们需要通过属性 (props) 进行传入

```html
<div id="app">
  <!-- 传递数据 -->
  <my-list :courses="courses"></my-list>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
  Vue.component("my-list", {
    // 在 props 中声明接收的数据
    props: ["courses"],
    template: `<ul><li v-for="c in courses" :key="c">{{c}}</li></ul>`,
  });

  const vm = new Vue({
    el: "#app",
    data() {
      return {
        courses: ["语文", "数学", "英语"],
      };
    },
  });
</script>
```

在上面的代码中，我们通过为 `my-list` 组件的 `courses` 属性绑定数据来传递数据，而在 `my-list` 组件中要是用传入的数据，则需要在它的 `props` 属性中进行声明，这样才可以使用传入的数据。

> **传递一个对象的所有属性**
>
> 如果你想要将一个对象的所有属性都作为 prop 传入，你可以使用不带参数的 `v-bind` (取代 `v-bind:prop-name`)。例如，对于一个给定的对象 `post`：
>
> ```javascript
> post: {
>   id: 1,
>   title: 'My Journey with Vue'
> }
> ```
>
> 下面的模板：
>
> ```html
> <component v-bind="post"></component>
> ```
>
> 等价于
>
> ```html
> <component :id="post.id" :title="post.title"></component>
> ```

#### 类型检查

`props` 除了可以是一个数组以外，还可以是一个对象

```javascript
props: {
  courses: Array;
}
```

上面的意思是，接收到的 `courses` 属性必须为数组。除此之外还可以对传入参数做更多的验证

```javascript
props: {
    courses: {
        type: Array,
        required: true,
        // 对象或数组默认值必须从一个函数返回
        default: function() {
            return ['物理', '化学', '生物'];
        },
        validator(value) {
            return value.length > 2
        }
    }
}
```

- type：规定传入属性的类型，可以有如下取值
  - String
  - Number
  - Boolean
  - Array
  - Object
  - Date
  - Function
  - Symbol
  - Promise
- required：是否是必须的
- default：如果不传入数据，则使用该默认值
- validator：函数，对传入的数据进行校验

如果传入的数据没有通过验证的话，就会在控制台打印错误，例如我们不向组件 `my-list` 传入数据

```html
<my-list></my-list>
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210328194957.png" alt="image-20210328194957209" style="zoom:50%;" />

因为没有传入数据，所以使用默认值，并且因为我们要求必须传入数据 `required: true`，所以在控制台给出了一个警告。

> 注意：
>
> 1. `props` 参数的验证是在实例化组件之前，因此无法在 `default` 和 `validator` 中访问组件中定义的数据(如 `data` `computed` 等)
> 2. 注意在 JavaScript 中对象和数组是通过引用传入的，所以对于一个数组或对象类型的 `prop` 来说，在子组件中改变变更这个对象或数组本身**将会**影响到父组件的状态，所以不要在子组件中修改 `prop`

#### $attrs

如果我们向组件传入了一个属性，但是在组件中并没有在 `props` 中声明这个属性，那么这个属性会被默认添加到组件的根元素上

```javascript
Vue.component("my-list", {
  template: `<div><p>my-list</p></div>`,
});
```

如上我们定义了一个全局组件，它没有在 `props` 中声明需要接收的数据，如果我们向其传递数据，那么传递的数据会被放在根元素上，也就是 `div` 元素上

```html
<div id="app">
  <my-list :title="title"></my-list>
</div>
```

```javascript
const vm = new Vue({
  el: "#app",
  data() {
    return {
      title: "Vue",
    };
  },
});
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210328201744.png" alt="image-20210328201744565" style="zoom:50%;" />

如果不希望根元素直接继承属性，那么可以在组件的选项中设置 `inheritAttrs: false`

```javascript
Vue.component("my-list", {
  template: `<div><p>my-list</p></div>`,
  inheritAttrs: false,
});
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210328201836.png" alt="image-20210328201836371" style="zoom:50%;" />

这时我们发现根元素上没有继承传入的属性了。但是我们可以通过 `$attrs` 获得父组件传入的属性，它是一个对象，我们可以自己决定将属性赋予哪个元素

```javascript
Vue.component("my-list", {
  template: `<div><p v-bind="$attrs">my-list</p></div>`,
  inheritAttrs: false,
});
```

上面我们决定将所有接收到的且未在 `props` 中声明的属性全部放在 `p` 标签上

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210328202103.png" alt="image-20210328202103131" style="zoom:50%;" />

> 注意：在 `props` 中已经声明的属性不会在 `$attrs` 中出现。

### 自定义事件

上面讲述了父组件向子组件传递数据，那么子组件如何向父组件传递数据呢? 假设有一个父组件，它维护着一个 `courses` 的数组，它有两个子组件

- course-add：用以向 `courses` 中添加一个数据
- course-display：用以展示 `courses` 中的数据

```html
<div id="app">
  <course-add></course-add>
  <course-display :courses="courses"></course-display>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
  Vue.component("course-add", {
    template: `
			<div>
				<p><input type="text" v-model="course"></input></p>
				<p><button @click="add">添加课程</button></p>
    		</div>
		`,
    data() {
      return {
        course: "",
      };
    },
    methods: {
      add() {},
    },
  });

  Vue.component("course-display", {
    props: {
      courses: Array,
    },
    template: `
			<ul>
				<li v-for="c in courses" :key="c">{{c}}</li>
    		</ul>
		`,
  });
  const vm = new Vue({
    el: "#app",
    data() {
      return {
        courses: ["Node.js", "Vue", "React"],
      };
    },
  });
</script>
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210328204441.png" alt="image-20210328204441061" style="zoom:50%;" />

我们希望当我们点击添加课程的按钮时，向父组件的 `courses` 添加一门课程，也就是如何将子组件的数据传递到父组件。要做到这件事情，只能通过回调函数的方式将数据传递给父组件，首先我们为组件 `course-add` 绑定一个自定义的事件

```html
<course-add @add-course="addCourse"></course-add>
```

我们为组件 `course-add` 绑定了 `add-course` 这个自定义事件，要触发该自定义事件，可通过 `this.$emit('add-course')` 触发，点我们点击按钮时我们便触发该事件，即

```javascript
// course-add
methods: {
    add() {
        // 通过后续参数将数据传递
        this.$emit('add-course', this.course);
        this.course = '';
    }
}
```

因为我们为 `add-course` 这个自定义事件绑定了父组件的 `addCourse` 方法，所以当触发 `add-course` 这个自定义事件时，便会调用父组件的 `addCourse` 方法

```javascript
// 父组件
methods: {
    addCourse(course) {
        // 将子组件传递的数据加入到 courses 数组中
        this.courses.push(course);
    }
}
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210328205642.gif" alt="1" style="zoom:50%;" />

我们再次捋一捋流程：

1. 点击按钮，触发子组件的 `add` 方法
2. 在 `add` 方法中，触发自定义事件 `add-course`，并传递数据
3. 自定义事件 `add-course` 绑定的处理函数为父组件的 `addCourse` 方法
4. 在父组件的 `addCourse` 方法中我们拿到子组件传递的数据，并添加到 `courses` 中

> **踩坑**：HTML 的属性大小写是**不敏感**的，会一律转化为小写。如果你为组件添加如下自定义事件 `addCourse`，那么它会被转化为小写的形式，即 `addcourse`，所以如果你通过 `this.$emit('addCourse')` 触发事件的话是不会成功的，所以一律推荐使用 `kebab-case` 命名，即 `add-course`。

### 双向绑定

如果我们为组件绑定 `v-model` 时，它实际上自动绑定了 `value` 属性，以及添加了一个名为 `input` 的自定义事件

```javascript
Vue.component("base-input", {
  props: {
    value: String,
  },
  template: `<input type="text" :value="value" @input="input">`,
  methods: {
    input(event) {
      this.$emit("input", event.target.value);
    },
  },
});
```

我们下面可以向使用正常表单一样使用该组件

```html
<div id="app">
  <base-input v-model="message"></base-input>
  <p>{{message}}</p>
</div>
```

```javascript
const vm = new Vue({
  el: "#app",
  data() {
    return {
      message: "",
    };
  },
});
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210328213116.gif" alt="2" style="zoom:50%;" />

因为向单选框、复选框这样的输入控件可能会将 `value` 用于不同的目的，我们可以通过 `model` 属性避免这样的冲突

```javascript
Vue.component("base-checkbox", {
  model: {
    prop: "checked",
    event: "change",
  },
  props: {
    checked: Boolean,
  },
  template: `
        <input
        type="checkbox"
        :checked="checked"
        @change="$emit('change', change)"
        >
    `,
  methods: {
    change(event) {
      this.$emit("change", event.target.checked);
    },
  },
});
```

上面我们在 `model` 属性中声明绑定的属性为 `checked` 以及绑定的自定义事件为 `change`。

### 动态组件

我们可以通过 `<component>` 以及 `:is` 属性来动态的切换组件

```html
<div id="app">
  <input
    type="radio"
    name="component"
    @click="currentComponent = 'component-a'"
    checked
  />A
  <input
    type="radio"
    name="component"
    @click="currentComponent = 'component-b'"
  />B
  <component :is="currentComponent"></component>
</div>
```

当我们点击单选框时，会修改 `currentComponent` 的值，而 `component` 会根据 `currentComponent` 选择展示什么组件，例如当我们点击第一个单选框时，`currentComponent` 的值会被设置为 `component-a`，因此 `<component>` 会展示 `component-a` 这个组件。在下面我们定义用到的组件

```javascript
Vue.component("component-a", {
  template: `<p>component-a</p>`,
});
Vue.component("component-b", {
  template: `<p>component-b</p>`,
});
const vm = new Vue({
  el: "#app",
  data() {
    return {
      currentComponent: "component-a",
    };
  },
});
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210329135906.gif" alt="3" style="zoom:50%;" />

## 插槽

### 插槽内容

除了可以通过 `prop` 向组件传递数据，我们还可以通过插槽向组件传递数据，例如

```html
<layout> content </layout>
```

其中 `content` 便是传递的数据，它会被分发到 `layout` 组件中特定的位置中。假设 `layout` 组件的模板如下

```html
<div>
  <header></header>
  <main>
    <slot></slot>
  </main>
  <footer></footer>
</div>
```

那么该模板会被渲染为

```html
<div>
  <header></header>
  <main>content</main>
  <footer></footer>
</div>
```

`content` 会替换 `layout` 中的 `<slot></slot>` 标签，`slot` 便是插槽的意思，它就是一个占位置的。

### 后备插槽

如果没有组件传入分发的内容，我们希望使用默认值，这时便可以使用后背插槽

```html
<div>
  <header></header>
  <main>
    <slot>默认内容</slot>
  </main>
  <footer></footer>
</div>
```

`slot` 标签中的内容就是默认值，当我们没有为 `layout` 组件传入内容时便会使用默认值，如

```html
<layout></layout>
```

会被渲染为

```html
<div>
  <header></header>
  <main>默认内容</main>
  <footer></footer>
</div>
```

### 具名插槽

有时候我们需要多个插槽，有如下 `layout` 组件

```html
<div>
  <header>
    <!-- 这个地方放头部 -->
  </header>
  <main>
    <!-- 这个地方放内容 -->
  </main>
  <footer>
    <!-- 这个地方放页脚 -->
  </footer>
</div>
```

这个时候我们就要为 `slot` 标签指定 `name` 属性，以区别不同的插槽

```html
<div>
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
```

上述我们没有为 `main` 中的 `slot` 指定 `name` 属性，它会有一个默认的名称 `default`。接下来我们如下使用 `layout` 组件进行组件分发

```html
<layout>
  <template v-slot:header>
    <h1>这是一个 Header</h1>
  </template>

  <template v-slot:default>
    <p>内容</p>
    <p>内容</p>
  </template>

  <template v-slot:footer>
    <small>这是页脚</small>
  </template>
</layout>
```

`layout` 组件最终会被渲染为

```html
<div>
  <header>
    <h1>这是一个 Header</h1>
  </header>
  <main>
    <p>内容</p>
    <p>内容</p>
  </main>
  <footer>
    <small>这是页脚</small>
  </footer>
</div>
```

同 `v-on` 与 `v-bind` 一样，`v-slot` 也有缩写，我们可以把上述的 `v-slot:` 替换为 `#`

```html
<layout>
  <template #header>
    <h1>这是一个 Header</h1>
  </template>

  <template #default>
    <p>内容</p>
    <p>内容</p>
  </template>

  <template #footer>
    <small>这是页脚</small>
  </template>
</layout>
```

### 作用域插槽

如果能在插槽中访问子组件才有的数据是很有用的，假设有如下的 `<current-user>` 组件

```html
<span>
  <slot></slot>
</span>
```

我们希望能够访问 `current-user` 组件中的 `user` 数据

```html
<current-user> {{user.firstName}} </current-user>
```

但是上述访问的是 `current-user` 父组件的数据，所以上面的代码不会正常工作。为了访问到子组件的数据，我们可以为 `slot` 元素绑定一个属性

```html
<span>
  <slot :user="user"></slot>
</span>
```

接着我们便可以以下面的方式访问 `current-user` 的数据了

```html
<current-user>
  <template v-slot:default="slotProps"> {{slotProps.user.firstName}} </template>
</current-user>
```

如果 `current-user` 中**只有默认插槽**的话，上面的代码可以简写为

```html
<current-user v-slot="slotProps"> {{slotProps.user.firstName}} </current-user>
```

另外我们可以通过结构语法，从 `slotProps` 中解构出 `user`，如

```html
<current-user v-slot="{user}"> {{user.firstName}} </current-user>
```

## 动画

Vue 在插入、更新、移除 DOM 元素时，提供不同的方式应用过渡效果。

### transition

Vue 提供了 `transition` 组件，可以为任何组件以及元素提供进入、离开过渡，下面给出一个例子

```html
<div id="app">
  <p v-if="show">Hello World!</p>
  <p><button @click="toggle">toggle</button></p>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
  new Vue({
    el: "#app",
    data() {
      return {
        show: true,
      };
    },
    methods: {
      toggle() {
        this.show = !this.show;
      },
    },
  });
</script>
```

现在点击按钮没有任何的过渡效果，现在我们为 `p` 标签外面添加 `transition` 组件

```html
<div id="app">
  <transition name="fade">
    <p v-if="show">Hello World!</p>
  </transition>
  <p><button @click="toggle">toggle</button></p>
</div>
```

为元素包裹 `transition` 组件以后，Vue 会做如下处理：

1. 自动嗅探目标元素是否应用了 CSS 过渡或动画，如果是，在恰当的时机添加/删除 CSS 类名
2. 如果过渡组件提供了 JavaScript 钩子函数，这些钩子函数将在恰当的时机被调用
3. 如果没有找到 JavaScript 钩子并且也没有检测到 CSS 过渡/动画，DOM 操作 (插入/删除) 在下一帧中立即执行

### CSS 动画

如果我们使用了 `CSS` 动画，在元素插入之前， `trasnition` 组件会为包裹元素添加 `v-enter` 类，其中 `v` 就是在 `transition` 指定的 `name` 属性值，如上面的 `name` 属性值为 `fade`，所以会添加 `fade-enter` 类，该类用以定义动画的初始状态，在元素插入之后会被移除；在插入的过程中，会添加一个 `v-enter-active` 以及 `v-enter-to`类，`v-enter-active` 为元素定义过渡状态，在动画完成之后被移除， `v-enter-to` 用以定义过渡结束时的状态，也是在动画完成之后被移除。

对于离场动画也是同理，在离场之前会添加一个 `v-leave` 类，用以定义动画的初始状态，动画开始后被移除，在离场的过程中会添加 `v-leave-active` 和 `v-leave-to` 类，分别用以动画的过渡状态以及动画最终状态。

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210329104254.png" alt="transition" style="zoom:50%;" />

```css
.fade-enter,
.fade-leave-to {
  opacity: 0;
}
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter-to,
.fade-leave {
  opacity: 1;
}
```

效果如下：

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210329105850.gif" alt="1" style="zoom:50%;" />

除了可以使用 `transition` 属性定义过渡状态，还可以使用 `animation` 动画，例如

```css
.bounce-in-enter-active {
  animation: bounce-in 0.5s;
}
.bounce-in-leave-active {
  animation: bounce-in 0.5s reverse;
}
@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.5);
  }
  100% {
    transform: scale(1);
  }
}
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210329111853.gif" alt="2" style="zoom:50%;" />

### 使用 CSS 动画库

我们可以通过以下属性自定义过渡类名：

- enter-class
- enter-active-class
- enter-to-class
- leave-class
- leave-active-class
- leave-to-class

这些类的优先级高于普通的类名，这对于 Vue 的过渡系统和其他第三方 CSS 动画库，如 [Animate.css](https://daneden.github.io/animate.css/) 结合使用十分有用

```html
<link
  href="https://cdn.jsdelivr.net/npm/animate.css@3.5.1"
  rel="stylesheet"
  type="text/css"
/>
<div id="app">
  <transition
    name="bounce-in"
    enter-active-class="animated tada"
    leave-active-class="animated bounceOutRight"
  >
    <p v-if="show">Hello</p>
  </transition>
  <p><button @click="toggle">toggle</button></p>
</div>

<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
  new Vue({
    el: "#app",
    data() {
      return {
        show: true,
      };
    },
    methods: {
      toggle() {
        this.show = !this.show;
      },
    },
  });
</script>
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210329123508.gif" alt="1" style="zoom:50%;" />

### 使用 JS 动画库

Vue 过渡系统还提供了一系列的钩子函数：

- before-enter：进入之前
- enter：进入的时候
- after-enter：进入完成
- before-leave：离开之前
- leave：离开时
- after-leave：离开之后

这些钩子函数会在特定的阶段触发，我们可以配合 JS 动画库来实现动画效果

```html
<div id="app">
  <p><button @click="toggle">toggle</button></p>
  <transition
    v-on:before-enter="beforeEnter"
    v-on:enter="enter"
    v-on:leave="leave"
  >
    <p v-if="show">Hello</p>
  </transition>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/velocity/1.2.3/velocity.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
  const vm = new Vue({
    el: "#app",
    data() {
      return {
        show: true,
      };
    },
    methods: {
      toggle() {
        this.show = !this.show;
      },
      beforeEnter: function (el) {
        el.style.opacity = 0;
        el.style.transformOrigin = "left";
      },
      enter: function (el, done) {
        Velocity(el, { opacity: 1, fontSize: "1.4em" }, { duration: 300 });
        Velocity(el, { fontSize: "1em" }, { complete: done });
      },
      leave: function (el, done) {
        Velocity(
          el,
          { translateX: "15px", rotateZ: "50deg" },
          { duration: 600 }
        );
        Velocity(el, { rotateZ: "100deg" }, { loop: 2 });
        Velocity(
          el,
          {
            rotateZ: "45deg",
            translateY: "30px",
            translateX: "30px",
            opacity: 0,
          },
          { complete: done }
        );
      },
    },
  });
</script>
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210329125635.gif" alt="2" style="zoom: 50%;" />

## 参考文献

- [Vue 官方文档](https://cn.vuejs.org/v2/guide/transitions.html)
