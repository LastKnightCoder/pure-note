## 问题描述

以下代码会在页面上输出什么？

```jsx
const { useState, useEffect } = React

const Demo = props => {
  const [a, setA] = useState('')
  const [b, setB] = useState('')
  
  const { code } = props
  
  // code Effect
  useEffect(() => {
    if (code === -1) {
      // 当 code 为 -1 时，同时设置 a 和 b 为空字符串
      setA('')
      setB('')
      return
    }
    // 做一些操作，然后设置 b
    setB('b')
  }, [code])
  
  // b Effect
  // 根据 b 的变化设置 a
  useEffect(() => {
    if (b == '') return
    setA('a')
  }, [b])
  
  return (
    <ul>
      <li>a: {a}</li>
      <li>b: {b}</li>
    </ul>
  )
}

const Parent = () => {
  const [code, setCode] = useState(0)
  
  useEffect(() => {
    // 设置 code 为 -1
    setCode(-1)
  }, [])
  
  return (<Demo code={code} />)
}

const root = ReactDOM.createRoot(document.getElementById('app'))
root.render(<Parent />)
```

页面逻辑如下，`Demo` 组件中存在两个状态 `a` 和 `b`，其中状态 `a` 根据状态 `b` 变化而改变，状态 `b` 根据父组件传递的 `code` 参数设置不同的值，当 `code` 为 `-1` 时，`a` 和 `b` 均要设置为空字符串。

`code Effect` 会被执行两次，第一次 `code` 为 `0`，因此 `a` `b` 均有值，第二次，父组件将 `code` 设置为 `-1`，`a` 和 `b` 应被设置为空字符串，但是实际页面展示 `a` 不为空。

## 问题分析

<img src="https://cdn.staticaly.com/gh/LastKnightCoder/image-for-2022@master/React%E8%B8%A9%E5%9D%91-%E7%AC%AC-1-%E9%A1%B5.5jhl4o5r49c0.png" style="zoom:50%;" />

## 解决办法

<img src="https://cdn.staticaly.com/gh/LastKnightCoder/image-for-2022@master/React%E8%B8%A9%E5%9D%91-%E7%AC%AC-2-%E9%A1%B5.595os5p3ock0.png" style="zoom:50%;" />

```jsx
useEffect(() => {
  if (code === -1) {
    setB('')
    return
  }
  setB('b')
}, [code])
useEffect(() => {
  if (b != '') {
    setA('a')
  } else {
    setA('')
  }
}, [b])
```

