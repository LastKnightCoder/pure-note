---
tags: [React, JSX]
---

现在我们看一个稍微复杂一点的例子，通过 `React.createElement()` 来创建一个对应于如下结构的虚拟 DOM

<!-- <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/carbon2022-06-09-10-35-24.svg" style="zoom:50%"/> -->

```html
<ul>
  <li key=1>Alice</li>
  <li key=2>Bob</li>
</ul>
```

你需要这么写

<!-- <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/carbon2022-06-09-10-36-45.svg" style="zoom:50%"/> -->

```javascript
const VDOM = React.createElement('ul', {}, [
  React.createElement('li', {key: 1}, 'Alice'),
  React.createElement('li', {key: 2}, 'Bob'),
])
```

这样写起来真的有点累，如果写一个更复杂、嵌套更深的组件，那将是十分的费劲，如果有一个函数能直接根据 html 转为虚拟 DOM 就好了，万幸的是，真有这个可能，我们可以直接这么写：

<!-- <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/carbon2022-06-09-10-37-36.svg" style="zoom:50%"/> -->

```jsx
const VDOM = (
  <ul>
    <li key="1">Alice</li>
    <li key="2">Bob</li>
  </ul>
)
```

我们在 JavaScript 中直接写了 html，它是 JavaScript 的新语法吗？并不是，这种写法我们称为 JavaScript XML，简写为 JSX。

```info
注意到 html 并没有使用双引号 `"` 包裹。 
```

那既然不是新语法，当然浏览器也不会识别，我们需要将其转为 JavaScript，这样浏览器才会识别，我们需要引入一个工具，这个工具就是 babel，它就是将 JSX 转为 JavaSCript 的工具，通过如下链接引入

<!-- <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/carbon2022-06-09-10-38-41.svg" style="zoom:50%"/> -->

```html
<script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>
```

现在我们要指定包含 JSX 内容的 `script` 标签的 `type` 属性为 `text/babel`，被标记为 `text/babel` 的标签会被 babel 自动识别，然后进行转换，并将转换后的代码交给浏览器执行

<!-- <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/carbon2022-06-07-14-08-27.svg" style="zoom:50%"/> -->

<!-- <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/carbon2022-06-09-10-40-12.svg" style="zoom:50%"/> -->

```html
<div id="list"></div>
  
<script src="https://unpkg.com/react@18/umd/react.development.js" crossorigin></script>
<script src="https://unpkg.com/react-dom@18/umd/react-dom.development.js" crossorigin></script>

<script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>

<script type="text/babel">
  const VDOM = (
    <ul>
      <li key="1">Alice</li>
      <li key="2">Bob</li>
    </ul>
  )
  const root = ReactDOM.createRoot(document.getElementById('list'))
  root.render(VDOM)
</script>
```

<!-- 内容如下：

```antd
const VDOM = (
    <ul>
      <li key="1">Alice</li>
      <li key="2">Bob</li>
    </ul>
  )
const root = ReactDOM.createRoot(el)
root.render(VDOM)
``` -->

```antd
const { Nav } = components
const root = ReactDOM.createRoot(el)
root.render(<Nav prev="React的第一个程序" next="JSX语法" />)
```