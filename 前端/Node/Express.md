---
tags: [Node/express]
---

Express 是基于 Node.js 的 Web 框架，通过 Express 可以快速的搭建一个 Web 应用。

## 基本使用

安装 express

```bash
mkdir express-demo
cd express-demo
npm init -y
npm i express --save
```

使用 Express 写一个简单的 Web 应用

```javascript
// index.js
const express = require('express');

const app = express();

app.get("/", (req, res) => {
    res.send("Hello World!");
});

app.listen(3000);
```

在上面我们创建了一个简单的 Web 服务器，服务启动在 3000 端口，当我们以 get 方法访问 `/` 路径时，会返回 `Hello World!`。

```bash
node index.js
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting/20210224210145.png" style="zoom:50%" />

## 路由

路由定义了服务器如何对客户端的请求作出相应，它由一个特定的 URI 以及一个 HTTP 方法确定，每一个路由都至少对应一个处理函数。

在 Express 中，路由由如下形式定义

```javascript
app.method(path, handler)
```

其中：

- app：express 实例
- method：HTTP 请求方法，如 get, post, delete ... ...
- path：请求路径，如 `/`、`/add`
- handler：该路由对应的请求

下面看几个例子

```javascript
// 当客户端以 POST 访问 / 路径时，返回 POST
app.post("/", (req, res) => {
    res.send(`POST`)
 });
```

```javascript
const fs = require('fs').promises;
// 当客户端以 GET 方法访问 /hello 路径时，返回 hello.txt 中的内容
app.get("/hello", (req, res) => {
   (async () => {
       try {
           const data = await fsreadFile('./hello.txt','utf-8');
           res.send(data);
       } catch(e) {
           res.send("文件读取失败");
       }
   })()
});
```

## 暴露静态资源文件

当我们在 `html` 通过 `script` `src` 去请求 `javascript` `css` 图片资源文件，我们希望不要通过路由处理，而是直接将文件返回，这时我们需要将资源文件夹暴露出来。

```javascript
app.use(express.static('public'))
```

通过上面一行代码，将根目录下的 public 文件夹中的静态资源暴露出来了。public 文件夹的内容如下

```
public
  script
    index.js
  css
    index.css
```

当我们将 public 文件夹暴露之后，我们便可以通过 

```
http://localhost:3000/script/index.js
http://localhost:3000/css/index.css
```

访问静态资源。我们还可以为资源文件夹规定虚拟目录，例如

```javascript
app.use('/static', express.static('public'))
```

上面我们资源文件所在的虚拟目录为 `/static`，即我们现在要以下面的方式访问资源文件

```
http://localhost:3000/static/script/index.js
http://localhost:3000/static/css/index.css
```

## 中间件

所谓的中间件就是一个函数，它可以在请求到达实际的路由之前对请求对象以及响应对象做任何的处理。例如上述暴露静态资源文件目录就是一个中间件，它可以将资源文件夹下的文件直接返回。

为什么叫做中间件，我猜是因为它位于请求与路由响应中间，所以称它为中间件

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting/20210225185518.png" style="zoom:50%"/>


我们说中间件是一个函数，它接收三个参数

```javascript
function(req, res, next) {
    // 做一些事情
}
```

其中：

- req：请求对象
- res：响应对象
- next：下一个中间件

其中 next 是一个函数，表示下一个中间件，一般如果不在这个中间件中终止响应的话，一般都会在函数的最后调用 `next()` 方法，表示应用下一个中间件。

为 Express 应用中间件也很简单，就直接使用 `app.use(中间件函数)` 即可，例如

```javascript
app.use((req, res, next) => {
    req.requestTime = Date.now();
    next();
})
```

上面我们写了一个中间件，该中间件的函数就是为请求对象添加了一个 `requestTime` 的属性，用来表示请求到来的时间。这样在后面的路由处理函数中可以直接使用 `req.requestTime` 这个属性

```javascript
app.get("/", (req, res) => {
    console.log(req.requestTime);
    res.send(`请求时间是：${req.requestTime}`)
})
```

在这里介绍一个常用的中间件，`body-parser`，它可以解析 `HTTP` 请求体即 body 中的内容，我们一般通过它拿到 `POST` 请求的请求参数。

首先安装 `body-aprser`

```bash
npm i body-parser --save
```

使用

```javascript
const bodyParser = require('body-parser');
app.use(bodyParser.urlencoded({extended: false}));
app.use(bodyParser.json());
```

`body-parser` 会将解析的结果添加到 `req.body` 上面，在路由处理函数中我们可以直接使用 `req.body` 拿到 `POST` 的请求参数

```javascript
// 将请求参数直接返回
app.post("/",(req, res) => {
    res.send(req.body);
})
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting/20210225193433.png" style="zoom: 50%"/>

## 参考文献

- [Express官网](https://expressjs.com/zh-cn/)
- [body-parser 使用详解](https://www.jianshu.com/p/4ebcc5acff45)

