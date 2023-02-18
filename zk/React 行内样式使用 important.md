---
tags: [React, CSS, 解决方案]
---

React 不支持在行内样式设置 `!important`

```jsx
// 无效
<p style={{fontSize: '20px !important'}}>Hello World</p>
```

通过 `ref` 来设置

```jsx
<p ref={(node) => {
  if (node) {
    node.setProperty('font-size', '20px', 'important')
  }
}}>Hello World</p>
```

参考：

- [react 行内样式的!important不生效怎么处理](https://blog.csdn.net/hzxOnlineOk/article/details/106728437)