---
tags: [React]
---

编写 React 程序依赖于两个库，`react` 与 `react-dom`，我们可以直接通过 `script` 标签引入，同时我们也需要准备一个容器，用以挂载 React 渲染出的 DOM

<!-- <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/carbon2022-05-28-14-26-51.svg" style="zoom:50%"/> -->

<!-- <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/carbon2022-06-09-10-23-02.svg" style="zoom:50%"/> -->

```html
<div id="list"></div>
<script
  src="https://unpkg.com/react@18/umd/react.development.js"
  crossorigin
></script>
<script
  src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"
  crossorigin
></script>
```

```note
`react` 库的引入必须在 `react-dom` 之前。
```

通过 `React.createElement` 创建出虚拟 DOM，并通过 `ReactDOM` 将其挂载到浏览器页面中

<!-- <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/carbon2022-06-09-10-25-28.svg" style="zoom:50%"/> -->

```html
<script>
  const VDOM = React.createElement(
    "h1",
    { className: "heading" },
    "Hello React"
  );
  const root = ReactDOM.createRoot(document.getElementById("list"));
  root.render(VDOM);
</script>
```

`React.createElement` 接收三个参数，依次为：

- 标签名
- 标签上的属性
- 标签中的内容

`React.createElement('h1', { className: 'heading' }, 'Hello React')` 的意思就是创建了一个 `h1` 标签，它的类名为 `heading`，其中的内容为 `Hello React`，上述虚拟 DOM 就相当于

<!-- <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/carbon2022-06-09-10-31-53.svg" style="zoom:50%"/> -->

```html
<h1 class="heading">Hello React</h1>
```

```info
因为 `class` 在 JavaScript 中是关键字，所以我们得使用 `className` 来进行代替！
```

效果如下所示：

```antd
const h1 = <h1>Hello React</h1>
const root = ReactDOM.createRoot(el)
root.render(h1)
```

````warning
如果你打开浏览器的控制台的话，你可能会看到如下提示

```danger
react-dom.development.js:73 Warning: You are importing createRoot from "react-dom" which is not supported. You should instead import it from "react-dom/client".
```

你可以暂时忽略它，可以参考这个[链接](https://githubhot.com/repo/facebook/react/issues/24292)。
````

```antd
const { Nav } = components

const root = ReactDOM.createRoot(el)
root.render(<Nav prev="React介绍" next="JSX介绍" />)
```