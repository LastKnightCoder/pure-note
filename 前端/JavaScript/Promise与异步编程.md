---
tags: [JavaScript, ES6/Promise]
---

## 异步编程背景

JavaScript 是单线程的，意味着同一时刻只有一处代码在执行，所以它的线程不能被阻塞住，为了达到这一个目的，JavaScript 使用任务队列的机制来实现异步编程。异步编程一般有两种模式

- 事件模型
- 回调模型

事件模型是指为某事件绑定一个函数，当事件触发时执行此函数，一般在 DOM 编程中比较常见，例如为按钮绑定点击事件

```javascript
document.getElementById("btn").addEventListener("click", (event) => {
  // 处理点击事件
});
```

回调模型在 Node.js 的 I/O 操作中比较常见，在读取文件时，我们肯定不能阻塞线程等待文件读取完毕，然后进行操作，这里的解决办法是在调用读取文件的函数时，传入一个函数，这个函数在文件被读取完成时会被调用，并且读取到的数据会作为参数传入该函数

```javascript
const fs = require("fs");

// 第二个参数是处理数据的回调函数，它会在文件读取完毕时执行
fs.readFile("a.txt", (err, data) => {
  // 处理数据
});

// 下面的程序不用等到文件读取完毕即可执行
// 如果下面的程序要用到读取到的文件，那么它应该放在回调函数中
```

当回调函数嵌套较多时，造成代码十分难以阅读，这种情况称之为回调地狱，为了解决这个问题，在 ES6 中提出了 `Promise`，进一步在 ES7 提出了 `async ... await` 语法。

## Promise 基础

`Promise` 的基本语法如下

```javascript
new Promise(fn1).then(fn2);
```

其中 `fn1` 和 `fn2` 都是两个函数，`fn1` 接收两个参数 `resolve` 和 `reject`，这两个参数也都是函数，只有当在 `fn1` 中调用了 `resolve` 方法后，`fn2` 方法才会被执行。所以 `Promise` 可以保证函数执行的顺序，并且 `fn1` 向 `resolve` 传入的参数被被传递给 `fn2`

```javascript
let promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve("Hello");
  }, 1000);
});

promise.then((data) => {
  console.log(data);
});
```

上述代码会在 `1s` 后打印出 `Hello`，因为在 `1s` 后才执行 `resolve` 函数，此时 `fn2` 才会被执行，并且 `Hello` 会作为参数传入 `fn2`。

现在我们比较一下回调模型与 `Promise` 模型

```javascript
const fs = require("fs");

fs.readFile("a.txt", (err, data) => {
  if (err) return;
  fs.writeFile("b.txt", data, (err) => {});
});
```

```javascript
let promise = new Promise((resolve, reject) => {
  fs.readFile("a.txt", (err, data) => {
    if (err) reject(err);
    resolve(data);
  });
});

promise.then((data) => {
  fs.writeFile("b.txt", data);
});
```

上面的版本为回调模型版本，下面的版本为 `Promise` 版本，从写法上看回调版本是一层嵌套一层以此来保证同步性，如果有较多的嵌套的话，代码肯定十分难读，体验感十分不好；而 `Promise` 的写法则比较像同步的写法(写法上与同步的写法类似，但是实际上还是异步的)，即使嵌套再多，可读性也十分的好。

## 错误处理

如果在 `fn1` 中出现了错误怎么办，这个时候就需要我们即将介绍的 `reject`，其实 `Promise` 还有一种用法

```javascript
new Promise(fn1).then(fn2, fn3);
```

`then` 可以接收两个函数，当在 `fn1` 中调用 `resolve` 方法后，`fn2` 被执行，当在 `fn1` 中执行 `reject` 方法后，`fn3` 被执行。`resolve` 表示函数正常执行完毕，用于传递数据，而 `reject` 表示函数出错，一般用于传递错误，向 `reject` 传递的参数会被传递给 `fn3`，所以 `fn2` 是 `fn1` 成功执行后的处理逻辑，而 `fn3` 是 `fn1` 执行出错后的处理逻辑

```javascript
let promise = new Promise((resolve, reject) => {
  fs.readFile("a.txt", (err, data) => {
    // 如果出错，则调用 reject 方法，并将错误信息传入
    if (err) reject(err);
    resolve(data);
  });
});

promise.then(
  (data) => {
    fs.writeFile("b.txt", data);
  },
  (err) => {
    console.log(err);
  }
);
```

其实还有一种写法

```javascript
new Promise(fn1).then(fn2).catch(fn3);
```

这种写法与上面的写法是一样的

```javascript
promise
  .then((data) => {
    fs.writeFile("b.txt", data);
  })
  .catch((err) => {
    console.log(err);
  });
```

## 链式调用

下面继续介绍 `Promise` 的新模式，链式调用

```javascript
new Promise(fn1).then(fn2).then(fn3).then(fn4);
```

因为每一个 `then` 方法都返回一个 `Promise` 对象，所以可以进行链式的调用

```javascript
let promise = new Promise((resolve, reject) => {
  console.log(1);
  resolve();
}).then(() => {
    console.log(2);
  })
  .then(() => {
    console.log(3);
  })
  .then(() => {
    console.log(4);
  });
```

上面的程序会依次打印出 `1 2 3 4`。现在有一个问题是链式调用如何传递值，答案是通过返回值

```javascript
let promise = new Promise((resolve, reject) => {
  resolve("Hello");
}).then((data) => {
    console.log(data); // Hello
    // 通过 return 将数据传递到下面的方法中
    return "World";
  })
  .then((data) => {
    console.log(data); // World
  });
```

上述的打印结果为

```javascript
Hello;
World;
```

我们看一个简单 `then` 方法的实现来理解上面的逻辑

```javascript
function then(fn) {
  return new Promise((resolve, reject) => {
    const result = fn();
    resolve(result);
  });
}
```

上面只是为了理解代码的简单实现，并不是 `then` 方法的真正实现。

但是如果传入 `then` 中的函数返回的是一个 `Promise` 对象的话就有所不同，后面链式调用传入 `then` 中的函数必须等到返回 `Promise` 执行 `resolve` 方法之后才会执行，并且向 `resolve` 传入的值会被传递

```javascript
let promise = new Promise((resolve) => {
  resolve();
})
  .then(() => {
    return new Promise((resolve) => {
      setTimeout(() => {
        resolve("Hello World");
      }, 1000);
    });
  })
  .then((data) => {
    console.log(data); // Hello World
  });
```

上面 `1s` 之后会打印出 `Hello World`。我们进一步改进 `then` 方法的实现来理解上述的执行结果

```javascript
function then(fn) {
  return new Promise((resolve, reject) => {
    const result = fn();
    if (result instanceof Promise) {
      result.then(resolve);
    } else {
      resolve(result);
    }
  });
}
```

## finally

在链式调用的最后一般调用 `finally` 方法，该方法无论上面的链式调用成功执行，或者是链式调用的过程中抛出了错误，传递给 `finally` 方法的函数最终都会被执行

```javascript
let promise = new Promise((resolve, reject) => {
  resolve();
})
  .then(() => {
    console.log("then 中函数被执行");   // √
  })
  .catch(() => {
    console.log("catch 中函数被执行");
  })
  .finally(() => {
    console.log("finally 中函数被执行"); // √
  });
```

```javascript
let promise = new Promise((resolve, reject) => {
  reject();
}).then(() => {
    console.log("then 中函数被执行");
  })
  .catch(() => {
    console.log("catch 中函数被执行");   // √
  })
  .finally(() => {
    console.log("finally 中函数被执行"); // √
  });
```

由于这一特性，`finally` 方法一般被用于进行资源的回收。

## 静态方法

本节讲述 `Promise` 的静态方法，不过在讲解静态方法之前，我们谈一谈 `Promise` 对象的状态，`Promise` 对象有三种状态：

- `pendding`
- `resolved`(有的地方称为 `fulfilled`)
- `rejected`

在调用 `resolve` 或 `reject` 方法之前，`Promise` 对象处于 `pendding` 状态，此时传入 `then` 中的函数不能被执行，当调用 `resolve` 方法后，状态由 `pendding` 变为 `resolved`，当调用 `reject` 后，状态由 `pendding` 变为 `rejected`，一旦状态变为 `resolved` 或 `rejected` 之后，状态不可改变，并且 `resolved` 和 `rejected` 状态只能由 `pendding` 状态转变而来。

当 `Promise` 的状态为 `resolved` 时，传入 `then` 方法的第一个函数就可以执行了，当 `Promise` 的状态为 `rejected` 时，传入 `then` 的第二个函数或者传入 `catch` 方法的函数就可以执行了。

下面我们介绍 `Promise` 的静态方法

- `Promise.resolve`
- `Promise.all`
- `Promise.allSettled`
- `Promise.race`

### Promise.resolve

`Promise.resolve` 返回一个 `Promise`，根据传入参数的不同，返回不同的值，分为三种情况

- 传入一个 `Promise` 对象，直接返回该 `Promise` 对象
	```javascript
	let promise = new Promise((resolve, reject) => {});
	
	let res = Promise.resolve(promise);
	console.log(res === promise); // true
	```

- 传入一个带有 `then` 方法的对象，首先执行 `then` 方法，并且将 `resolve` 和 `reject` 传入，返回的 `Promise` 状态由 `then` 方法是否调用 `resolve` 与 `reject` 方法决定，并且向 `resolve` 和 `reject` 传递的参数会被传递
 
	```javascript
	Promise.resolve({
	  then(resolve, reject) {
	    resolve("Hello World!");
	  },
	}).then((data) => {
	  console.log(data); // Hello World!
	});
	```

- 传入其他值时，返回一个 `resolved` 状态的 `Promise`，并且传入的值会作为 `resolv ` 方法的参数被传递

	```javascript
	Promise.resolve(3).then((data) => {
	  console.log(data); // 3
	});
  ```

经过上面的讲解，可以简单写一个 `Promise.resolve` 的实现

```javascript
Promise.resolve = (value) => {
  if (value instanceof Promise) {
    return value;
  }
  return new Promise((resolve, reject) => {
    if (typeof value === "object" && typeof value.then === "function") {
      value.then(resolve, reject);
    } else {
      resolve(value);
    }
  });
};
```

### Promise.all

`Promise.all` 接收一个 `Promise` 对象组成的数组，返回一个 `Promise`，只有当数组中的所有 `Promise` 都 `resolved` 后，返回的 `Promise` 才会变为 `resolved` 状态，并且将所有 `Promise` 传递的结果封装为数组传递下去

```javascript
let promise1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve(1);
  }, 1000);
});

let promise2 = Promise.resolve(2);

Promise.all([promise1, promise2]).then((results) => {
  console.log(results); // 1s 后打印 [ 1, 2 ]
});
```

一旦数组中的 `Promise` 有任一个变为 `rejected`，返回的 `Promise` 就会变为 `rejected` 状态

```javascript
let promise1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve(1);
  }, 1000);
});

// rejected 状态的 Promise
let promise2 = Promise.reject(2);

Promise.all([promise1, promise2])
  .then((results) => {
    console.log(results);
  })
  .catch((err) => {
    console.log(err); // 2，reject传递的参数
  });
```

所以 `all` 中的 `Promise` 要么一起执行成功，要么全部失败，我们拿不到部分成功的 `Promise` 结果。

### Promise.allSettled

`Promise.allSettled` 方法是解决 `Promise.all` 一旦一个 `Promise` 变为 `rejected`，其他变为 `resolved` 状态 `Promise` 传递的结果就仿佛被吞掉了

```javascript
Promise.allSettled([promise1, promise2]).then((results) => {
  console.log(results); // [
                        //    { status: 'fulfilled', value: 1 },
                        //    { status: 'rejected', reason: 2 }
                        // ]
});
```

**`Promise.allSettled` 方法会返回一个 `resolved` 状态的 `Promise`，**数组中的 `Promise` 的执行结果会以对象的形式添加到 `results` 数组中，`status` 表示 `Promise` 最终的状态，`value` 和 `reason` 分别表示成功执行时传递的值以及执行出错时传递的错误原因。

### Promise.race

`Promise.race` 方法也是接收一个 `Promise` 组成的数组，返回一个 `Promise`，当数组中有任一 `Promise` 的状态变为 `resolved` 或者 `rejected`，返回的 `Promise` 就会相应的变为 `resolved` 或 `rejected`。正如 `race` 所暗示的，是多个 `Promise` 在竞争，最终选择最快的那个

```javascript
let promise1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve(1);
  }, 1000);
});

let promise2 = Promise.resolve({
  then(resolve, reject) {
    setTimeout(() => {
      resolve(2);
    }, 2000);
  },
});

// promise1 会率先执行 resolve，所以得到的结果是promise1 传递的结果
Promise.race([promise1, promise2]).then((data) => {
  console.log(data); // 1
});
```

## Promise 与生成器

上面使用 `Promise` 编写的异步代码已经与同步代码很相似了，但是我们可以将 `Promise` 与 生成器结合起来，使得代码的写法看起来更加的像同步的写法，例如

```javascript
let promise = new Promise((resolve, reject) => {
  fetch("/id/2").then((name) => {
    resolve(name);
  });
})
  .then((name) => {
    return new Promise((resolve) => {
      fetch(`/name/${name}`).then((score) => {
        resolve(score);
      });
    });
  })
  .then((score) => {
    console.log(score);
  });
```

上面的代码是先根据 `id` 向服务器请求 `name`，然后根据 `name` 向服务器请求获得的分数，最后打印出分数，我们希望能有更加同步的写法，例如

```javascript
asyncTaskRunner(*() => {
    const name = yield fetch("id/2");
    const score = yield fetch(`/name/${name}`);
    console.log(score);
})
```

这种写法可以通过 `Promise` 与 `Generator` 来做到。`asyncTaskRunner` 的实现如下

```javascript
function asyncTaskRunner(generator) {
  const iterator = generator();

  function handle(result) {
    if (result.done) return;

    const resultValue = result.value;

    if (resultValue instanceof Promise) {
      resultValue
        .then((data) => {
          handle(iterator.next(data));
        })
        .catch((err) => {
          iterator.throw(err);
        });
    }
  }

  try {
    handle(iterator.next());
  } catch (err) {
    iterator.throw(err);
  }
}
```

在 ES7 中提出了 `async ... await` 语法，它能让我们以同步的方式写出异步代码

```javascript
(async () => {
  const name = await fetch("/id/2");
  const score = await fetch(`/name/${name}`);
  console.log(score);
})();
```

只需要在函数参数列表前加入 `async` 关键字，将 `yield` 换为 `await` 即可以同步方式写出优雅的异步代码。
