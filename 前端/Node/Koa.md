---
tags: [Node/Koa]
---

Koa 是号称是 Node.js 的下一代 Web 开发框架，它是原 Express 团队设计的新的 Web 开发框架，不像 Express 使用回调函数的开发方式，在 Koa 中使用 asnyc 函数，使用同步的写法写出异步的代码，让开发有更好的开发体验。

## 入门

按照惯例，我们使用 Koa 建立一个简单的 Web 服务器，向前端返回 `Hello World!`。首先下载 `koa`

```bash
npm install koa --save
```

接着在文件中导入 `koa` 使用即可

```javascript
const Koa = require('koa');
const app = new Koa();

app.use(async ctx => {
    ctx.body = "Hello World!";
});

app.listen(3000);
```

上述代码我们引入了 `Koa`，接着使用 `new Koa()` 创建了一个服务，并且监听了 `3000` 端口。当有请求到来时，我们会直接返回 `Hello World!`。下面使用 `Postman` 发出一个 GET 请求，响应如下

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210313162455.png" style="zoom:50%;" />

## 中间件

### 洋葱模型

当请求到来时，需要经过一系列的处理函数进行处理，然后才会对请求进行处理，中间经过的处理函数，我们就称为中间件。这些中间件可以我们添加很多的功能，例如解析请求参数，管理静态资源文件，处理 cookie 等等，这些功能我们都可以抽象为一个中间件。

上述我们说中间件就是一个函数，这个函数接收两个参数：

- `ctx`：上下文对象，包含请求信息，也可以使用它写响应信息
- `next`：下一个中间件

我们通过下面这张图来看中间件的执行过程(洋葱模型)

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210313190007.png" style="zoom: 33%;" />



当请求到来时，首先会经过我们添加的中间件，当我们在中间件时调用 `next` 方法时，便会来到下一个中间件，直到某个中间件没有调用 `next` 方法便执行完毕，接着便会回到上一个中间件调用 `next` 处的地方，执行之后的代码。

当经过中间件处理过后，一般中间件会对上下文 `ctx` 进行设置，我们便根据对 `ctx` 的设置，产生相应的响应给前端。

```javascript
const Koa = require('koa');
const app = new Koa();

// 中间件 1
const middleware1 = async (ctx, next) => {
    console.log("中间件 1 before");
    await next();
    console.log("中间件 1 after");
};

// 中间件 2
const middleware2 = async (ctx, next) => {
    console.log("中间件 2 before");
    await next();
    console.log("中间件 2 after")
};

// 中间件 3
const middleware3 = async ctx => {
    console.log("中间件 3");
    ctx.body = "Hello World!";
};

// 通过 use 方法为应用添加中间件
app.use(middleware1);
app.use(middleware2);
app.use(middleware3);

app.listen(3000);

```

上面我们使用 `use` 方法添加了三个中间件，根据上面的模型，不难得出控制台的打印结果为

```javascript
中间件 1 before
中间件 2 before
中间件3
中间件 2 after
中间件 1 after
```

我们在 `中间件 3` 中设置了 `ctx.body` 为 `Hello World`，`Koa` 便会根据此信息向前端发送 `Hello World!`。

### 简易实现

为了加深对中间件执行的理解，这里给出简易版的中间件的实现

```javascript
const http = require('http');

class Koa {
    constructor() {
        this.middleware = []
    }

    // 添加中间件
    use(fn) {
        this.middleware.push(fn);
    }

    // 根据 req 和 res 创建上下文对象
    createContext(req, res) {
        const context = {};
        context.req = req;
        context.res = res;
        return context;
    }

    // 串联所有的中间件
    compose(middleware) {
        return function (ctx) {
            function dispatch(i) {
                let fn = middleware[i];
                if (!fn) {
                    return Promise.resolve();
                }

                return Promise.resolve(fn(ctx, dispatch.bind(null, i + 1)));
            }
            return dispatch(0);
        }
    }

    // 整体的执行流程
    callback() {
        // 串联中间件，当执行返回的函数时，会以洋葱模型形式执行中间件
        let fn = this.compose(this.middleware);
        // 处理请求，生成上下文 --> 执行中间件 --> 产生响应
        const handleRequest = (req, res) => {
            const ctx = this.createContext(req, res);
            return this.handleRequest(ctx, fn);
        };

        return handleRequest;
    }

    // 监听端口时创建一个 HTTP 服务器
    listen(...args) {
        const server = http.createServer(this.callback());
        return server.listen(...args);
    }

    // 如何处理请求
    handleRequest(ctx, fnMiddleware) {
        res.statusCode = 404;
        const handleResponse = () => this.respond(ctx);
        // 执行中间件 --> 产生响应
        return fnMiddleware(ctx).then(handleResponse);
    }

    // 根据上下文产生响应
    respond(ctx) {
        const res = ctx.res;
        res.setHeader("Context-Type", "text/plain");
        res.end(ctx.body);
    }
}

module.exports = Koa;
```

上述代码的执行流程是，当我们调用 `listen` 方法监听端口时，会利用 Node 内置的 `http` 模块创建一个服务器，并将 `this.callback` 返回的函数作为处理函数

```javascript
listen(...args) {
    const server = http.createServer(this.callback());
    return server.listen(...args);
}

callback() {
    let fn = this.compose(this.middleware);
    const handleRequest = (req, res) => {
        const ctx = this.createContext(req, res);
        return this.handleRequest(ctx, fn);
    };

    return handleRequest;
}
```

在 `this.callback`  函数中，我们首先调用了 `compose` 方法，这个方法可以将通过 `use` 方法添加的中间件建模为洋葱模型，返回一个函数 `fn`，当我们调用这个函数时，便会以洋葱模型执行所有的中间件。

```javascript
compose(middleware) {
    return function (ctx) {
        function dispatch(i) {
            let fn = middleware[i];
            if (!fn) {
                return Promise.resolve();
            }

            return Promise.resolve(fn(ctx, dispatch.bind(null, i + 1)));
        }
        return dispatch(0);
    }
}
```

在 `this.callback` 返回处理请求的函数中，首先根据  `req` 和 `res` 创建上下文对象

```javascript
// 演示代码，只是将 req，res 挂载在 ctx 上面
createContext(req, res) {
    const context = {};
    context.req = req;
    context.res = res;
    return context;
}
```

接着便将上下文对象 `ctx` 传递给 `fn` 中间件进行处理，

```javascript
handleRequest(ctx, fnMiddleware) {
    res.statusCode = 404;
    const handleResponse = () => this.respond(ctx);
    return fnMiddleware(ctx).then(handleResponse);
}
```

中间件处理完成之后，便根据上下文对象作出响应(这里为了演示，只是将 `ctx.body` 的内容直接返回)

```javascript
respond(ctx) {
    const res = ctx.res;
    res.setHeader("Context-Type", "text/plain");
    // 直接将 ctx.body 的内容返回
    res.end(ctx.body);
}
```

``` info
上述代码只是为了演示 Koa 整体的执行流程以及中间件是如何执行的，在实现的过程中忽略了错误的处理，上述产生上下文的代码以及根据上下文对象产生响应的代码只是为了演示。
```


## Context

Koa Context 封装了 Node 的 request 和 response 对象，每次请求都会创建一个 Context，并将此 Context 传递给中间件进行处理，例如

```javascript
const Koa = require('koa');
const app = new Koa();

// 将 Context 传递给中间件进行处理
app.use(async ctx => {
    ctx.body = "Hello World!";
    console.log(ctx);
});

app.listen(3000);
```

下面介绍 Context 的 API。

- `ctx.req`：Node 原生的 Request 对象
- `ctx.res`：Node 原生的 Response 对象
- `ctx.request`：Koa 的 Request 对象
- `ctx.response`：Koa 的 Response 对象

虽然我们可以通过 `ctx.res` 直接返回结果，但是这种绕过 Koa 的 response 对象的行为，会破坏 Koa 的整体流程，所以不推荐使用如下方法和属性

- `res.statusCode`
- `res.write()`
- `res.end()`
- `res.writeHead()`

其实 `ctx` 上很多的属性都是 `ctx.request` 与 `ctx.response` 的代理

-  `ctx.header` ： `ctx.request.header`
- `ctx.method` ： `ctx.request.method`
- `ctx.body` ：`ctx.response.body`
- `ctx.type` ：`ctx.response.type`
- ...

### Request

以下介绍的属性和方法都属于 `ctx.request`。

#### header、headers、get

通过 `header` 获得所有的请求头(也可以通过 `ctx.header` 获得)

```javascript
const Koa = require('koa');
const app = new Koa();

app.use(async ctx => {
    // 将请求头返回
    ctx.body = ctx.header;
});

app.listen(3000);
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210314122050.png" style="zoom:50%;" />



`headers` 是 `header` 的别名，二者的值是一样的(`header === headers`)，也可以通过 `ctx.headers` 访问。

通过 `get()` 方法可以获得某一个请求头的信息

```javascript
app.use(async ctx => {
    console.log(ctx.get("Host")); // localhost:3000
});
```

#### method、url

通过 `method` 与 `url` 可以知道请求方法以及请求路径，这个两个属性也可以通过 `ctx.xxx` 直接访问

```javascript
app.use(async ctx => {
    ctx.body = {
        method: ctx.method,
        url: ctx.url
    };
});
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210314123132.png" style="zoom:50%;" />

#### origin、protocol、host

通过 `origin` 可以获得协议 (protocol) 和主机 (host) 名

```javascript
app.use(async ctx => {
    ctx.body = {
        origin: ctx.origin
    };
});
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210314125913.png" style="zoom:50%;" />

协议以及主机名称可以直接通过 `protocol` 与 `host` 属性获得

```javascript
app.use(async ctx => {
    ctx.body = {
        protocol: ctx.protocol,
        host: ctx.host
    };
});
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210314131712.png" style="zoom:50%;" />

#### href

获得全部的请求路径，包括协议、主机和 url，相当于 `origin + url`

```javascript
app.use(async ctx => {
    ctx.body = {
        origin: ctx.origin,
        url: ctx.url,
        href: ctx.href
    };
});
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210314130753.png" style="zoom:50%;" />

#### path、querystring、search

通过 `path` 可以获得请求路径，即 `url` 中 `?` 之前的内容，而 `querystring` 中保存的是 `?` 之后的内容(不包括 `?`)，与 `querystring` 及其相似的一个属性是 `search`，它保存的也是 `?` 之后的内容，不过是包括 `?`

```javascript
app.use(async ctx => {
    ctx.body = {
        path: ctx.path,
        querystring: ctx.querystring,
        search: ctx.search
    };
});
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210314132111.png" style="zoom:50%;" />

### Response

以下属性都是 `ctx.response` 的属性，如果在代码中直接通过 `ctx` 访问如下属性，说明 `ctx` 对这些属性做了代理。

#### body

通过 `body` 来设置响应体，它可以是如下几种类型：

- String
- Buffer
- Stream
- Object
- null

当我们设置 `ctx.body` 时，如果状态码还未被设置，会自动的帮我们将状态码设置为 200 或 204。并且根据返回的类型设置 `Content-Type` 响应头：

| 类型   | Content-Type                            |
| ------ | --------------------------------------- |
| String | text/plain，text/html，默认编码为 utf-8 |
| Buffer | application/octet-stream                |
| Stream | application/octet-stream                |
| Object | application/json                        |

对于 String 和 Buffer 类型的数据，还会计算内容的长度，并且设置 `Content-Length` 响应头。

#### header、headers

通过 `header` 可以获得响应头对象

```javascript
app.use(async ctx => {
    ctx.body = "Hello World!";
    console.log(ctx.response.header);
});
```

输出结果为

```javascript
[Object: null prototype] {
  'content-type': 'text/plain; charset=utf-8',
  'content-length': '12'
}
```

当我们对 `ctx.body` 进行设置时，它会根据设置给 `ctx.body` 的内容，对 `content-type` 响应头进行设置，并且会计算 `ctx.body` 的长度，对 `content-length` 响应头进行设置。

`headers` 是 `header` 的别名，二者是一模一样的。

#### type

`type` 是用来设置返回的数据类型是什么

```javascript
app.use(async ctx => {
    ctx.body = fs.createReadStream("./girl.jpg");
});
```

上述我们为前端返回了一张图片的二进制流，它默认的类型为 `application/octet-stream`，当我们返回给浏览器时，浏览器会自动下载内容，而不是将其解析为图片。这时我们可以通过 `type` 属性设置返回的数据为图片，这样浏览器便可以对流进行解析然后在页面展示

```javascript
app.use(async ctx => {
    ctx.type = "jpg";
    ctx.body = fs.createReadStream("./girl.jpg");
});
```

我们知道浏览器是根据 `MIME` 类型来是识别数据类型的，但是上面我们设置的类型为 `jpg`，而不是 `jpg` 对应的 `MIME` 类型 `image/jpeg`，这时因为 `Koa` 会根据我们设置的类型，找到对应的 `MIME` 类型然后设置 `type` 属性

```javascript
app.use(async ctx => {
    ctx.type = "jpg";
    console.log(ctx.type); // image/jpeg
    ctx.body = fs.createReadStream("./girl.jpg");
});
```

还可以直接为 `type` 属性直接设置为响应的 `MIME` 类型

```javascript
app.use(async ctx => {
    ctx.type = "image/jpeg";
    ctx.body = fs.createReadStream("./girl.jpg");
});
```

在设置 `type` 属性时，会同时设置 `Content-Type` 响应头。

#### length

当我们设置 `ctx.body` 为 String 或者为 Buffer 类型时，会自动设置 `Content-Length` 响应头，通过 `length` 属性我们可以知道内容长度为多少，即 `Content-Length` 对应的值

```javascript
app.use(async ctx => {
    ctx.body = "Hello World!";
    console.log(ctx.length);    // 12
});
```

我们也可以通过 `length` 属性来设置 `Content-Length` 响应头

```javascript
app.use(async ctx => {
    ctx.body = "Hello World!";
    ctx.length = 10;
});
```

实际我们发送的内容有 `12` 个字节，但是我们设置 `Conetnt-Length` 只有 `10` 个字节，浏览器根据 `Content-Length` 的大小来接收数据，这会导致浏览器只接收到 `10` 个字节的数据

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210314154337.png" style="zoom:50%;" />

#### status、message

可以通过 `status` 来设置返回的状态码，通过 `message` 属性来设置返回的信息。当我们设置状态码时，会自动的帮我们设置 `message` 的值，例如当我们设置状态码为  `200` 时，`message` 的值就会被设置为 `OK`，当我们设置状态码为 `404` 时，`message` 就会被设置为 `Not Found`

```javascript
app.use(async ctx => {
    ctx.status = 404;
    console.log(ctx.message); // Not Found
});
```

``` info
当我们返回的状态码为 `404` 并且没有设置 `body` 属性时，会向前端返回字符串 `Not Found`。
```

状态码以及对应的默认 `message` 如下(不必记忆)

- 100 "continue"
- 101 "switching protocols"
- 102 "processing"
- 200 "ok"
- 201 "created"
- 202 "accepted"
- 203 "non-authoritative information"
- 204 "no content"
- 205 "reset content"
- 206 "partial content"
- 207 "multi-status"
- 208 "already reported"
- 226 "im used"
- 300 "multiple choices"
- 301 "moved permanently"
- 302 "found"
- 303 "see other"
- 304 "not modified"
- 305 "use proxy"
- 307 "temporary redirect"
- 308 "permanent redirect"
- 400 "bad request"
- 401 "unauthorized"
- 402 "payment required"
- 403 "forbidden"
- 404 "not found"
- 405 "method not allowed"
- 406 "not acceptable"
- 407 "proxy authentication required"
- 408 "request timeout"
- 409 "conflict"
- 410 "gone"
- 411 "length required"
- 412 "precondition failed"
- 413 "payload too large"
- 414 "uri too long"
- 415 "unsupported media type"
- 416 "range not satisfiable"
- 417 "expectation failed"
- 418 "I'm a teapot"
- 422 "unprocessable entity"
- 423 "locked"
- 424 "failed dependency"
- 426 "upgrade required"
- 428 "precondition required"
- 429 "too many requests"
- 431 "request header fields too large"
- 500 "internal server error"
- 501 "not implemented"
- 502 "bad gateway"
- 503 "service unavailable"
- 504 "gateway timeout"
- 505 "http version not supported"
- 506 "variant also negotiates"
- 507 "insufficient storage"
- 508 "loop detected"
- 510 "not extended"
- 511 "network authentication required"

当我们设置 `ctx.body` 为**除 `null` 以外外**的数据时，状态码会被自动设置为 `200`，对应的消息会会被设置为 `OK`。当我们设置 `ctx.body` 为 `null` 时，状态码会被设置为 `204`，对应的消息会被设置为 `No Content`。

#### lastModified、etag

这是用来设置 `LastModified` 与 `ETag` 响应头，`LastModified` 的格式应该为一个 `UTC` 字符串，你也可以直接为 `lastModified` 设置为一个 Date 对象，Koa 内部会帮我们转化为 `UTC` 字符串

```javascript
app.use(async ctx => {
    ctx.lastModified = new Date();
});
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210314165115.png" style="zoom:50%;" />



``` warning
虽然 `Koa` 会将 `Date` 转化为 `UTC` 字符串，但是我发现时区不对，目前没有找到解决办法。
```

通过 `etag` 属性设置 `ETag` 响应头，设置的内容会使用 `""` 包裹起来

```javascript
app.use(async ctx => {
    ctx.etag = "hi";
});
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210314185808.png" style="zoom:50%;" />

#### get、set、append、remove

上述四个方法都是与响应头相关的：

- `get(filed)`：获得指定响应头的内容
- `set`：分为两种
  - `set(field, value)`：设置一个响应头
  - `set(fields)`：设置多个响应头
- `append(field, value)`：向响应头追加内容
- `remove(field)`：删除响应头

```javascript
app.use(async ctx => {
    ctx.set({
        "Content-Type": "text/html",
        "Last-Modified": (new Date()).toUTCString()
    });
    // 删除 Last-Modified 响应头
    ctx.remove("Last-Modified");
    
    // 不要使用 ctx.get，这是获取请求头的方法
    ctx.body = ctx.response.get("Content-Type");
});
```

#### redirect

通过 `redirect(url)` 方法重定向到新的路径

```javascript
app.use(async ctx => {
    ctx.redirect("http://www.baidu.com");
});
```

当我们访问 `http://localhost:3000/` 时会被重定向到 `http://www.baidu.com`，使用 `redirect` 方法进行重定向，会默认设置状态码为 `302`，如果需要更改状态码，可以通过 `status` 属性直接更改，在调用 `redirct` 方法之前或之后都可以。

#### attachment

 调用 `attachment` 方法，会设置 `Content-Disposition` 响应头，浏览器接收到该响应头之后，会对前端返回的内容进行下载。`attachment` 方法接收一个文件名作为参数

```javascript
app.use(async ctx => {
    ctx.body = fs.createReadStream("./girl.jpg");
    ctx.attachment("girl.jpg");
});
```

当我们使用浏览器访问 `http://localhost:3000/` 时，会弹出下面的窗口提示我们进行下载

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210314194752.png" style="zoom:50%;" />

## 路由

我们应当根据不同的请求路径，产生不同的响应，而路由正是定义这些行为的。我们通过对 `url、path、method` 等属性进行判断，然后返回不同的响应

  ```javascript
class Router {
    constructor() {
        this.routers = {}
    }

    // 当请求以 method 方法访问 path 路径时，添加对应的处理函数到 routers
    addHandle(method, path, handle) {
        this.routers[path] = this.routers[path] || {};
        let handles = this.routers[path][method] = this.routers[path][method] || [];
        handles.push(handle);
        // 可链式执行
        return this;
    }

    get(path, handle) {
        return this.addHandle("get", path, handle);
    }

    post(path, handle) {
        return this.addHandle("post", path, handle);
    }

    // 将多个处理函数串联起来
    compose(middleware = []) {
        return function (ctx) {
            function dispatch(i) {
                let fn = middleware[i];
                if (!fn) {
                    return Promise.resolve();
                }
                return Promise.resolve(fn(ctx, dispatch.bind(null, i + 1)));
            }
            return dispatch(0);
        }
    }

    // 返回一个中间件
    routes() {
        return async (ctx, next) => {
            // 找到匹配的路径
            let matchedPath = this.routers[ctx.path];
            // 找到对应方法的处理函数
            const handles = matchedPath[ctx.method.toLowerCase()] || [];
            // 将处理函数串联起来，然后执行
            return this.compose(handles)(ctx, next);
        }
    }
}

module.exports = Router;
  ```

上面我们写了一个 `Router` 类，如下使用

```javascript
const userRouter = new Router();

userRouter.get('/user/name', async (ctx, next) => {
    ctx.body = "Alice";
    await next();
});
```

上面的程序表示，当请求以 `get` 方法访问 `/user/name` 路径时，便执行对应的处理函数，并且可以为同一路径添加多个处理函数，会根据添加的顺序依次执行

```javascript
userRouter.get('/user/name', async (ctx, next) => {
    ctx.body = "Alice";
    await next();
}).get('/user/name', async (ctx, next) => {
    ctx.type = "html";
    await next();
});
```

使用时，调用 `Router` 实例的 `routes` 方法就会返回一个中间件，通过 `app.use` 将该中间件插入即可

```javascript
const Koa = require('koa');
const Router = require('./Router');
const queryString = require('querystring');

let userRouter = new Router();

// 当以 get 方法访问 /user/name 路径时，返回 Alice
userRouter.get('/user/name', async (ctx, next) => {
    ctx.body = "Alice";
    await next();
});

// 当以 post 方法访问 /user/name 路径时，将请求参数返回
userRouter.post('/user/name', async (ctx, next) => {
    const req = ctx.req;
    const postData = await getPostData(req);
    ctx.body = postData;
    await next();
});

// 获得 post 请求参数的函数
const getPostData = async req => {
    return new Promise(resolve => {
        let buffers = [];
        req.on("data", data => {
            buffers.push(data);
        });
        req.on("end", () => {
            let data = Buffer.concat(buffers).toString();
            resolve(queryString.parse(data));
        })
    })
};

const app = new Koa();
// 使用路由中间件
app.use(userRouter.routes());

app.listen(3000);
```

发送 `get` 请求

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210315153816.png" style="zoom:50%;" />

发送 `post` 请求

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210315153900.png" style="zoom:50%;" />

上面是使用了我们自己写了 `Router` 类，支持的功能不多，我们可以使用官网提供的 `Router` 插件，首先进行下载

```bash
npm i @koa/router --save
```

然后引入 `Router`

```javascript
const Router = require('@koa/router');
```

使用方法同上一样。

## 获取请求数据

通过 `ctx.query` 可以获得 `get` 请求的数据

```javascript
app.use(async ctx => {
    const query = ctx.query;
    ctx.body = query;
});
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210315155534.png" style="zoom:50%;" />

获取 `post` 请求的数据需要手动解析请求体

```javascript
const getPostData = async req => {
    return new Promise(resolve => {
        let buffers = [];
        req.on("data", data => {
            buffers.push(data);
        });
        req.on("end", () => {
            let data = Buffer.concat(buffers).toString();
            // 借助了 Node 提供的核心模块 querystring 解析请求参数为对象
            resolve(queryString.parse(data));
        });
    });
};

app.use(async ctx => {
    const postData = await getPostData(ctx.req);
    ctx.body = postData;
});
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210315155847.png" style="zoom:50%;" />

除了手动解析参数外，我们还可以借助于中间件，这里我们使用 `koa-bodyparser`，首先进行安装

```bash
npm i koa-bodyparser --save
```

`koa-bodyparser` 解析请求参数之后会将请求参数放在 `ctx.request.body` 上面，所以我们可以访问 `ctx.request.body` 来获取 `post` 请求参数

```javascript
const bodyParser = require('koa-bodyparser');

app.use(bodyParser());

app.use(async ctx => {
    ctx.body = ctx.request.body;
});
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210315160802.png" style="zoom:50%;" />

## 暴露静态资源

对于我们在网页里面引用的 JavaScript、CSS、图片等静态资源文件，它也会走我们的路由，所以我们也要为它们写处理函数，其实就是直接将文件返回。我们首先看如果不做处理的情况，首先有这个一个 HTML 文件

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <link rel="stylesheet" href="/public/css/index.css">
</head>
<body>
    Hello World!
</body>
</html>
```

在 `HTML`  文件中引用了一个 `CSS` 文件，`CSS` 文件内容如下

```css
body {
    color: red;
    font-size: 40px;
}
```

上面只是设置了字体的颜色和大小，浏览器会向服务器请求 `CSS` 文件，但是我们服务器没有对这种静态资源的请求做处理，所以并不能返回 `CSS` 文件，服务器端代码如下，只是直接返回了一个 `HTML` 文件

```javascript
const Koa = require('koa');
const fs = require('fs');
const app = new Koa();
const path = require('path');
const Router = require('@koa/router');

const router = new Router();

router.get('/', async (ctx, next) => {
    ctx.type = 'html';
    ctx.body = fs.createReadStream(path.resolve('public/index.html'));
    await next();
});

app.use(router.routes());

app.listen(3000);
```

现在我们使用浏览器访问

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210315201940.png" style="zoom:50%;" />

字体的颜色和大小没有变化，说明没有加载到 `CSS` 文件，并且控制台也显示文件资源加载失败。现在我们使用一个中间件 `koa-static` 来处理静态资源，首先进行下载

```bash
npm i koa-static --save
```

使用如下

```javascript
const Koa = require('koa');
const fs = require('fs');
const app = new Koa();
const path = require('path');
const Router = require('@koa/router');
// 使用 koa-static 中间件
const server = require('koa-static');

const router = new Router();
router.get('/', async (ctx, next) => {
    ctx.type = 'html';
    ctx.body = fs.createReadStream(path.resolve('public/index.html'));
    await next();
});

// 将当前目录当做静态服务的根目录
app.use(server(path.resolve('.')));
app.use(router.routes());

app.listen(3000);
```

再次访问

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210315203247.png" style="zoom:50%;" />

样式生效了，说明 `koa-static` 起作用了。上面我们使用当前目录作为静态资源的根目录，如果我们使用 `public` 目录作为静态资源的根目录

```javascript
app.use(server(path.resolve('public')));
```

那么引用 `CSS` 文件的方式需要修改为

```html
<link rel="stylesheet" href="/css/index.css">
```

## 文件上传

本节研究如何处理上传文件，前端上传文件的代码如下

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <input type="file" id="upload">

    <script>
        const upload = document.getElementById("upload");
        upload.addEventListener("change", event => {
            // 获得上传的文件
            let file = event.target.files[0];
            // 添加到 FormData 对象中
            let data = new FormData();
            data.append("file", file);

            // 上传文件
            fetch('http://localhost:3000/upload', {
                method: 'post',
                body: data,
                // 设置上传的文件类型为 multipart/form-data，表示上传的为文件
                // headers: {
                //     'Content-Type': 'multipart/form-data'
                // }
            }).then(res => {
                console.log(res);
            }).catch(err => {
                console.error(err);
            })
        })
    </script>
</body>
</html>
```


``` warning
这里记录一个坑，在使用 fetch 上传文件时，不要手动设置 `'Content-Type': 'multipart/form-data'`，因为还需要设置分界字符，这个分界字符是随机生成，所以 `Content-Type` 头部就由 `fetch` 自己去生成，否则后端的一些中间件不能解析内容。
```

为了解析上传的数据，我们首先需要了解 `multipart/form-data` 的数据格式。这里我们假设上传了两个字段，第一个字段的名称为 `file`，它是一个文本文件，其中的内容为 `Hello World!`；第二个字段为 `name`，它的值为 `Alice`。那么它们形成的请求体的数据如下

```http
------WebKitFormBoundaryHdpL0f6HyVyJAFok
Content-Disposition: form-data; name="file"; filename="hello.txt"
Content-Type: text/plain

Hello World!
------WebKitFormBoundaryHdpL0f6HyVyJAFok
Content-Disposition: form-data; name="name"

Alice
------WebKitFormBoundaryHdpL0f6HyVyJAFok--
```

每个字段之间都会使用一个分界线分开，这个分界线的组成是 `------WebKitFormBoundary` 加上一个随机的 16 位哈希，在每个字段中也分为两部分，第一部分是字段有关的请求头，例如 `Content-Type`、`Content-Disposition` ，第二部分就是字段的具体内容，两部分使用空行进行隔开。

``` tip
我尝试过自己解析数据，但是花费数个小时，还是没有成功，因此放弃，感兴趣可以尝试自己解析！
```
这里介绍使用中间件 `koa-body` 来获得上传的文件首先进行下载

```javascript
npm i koa-body --save
```

然后使用便可以使用了

```javascript
const koaBody = require('koa-body');
app.use(koaBody({
    multipart: true
}));
```

`koa-body` 会将上传的文件设置在 `ctx.request.files` 属性，我们通过 `files.file` 属性来获得上传的文件，如果上传的是一个文件，那么它就是一个 `File` 对象，如果上传多个文件，那么它就是一个由 `File` 对象组成的数组；其他表单请求字段设置在 `ctx.request.body` 属性上。

`koa-body` 会先将文件保存在 `C:\\Users\\User\\AppData\\Local\\Temp\\` 目录下，具体的路径可以由 `File` 对象的 `path` 属性获得，通过 `name` 属性可以获得上传的文件名，通过 `type` 属性可得知上传的文件类型

```javascript
const Koa = require('koa');
const Router = require('@koa/router');
const koaBody = require('koa-body');

const fs = require('fs');
const path = require('path');

const app = new Koa();
const router = new Router();
router.post('/upload', async (ctx, next) => {
    const file = ctx.request.files.file;
    // 将接收到的文件保存在当前目录
    fs.createReadStream(file.path).pipe(fs.createWriteStream(path.resolve(file.name)));
    ctx.body = "上传成功";
    await next();
});

app.use(koaBody({
    multipart: true
}));
app.use(router.routes());

app.listen(3000);
```

## 参考文章

- [Koa官网](https://koajs.com/)
- [Koa进阶学习笔记](https://chenshenhai.com/koa2-note)
- [玩转Koa-核心原理分析](https://zhuanlan.zhihu.com/p/53609310)
- [手写@koa/router源码](https://segmentfault.com/a/1190000038182946)
- [NodeJs koa2实现文件上传](https://www.jianshu.com/p/34d0e1a5ac70)
- [Error when POST file multipart/form-data #505](https://github.com/github/fetch/issues/505)
- [踩坑篇--使用 fetch 上传文件](https://zhuanlan.zhihu.com/p/34291688)



