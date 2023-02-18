---
tags: [React]
---

常规网页开发流程

1. 请求数据
2. 处理数据
3. 操作 DOM，更新页面

React 帮助我们简化操作 DOM 的流程，完全不用手动操作 DOM，当数据发生变化时，视图会相应的发生变化，这种开发方式称为数据驱动的开发方式，在定义好数据与视图之间的映射关系之后，我们只需要操作数据，视图会相应的发生变化，我们完全不需要操作 DOM 来改变视图了，现在主流的前端框架 React、Vue 都是数据驱动视图。

以往我们的数据发生变化，我们一般不会比较数据发生的变化，而是会全盘更新

<!-- <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/carbon2022-06-09-10-18-21.svg" style="zoom:50%"/> -->

```javascript
const data = ['Alice', 'Bob']
function update(data) {
  let html = ''
  data.forEach(name => {
    html += `<div>${name}</div>`
  })
  document.getElementById('list').innerHTML = html
}
update(data)
```

每次有新的数据，我们都会构建新的 `html` 字符串，然后重新设置 `list` 的 `innerHTML`。如果之前的数据与现在的数据有相当大的重复，也会重新设置，并不会复用之前的元素。

React 使用了虚拟 DOM，你可以认为它与展示在浏览中的真实 DOM 是一一对应的，在数据发生变化时，它会得到新的视图的虚拟 DOM，然后与之前的虚拟 DOM 进行对比，尽最大的可能复用之前的 DOM，这样就提升了网页的性能。

```antd
const { Nav } = components
const root = ReactDOM.createRoot(el)
root.render(<Nav noPrev next="React的第一个程序" />)
```