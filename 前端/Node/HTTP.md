---
tags: [Node/http]
---

我们可以使用 http 模块搭建 HTTP 服务器，处理前端页面发起的请求，首先我们搭建一个简单的 HTTP 服务器

```javascript
const http = require("http");

// 创建一个 HTTP 服务器
const server = http.createServer();

// 监听请求，当有请求到达时触发回调函数
server.on("request", (req, res) => {
  res.setHeader("Content-Type", "text/plain");
  res.write("Hello World!");
  res.end();
});

// 监听 3000 端口
server.listen(3000, (err) => {
  if (!err) {
    console.log("服务启动在3000端口...");
  }
});
```

上述我们搭建了一个 HTTP 服务器，它监听了 3000 端口，当有请求到来时，我们返回一个字符串 `Hello World！`，我们使用 Postman 发送一个请求测试

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting/20210220192055.png" style="zoom:50%;" />

## 请求对象

```javascript
server.on("request", (req, res) => {
  // ... ...
});
```

上面的代码我们向回调函数传递了两个参数

- req：请求对象，包含了请求相关的信息，例如请求头，请求参数，请求地址
- res：响应对象，用以设置响应信息，例如响应头，响应内容

在本节介绍请求对象。

### req.url

通过 `req.url` 我们可以过得请求地址

```javascript
const http = require("http");

// 创建一个 HTTP 服务器
const server = http.createServer();

// 监听请求，当有请求到达时触发回调函数
server.on("request", (req, res) => {
  // 解决中文乱码
  res.setHeader("Content-Type", "text/plain;charset=utf-8");
  // 根据不同的 url 返回不同的内容
  if (req.url === "/") {
    res.write("您访问的是主页");
  } else if (req.url === "/add") {
    res.write("添加一条记录");
  } else {
    res.write("您访问的资源不存在");
  }
  res.end();
});

// 监听 3000 端口
server.listen(3000, (err) => {
  if (!err) {
    console.log("服务在启动3000端口...");
  }
});
```

我们可以利用核心模块 `querystring` 来解析 url，获得请求参数

```javascript
const http = require("http");
const querystring = require("querystring");

const server = http.createServer();

server.on("request", (req, res) => {
  // 解析请求参数为键值对的形式
  const url = req.url;
  const query = url.split("?")[1];
  const params = querystring.parse(query);

  res.setHeader("Content-Type", "application/json");
  // 将请求参数返回
  res.write(JSON.stringify(params));
  res.end();
});

server.listen(3000);
console.log("服务启动在3000端口......");
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting/20210220210926.png" style="zoom:50%;" />

### req.method

通过 `req.method` 属性，可以知道请求方法，常见请求方法包括

- GET
- POST
- PUT
- DELETE
- OPTIONS

```javascript
const http = require("http");

const server = http.createServer();

server.on("request", (req, res) => {
  res.setHeader("Content-Type", "text/plain;charset=utf-8");

  if (req.method === "GET") {
    res.write("GET 请求");
  }

  if (req.method === "POST") {
    res.write("POST 请求");
  }

  res.end();
});

server.listen(3000, (err) => {
  if (!err) {
    console.log("服务在启动3000端口...");
  }
});
```

### req.headers

`req.headers` 保存着所有的请求头的信息

```javascript
const http = require("http");

const server = http.createServer();

server.on("request", (req, res) => {
  res.setHeader("Content-Type", "application/json");
  // 将请求头原样返回
  res.write(JSON.stringify(req.headers));
  res.end();
});

server.listen(3000, (err) => {
  if (!err) {
    console.log("服务在启动3000端口...");
  }
});
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting/20210220204430.png" style="zoom:50%;" />

## 响应对象

响应对象就是用来发送响应信息，包括

- 响应头
- 状态码
- 响应内容

### setHeader

我们可以通过 setHeader(name, value) 来设置响应头，例如设置 `Content-Type` 响应头

```javascript
const http = require("http");

const server = http.createServer();

server.on("request", (req, res) => {
  // 设置响应头 Content-Type
  res.setHeader("Content-Type", "text/plain");
  res.write("Hello World!");
  res.end();
});

server.listen(4000);
```

### writeHead

我们可以通过 writeHead 来设置响应状态码

```javascript
const http = require("http");

const server = http.createServer();

server.on("request", (req, res) => {
  res.setHeader("Content-Type", "text/plain");
  res.writeHead(404);
  res.write("Resource Not Found");
  res.end();
});

server.listen(4000);
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting/20210220213829.png" style="zoom:50%;" />

除此之外，writeHead 还可以接收一个可选的对象来设置响应头，它的优先级比 setHeader 方法的优先级高

```javascript
const http = require("http");

const server = http.createServer();

server.on("request", (req, res) => {
  res.setHeader("Content-Type", "text/plain");
  // 重新设置 Content-Type 请求头
  res.writeHead(404, {
    "Content-Type": "text/html",
  });
  res.write("Resource Not Found");
  res.end();
});

server.listen(4000);
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting/20210220215045.png" style="zoom:50%;" />

### write、end

我们使用 res.write 方法来返回响应内容，res.write 方法可以多次调用，在调用多次 write 之后，应该使用 res.end 方法表示响应的结束。

```javascript
const http = require("http");
const fs = require("fs").promises;

const server = http.createServer();

server.on("request", (req, res) => {
  res.setHeader("Content-Type", "text/html");
  (async () => {
    const data = await fs.readFile("./page/index.html", "utf-8");
    res.write(data);
    res.end();
  })();
});

server.listen(4000);
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting/20210220220557.png" style="zoom:50%;" />

end 方法也可以接收一个 data 参数

```javascript
res.end(data);

// 相当于
res.write(data);
res.end();
```

## 参考文章

- [nodejs 中服务器返回响应信息中的中文乱码](https://my.oschina.net/u/3407699/blog/1624320)

