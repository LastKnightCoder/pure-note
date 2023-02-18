---
tags: [Vue]
---

## 安装 Vue

安装 Vue 当然是选择[官网](https://cn.vuejs.org/js/vue.js)了，当然我们也可以选择通过 CDN 引入文件，例如

```javascript
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
```

## Hello World

下面就是经典的 Hello World 程序，新建一个 HTML 文件，内容如下

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div id="app">
        {{message}}
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        const vm = new Vue({
            el: '#app',
            data() {
                return {
                    message: 'Hello World!'
                }
            }
        })
    </script>
</body>
</html>
```

页面上显示的内容如下：

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210326190835.png" style="zoom:50%;" />

## 发生了什么

我们先简单的捋一捋发生了什么，一切先从下面的代码说起

```javascript
const vm = new Vue({
    el: '#app',
    data() {
        return {
            message: 'Hello World!'
        }
    }
})
```

上面我们创建了一个 Vue 的实例 `new Vue()`，并且向其中传入一个对象，包括这么两个属性：

- el：绑定的 DOM 元素
- data：返回一个对象的函数，返回对象中的数据与视图进行了绑定，当修改数据时，相应的视图也会发生更新

当我们创建一个 Vue 实例时，首先它会根据 el 属性指定的选择器找到相应的 DOM 元素，我们称这个 DOM 结构为模板(template)，例如上例根据 `#app` 选择器我们找到下面的模板

```html
<div id="app">
    {{message}}
</div>
```

接着便会解析该模板，例如上面将 `{{message}}` 替换为了 `data` 中定义的数据 `message` 的对应的值，即 `Hello World!`，解析完毕后便会渲染页面，我们便在页面上看到了 `Hello World!`。

因为 `Vue` 对 `data` 属性中定义的数据进行了拦截，一旦我们改变对象属性的值，Vue 便会对用到该属性的模板进行解析、渲染。

``` tip
拦截数据是通过 `Object.definePropety()` 来做到的，可以通过 [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty) 来了解该方法的使用。
```

## Vue实例

上面我们使用变量 `vm` 接收了 `new Vue()` 返回的 `Vue` 实例，现在我们看看里面有什么。

### $el

模板经过 `Vue` 解析、渲染以后，然后根据该模板生成一个 DOM 元素挂载在页面中，而这个 DOM 元素我们可以通过 `vm.$el` 进行访问得到

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210326204150.png" style="zoom:50%;" />

### $data

通过 `vm.$data` 可以得到 `data` 属性返回的对象

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210326204437.png" style="zoom:50%;" />

我们可以通过 `vm.$data` 来修改数据

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210326204658.png" style="zoom:50%;" />

上面我们修改数据 `message` 为 `Hello Vue`，页面便发生了变化，进一步证实了数据与视图的绑定。

为了方便通过 `vm` 操作数据，所有的数据都被挂载到了 `vm` 上，即我们可以直接通过 `vm` 访问以及修改数据，而不必通过 `vm.$data`

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210326204947.png" style="zoom:50%;" />

上面我们通过 `vm.message` 直接修改了数据，页面也立即发生了变化。

```info
无论我们是通过 `vm.message` 还是 `vm.$data.message` 修改数据，它们之间是互相影响的。即当我们通过 `vm.xxx` 修改数据，那么 `vm.$data.xxx` 的值也会发生改变，反之亦然。
```
