---
tags: [React, JSX]
---

## 使用变量

考虑如下虚拟 DOM

```js
const VDOM = <h1>Hello World!</h1>;
```

我们希望 `h1` 中的内容跟随变量的变化而变化，使用如下写法

```antd
const { Tabs } = antd;
const { TabPane } = Tabs;

const code = `\`\`\`jsx
const header = 'Hello React'
const VDOM = <h1>{header}</h1>
\`\`\``


const html = await renderMarkdown(code)

const header = 'Hello React'
const VDOM = <h1>{header}</h1>

const CodeTab = () => (
 <Tabs defaultActiveKey="1">
    <TabPane tab="CODE" key="1">
      <div dangerouslySetInnerHTML={{ __html: html }} />
    </TabPane>
    <TabPane tab="RESULT" key="2">
      {VDOM}
    </TabPane>
  </Tabs>
);
const root = ReactDOM.createRoot(el)
root.render(<CodeTab />)
```

---

对于需要使用变量的地方，我们需要使用花括号 `{}` 进行包裹，`{}` 不仅可以是变量，还可以是任何的表达式，但不能是 `for`、`if` 等语句。

如果 `{}` 中的变量是一个数组，那么数组中的内容会被自动的遍历并进行渲染。

```antd
const { Tabs } = antd;
const { TabPane } = Tabs;

const code = `\`\`\`jsx
const datas = ['JavaScript', 'Rust', 'Go']
const VDOM = <ul>{datas}</ul>
\`\`\``


const html = await renderMarkdown(code)
const datas = ['JavaScript', 'Rust', 'Go']

const CodeTab = () => (
 <Tabs defaultActiveKey="1">
    <TabPane tab="CODE" key="1">
      <div dangerouslySetInnerHTML={{ __html: html }} />
    </TabPane>
    <TabPane tab="RESULT" key="2">
      {datas}
    </TabPane>
  </Tabs>
);
const root = ReactDOM.createRoot(el)
root.render(<CodeTab />)
```

---

如果需要对数组进行转换，此时可以使用 `map` 函数

```antd
const { Tabs } = antd;
const { TabPane } = Tabs;

const code = `\`\`\`jsx
const datas = ['JavaScript', 'Rust', 'Go']
const VDOM = (
  <ul>
    {datas.map((data, index) => <li key={index}>{data}</li>)}
  </ul>
)
\`\`\``


const html = await renderMarkdown(code)
const datas = ['JavaScript', 'Rust', 'Go']

const CodeTab = () => (
 <Tabs defaultActiveKey="1">
    <TabPane tab="CODE" key="1">
      <div dangerouslySetInnerHTML={{ __html: html }} />
    </TabPane>
    <TabPane tab="RESULT" key="2">
      <ul>{datas.map((data, index) => <li key={index}>{data}</li>)}</ul>
    </TabPane>
  </Tabs>
);
const root = ReactDOM.createRoot(el)
root.render(<CodeTab />)
```

当我们使用 `map` 函数生成由多个 JSX 对象组成的列表时，需要为每个 JSX 对象指定一个 `key` 属性作为唯一标识，当数据发生变化时，通过 `key` 可以快速知道哪些元素发生了变化（添加、删除、移动等），可以使 React 更新页面更加快速。如果不指定 `key` 将会给出错误提示（页面仍可渲染）

>Each child in a list should hava a unique "key" prop.

```note
此处使用 `index` 作为 `key` 的唯一标识，但是这样是有问题的，后续深入时会指出。
```

---

如果需要根据变量显示不同的内容，那么可以使用三元运算符。

```antd
const { Tabs } = antd;
const { TabPane } = Tabs;

const code = `\`\`\`jsx
const body = document.querySelector('body')
const dark = body.classList.contains('theme-dark')
const VDOM = <h1>{dark ? 'Dark' : 'Light'}</h1>
\`\`\``

const body = document.querySelector('body')
const dark = body.classList.contains('theme-dark')
const VDOM = <h1>{dark ? 'Dark' : 'Light'}</h1>
const html = await renderMarkdown(code)

const CodeTab = () => (
 <Tabs defaultActiveKey="1">
    <TabPane tab="CODE" key="1">
      <div dangerouslySetInnerHTML={{ __html: html }} />
    </TabPane>
    <TabPane tab="RESULT" key="2">
      {VDOM}
    </TabPane>
  </Tabs>
);
const root = ReactDOM.createRoot(el)
root.render(<CodeTab />)
```

```warning
不能在 `{}` 中放置对象，否则会报错，但是这个对象是虚拟 DOM 对象就没有问题。
```

## 添加样式

添加样式有两种方法，设置类名以及使用行内样式。需要注意的是在 `VDOM` 中不能使用 `class` 属性，因为 `class` 在 JavaScript 中是关键字，转而要使用 `className`

```jsx
<style>
  .red {
    color: red;
  }
</style>

const VDOM = <h1 className="red">Hello React</h1>
```

使用行内样式时，`style` 属性不能指定为字符串，即下面的写法是不行的

```jsx
const VDOM = <h1 style="color: red;">Hello React</h1> // ❎
```

而是要指定 `style` 为一个对象

```jsx
const VDOM = <h1 style={{color: 'red'}}>Hello React</h1> // ✅
```

````note
`style={{color: 'red'}}` 中的第一对花括号表示要插入一个变量，第二个花括号表示对象，上面的写法等价于

```jsx
const style = {color: 'red'}
const VDOM = <h1 style={style}>Hello React</h1>
```
````

对于连字符，需要转换为驼峰命名的形式

```jsx
// background-color -> backgroundColor
const VDOM = <h1 style={{backgroundColor: 'red'}}>Hello React</h1>
```

## 根标签

虚拟 DOM 对象只能有一个根标签，以下写法是错误的 ❎

```jsx
const VDOM = (
  <p>床前明月光</p>
  <p>疑是地上霜</p>
)
```

必须将其包裹在一个根标签中 ✅

```jsx
const VDOM = (
  <div>
    <p>床前明月光</p>
    <p>疑是地上霜</p>
  </div>
)
```

## 标签闭合

所有使用 `JSX` 写的标签都必须闭合，否则会报错

```jsx
const VDOM = <input type="text">         // ❎
const VDOM = <input type="text" />       // ✅
const VDOM = <input type="text"></input> // ✅
```

```antd
const { Nav } = components

const root = ReactDOM.createRoot(el)
root.render(<Nav prev="JSX介绍" next="组件介绍" />)
```