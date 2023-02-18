---
tags: [JavaScript/BOM]
---

`BOM` 指的是浏览器对象模型，它定义了如何操作浏览器(不是网页)，但是事实上并没有有关于 `BOM` 的标准，很多浏览器厂商对其实现不一致，下面的例子都是基于 `Chrome` 浏览器的，但是大多数对于其他的浏览器也是成立的。

在本篇中会介绍与浏览器有关的几个对象，包括

- `window`
- `location`
- `history`
- `navigator`

## window

`window` 对象有两重角色：

1. 它是 `JavaScript` 访问浏览器的接口
2. 它是在 `EcmaScript` 规范所规定的 `Global` 对象的实现

### 全局作用域

所有在全局作用域中声明的变量都是 `window` 的属性，例如

```javascript
let x = 1;
function sayHello() {
    console.log("Hello!");
}

console.log(window.x); // 1
window.sayHello(); // Hello!
```

所以如果一个函数是一个全局变量，在函数内部使用了 `this`，那么这个 `this` 代表就是 `window` 对象，例如

```javascript
function copy(x) {
    this.x = x;
}

copy(5);
```

上面 `copy` 函数的调用相当于 `window.copy()`，所以 `copy` 函数中的 `this` 就是 `window` 对象，调用上面的 `copy(5)` 以后，在 `window` 对象下有一个属性 `x`，它的值为 `5`

```javascript
console.log(window.x); // 5
```

全局变量与 `window` 对象上的属性有一点不同，全局变量不能通过 `delete` 操作符删除，而 `window` 上的对象可以通过 `delete` 操作符删除

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/202204181408302022-04-18-14-08-32.png" style="zoom:50%"/>

### 窗口位置

通过 `window` 的以下属性，可以知道窗口的位置

| 属性                        | 功能                     |
| --------------------------- | ------------------------ |
| `screenLeft` 或者 `screenX` | 表示窗口离屏幕左边的距离 |
| `screenTop` 或者 `screenY`  | 表示窗口离屏幕顶部的距离 |

建议使用 `screenLeft` 和 `screenTop` 两个属性。

### 窗口大小

通过以下属性获得窗口的大小

| 属性          | 功能                             |
| ------------- | -------------------------------- |
| `innerWidth`  | 页面的宽度                       |
| `innerHeight` | 页面的高度，不包括工具栏和调试台 |
| `outerWidth`  | 浏览器的宽度                     |
| `outerHeight` | 浏览器的高度，包括工具栏和调试台 |

因为浏览器在横向没有工具栏等部件，所以 `innerWidth` 和 `outerWidth` 应该是相同的。

除了通过上面的属性得到窗口的大小以外，还可以通过 `document.documentElement` 对象的 `clientWidth` 和 `clientHeight` 属性以及 `document.body` 对象的 `clientWidth` 和 `clientHeight` 属性来得到页面的窗口信息，不过我试验了一下，发现它们的值并不相等，所以我建议还是只使用 `innerXxx` 和 `outerXxx`。

### 打开窗口

通过 `window.open` 方法可以打开一个新的窗口，该方法接收四个可选参数

| 参数      | 含义                                                         |
| --------- | ------------------------------------------------------------ |
| `url`     | 要加载的 `URL`                                               |
| `name`    | 规定窗口的 `target` 属性或者设定窗口的名字                   |
| `feature` | 特性字符                                                     |
| `replace` | 表示在 `history` 中列表中是新增一条记录，还是替代当前的记录<br /><ul><li><code>true</code>：替代</li><li><code>fasle</code>：新增</li></ul> |

这里解释一下 `name` 属性，它可以用来设置窗口的 `target` 属性，或者表示打开窗口的名字。当为以下值时，表示规定的是 `target` 属性

- `_self`
- `_parent`
- `_blank`
- `_top`

当为其他值时，表示的是打开的窗口的名字。

`feature` 字符串是用来表示在新窗口中有哪些特性，可以设置如下值

| 特性         | 值              | 功能                                                   |
| ------------ | --------------- | ------------------------------------------------------ |
| `fullscreen` | yes/no \|\| 1/0 | 窗口是否最大化，IE Only                                |
| `width`      | 数值            | 窗口的宽度，最小值为 100                               |
| `height`     | 数值            | 窗口的高度，最小值为 100                               |
| `left`       | 数值            | 窗口离左边屏幕的距离，不能为负值                       |
| `top`        | 数值            | 窗口离顶部屏幕的距离，不能为负值                       |
| `location`   | yes/no \|\| 1/0 | 是否显示地址栏，Opera Only                             |
| `menubar`    | yes/no \|\| 1/0 | 是否显示菜单栏                                         |
| `resizable`  | yes/no \|\| 1/0 | 是否可拖动窗口边框改变浏览器大小，only IE              |
| `scrollbars` | yes/no \|\| 1/0 | 如果内容在视口显示不下，是否允许滚动，IE Firefox Opera |
| `toolbar`    | yes/no \|\| 1/0 | 是否显示工具栏，IE Firefox                             |
| `status`     | yes/no \|\| 1/0 | 是否显示状态栏                                         |

下面给出一个示例写法

```javascript
window.open("https://www.baidu.com", "baidu", "width=400,height=400,resizeable=yes", true);
```

```note
需要注意的是，特性字符串中不能有空格，要同时设置 `width` 和 `height` 才会起效果，单独设置一个是没有效的。
```

```tip
实际感受了一下特性字符串，感觉除了 `width` `height` `top` `left` 等属性设置有效，其他属性设置没有效果。
```

`window.open()` 方法会返回新打开窗口的 `window` 对象(如果失败的话会返回 `null`)，该对象的 `opener` 属性和 `parent` 属性都指向原窗口的 `window` 对象，即

```javascript
let newWindow = window.open();
newWindow.opener == newWindow.parent == window; // true
```

因为新窗口的 `window` 对象中的 `opener` 和 `parent` 属性保持着对原窗口的引用，可以使用它来进行两个窗口的通信，如果两个窗口之间不要通信的话，我们需要手动将 `opener` 属性设置为 `null`，以免发生安全问题

```javascript
newWindow.opener = null; // 此时 newWindow.parent 也为 null
```

### 定时器

在 `JavaScript` 中有两种定时器，分别为

- `setTimeout`
- `setInterval`

`setTimeout` 可以看做是延时函数，它接收两个参数，第一个参数是一个回调函数，第二个参数是时间，单位是毫秒，它会在经过指定的时间之后，来调用传入的回调函数，所以 `setTimeout` 可以看做是一个延时器，下面的程序会在 `1s` 后在控制台打印 `Hello World!`

```javascript
setTimeout(function() {
    console.log("Hello World!");
}, 1000);
```

`setTimeout` 函数会返回一个值，表示该定时器的 `id`，这个 `id` 的作用一般是用来停止定时器，只要将这个 `id` 作为参数传入到 `clearTimeout(id)` 中，就会停止这个计时器

```javascript
const timerId = setTimeout(function() {
    console.log("Hello World!");
}, 1000);
clearTimeout(timerId);
```

在 `1s` 后，我们在控制台看到打印出了 `Hello World!`，因为在 `1s` 以内，这个定时器就被回收了。

另一个定时器是 `setInterval`，它也接收两个参数，第一个参数为一个回调函数，第二个参数为时间，单位为毫秒，它会每隔设定的时间就会调用一次回调函数，如下代码会每隔 `1s` 就在控制台打印一次 `Hello World!`

```javascript
setInterval(function() {
    console.log("Hello World!");
}, 1000);
```

使用 `setInterval` 要十分的小心，因为如果我们没有清理该定时器的话，它会一直工作下去，直到页面卸载，如果我们忘记清理这个计时器的话，那么它会一直在后台运行，占用资源。

同 `setTimeout` 的一样，该函数也返回一个 `id`，作用也是用来停止计时器的，将它作为参数传递给 `clearInterval(id)` 即可停止这个计时器。下面给出一个例子

```javascript
const max = 100;
let count = 0;
let timerId = null;

function increment() {
    count++;
    if (count > max) {
        clearInterval(timerId);
    }
}

timer = setInterval(increment, 1000);
```

上面的代码每隔一秒对 `count` 进行加一，当 `count > max` 时，我们停止了该计时器。

其实上面的代码也可以使用 `setTimeout` 实现，如下

```javascript
const max = 100;
let count = 0;
function increment() {
    count++;
    if (count <= max) {
        setTimeout(increment, 1000);
    }
}

setTimeout(increment, 1000);
```

仔细阅读代码，该代码的功能同上面是一样的，使用 `setTimeout` 的好处就是不用跟踪定时器的 `id`，因为 `setTimeout` 每次执行完函数后，它会自动的停止，**一般认为使用 `setTimeout` 来模拟 `setInterval` 是一种最佳实践**，所以最好不用 `setInterval`，以免忘记停止定时器，浪费资源。

### 对话框

浏览器通过以下三种方法调用系统对话框向用户显示消息

- `alert`
- `confirm`
- `prompt`

## location

### 属性

`location` 对象不仅保存当前文档的 `URL` 信息，并且还将 `URL` 解析为独立的片段，假设对于 `URL` 为 `https://app.diagrams.net:80/#G1FX9ydf3wgzNVe15n9jNgaGP_XaJyEYKz?login=true`，`location` 对象对应的属性如下

| 属性名     | 例子                                                         | 说明                                                         |
| ---------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| hash     | \#G1FX9ydf3wgzNVe15n9jNgaGP_XaJyEYKz                        | 返回 URL 中的 hash                                           |
| host     | app.diagrams.net:80                                       | 返回服务器名称和端口号(如果有的话)                           |
| hostname | app.diagrams.net                                           | 返回服务器名称                                               |
| href     | https://app.diagrams.net:80/<br/>\#G1FX9ydf3wgzNVe15n9jNgaGP_XaJyEYKz?login=true | 当前页面的完整地址                                           |
| pathname | /                                                          | URL 中的目录或文件名                                         |
| port     | 80                                                       | 返回 URL 中指定的端口号，如果 URL 中没有指定，那么返回空字符串 |
| protocol | https:                                                     | 页面使用的协议                                               |
| serach   | ?login=true                                               | URL 中的查询字符串                                           |

### assign

我们可以使用 `location` 对象来改变当前页面的 `URL`，首先最常用的方式就是使用 `assign`，该方法接收一个 `URL`

```javascript
location.assign("https://www.baidu.com");
```

这样就会马上打开 `URL`，并在 `history` 对象中产生一条新的记录。

我们还可以直接修改 `location.href` 属性，如

```javascript
location.href = "https://www.baidu.com";
```

或者直接修改 `location` 

```javascript
window.location = "https://www.baidu.com"
```

其实不管是使用 `location.href` 还是直接修改 `location`，在底层都会调用 `assign()` 方法，所以上面三行代码的效果都是一样的。

除了跳转到新的页面，我们还可以设置 `location` 对象的其他属性，来改变当前加载的页面，每次修改 `location` 的属性(除 `hash` 外)，页面都会以新的 `URL` 重新加载。

### replace

我们还可以通过 `replace` 方法来改变当前页面的 `URL`，它也接收一个 `URL` 地址，并跳转到该地址，不过与 `assign` 不同的是，它不会再 `history` 对象产生一条新的记录，而是使用当前的记录替代之前的记录，如下

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/location.drawio2022-04-18-14-21-07.svg" style="zoom:50%"/>

## history

`history` 保存着当前页面访问过的页面的历史记录，出于安全考虑，安全人员无法得知用户浏览过的 `URL`，不过可以根据通过 `go` 方法在历史记录之间任意跳转。

该方法接收一个整数值，正数表示向前跳转，负数表示向后跳转，如下

```javascript
history.go(1); // 向前跳转一步
history.go(-2); // 向后跳转两步
```

除了给 `go` 方法传递数字以外，还可以传递字符串，浏览器会跳到历史记录中包含该字符串的页面中最近的一个，可能前进，也可能后退，如果历史记录中不包含该字符串，那么什么也不做

```javascript
history.go("baidu"); // 跳转到最近的包含 baidu 的页面
```

`history` 提供了两个简写方法 `back()` 和 `forward()` 来代替 `go()`，这两个方法分别代表后退一步和前进一步。

如果在后退到某页面时，出于某种原因，向 `history` 中添加了一条新的记录，那么此页面之后的记录全部作废，如下图所示

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/history.drawio2022-04-18-14-24-11.svg" style="zoom:50%"/>


