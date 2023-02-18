---
tags: [React Router, React]
---

单页应用(SPA)
- 局部更新，不刷新页面
- 通过 ajax 获取数据，更新页面

```bash
npm install react-router-dom@5
```

Router

- BrowserRouter
- HashRouter

NavLink：需放置在 Router 下

- activaClassName
- to

Route：需放置在 Router 下

- path
- component

Switch：只匹配一次

```jsx
<Siwtch>
  <Route path="/home" component={Home1} />
  <Route path="/home" component={Home2} />
</Siwtch>
```

多级路径刷新，样式丢失。解决：

```html
<link href="./css/xxx.css" />            // ❎
<!-- 方法一，使用解绝对路径 -->
<link href="/css/xxx.css" />             // ✅
<!-- 方法二，使用%PUBLIC_URL%，只适用于 React 脚手架创建的项目 -->
<link href="%PUBLIC_URL%/css/xxx.css" /> // ✅
<!-- 方法三，将 BrowserRouter 替换为 HashRouter，罕见 -->
<BrowserRouter> -> <HashRouter>          // ✅
```

模糊匹配

```jsx
<NavLink to="/home/a/b" />
<Route path="/home" />
```

精准匹配：exact，有问题才设置，有时会导致无法继续匹配二级路由

```jsx
<Route exact={true} path="" />
```

Redirect：兜底，全都没匹配上

```jsx
<Switch>
  <Route path="/home" />
  <Redirect to="/home" />
</Switch>
```

嵌套路由

向路由组件传递数据

- params

  ```jsx
  <NavLink to="/home/user/1" />
  <Route path="/home/user/:id" component={User} />
  ```

  ```jsx
  // User
  const { id } = this.props.match.params
  ```

- search

  ```jsx
  <NavLink to="/home/user?id=1" />
  // 正常声明即可，无需声明接收
  <Route path="/home/user" component={User} />
  ```

  ```jsx
  // User
  this.props.location.search // ?id=1，需要手动解析
  ```

  ```tip
  可通过 `querystring` 解析 search 参数
  ```

- state：参数不在地址栏显示

  ```jsx
  <NavLink to={pathname: '/home/user', state: {id: 1}} />
  <Route path="/home/user" component={User} />
  ```

  ```jsx
  // User
  this.props.location.state
  ```

  ```note
  刷新会保留 `state` 参数。
  ```

编程式路由导航

- this.props.history

  - go(n)

  - goBack()

  - goForward()

  - push(url, state)

    ```js
    const { history } = this.props
    history.push('/home/1')
    history.push('/home?id=1')
    history.push('/home', {id: 1})
    ```

  - replace(url, state)

---

withRouter

非路由组件如何进行编程式导航

```jsx
import { withRouter } from 'react-router-dom'
class Header extends React.Component {
  back = () => {
    // 可以访问到 
    this.props.history.goBack()
  }
}

export default withRouter(Header)
```



