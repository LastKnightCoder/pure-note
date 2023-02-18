---
tags: [JavaScript, ES6/Proxy, ES6/Reflect]
---

在 ES6 之前我们很难模仿一个内置的对象的行为，例如模仿数组，当添加一个新的元素时，`length` 的长度 `+1`，当改变 `length` 的大小时，也会对数组中的元素产生影响，我们很难通过现有的语法去做到这件事情，在 ES6 中给我们提供了 `Proxy`，使得开发者能够创建内建的对象。

`Proxy` 可以为对象做一层代理，拦截对象的一些操作，例如为对象的属性赋值，访问对象的属性值，使用 `in` 操作判断对象是否存在某个属性等等，这些操作都可以被拦截，从而可以对要访问的属性进行验证从而决定采用什么行为。

```javascript
let target = {};
let proxy = new Proxy(target, {});

proxy.name = "Alice";
console.log(proxy.name); // Alice
console.log(target.name); // Alice

proxy.name = "Bob";
console.log(proxy.name); // Bob
console.log(target.name); // Bob
```

上述对 `proxy` 的操作会被无条件的转发到 `target`，`proxy` 中并不会存储 `name` 这个属性，所有的操作都是通过转发到 `target` 完成的。所以当我们为 `proxy.name` 赋予一个新值，这个操作会被转发到 `target`，也就是为 `target.name` 赋予一个新值，当我们访问 `proxy.name` 属性的时候，这个操作也会被转发到 `target`，返回的是 `target.name` 的值。

`Proxy` 可以拦截下面的一些行为

<img src="https://user-images.githubusercontent.com/29890094/106619623-becf4600-65ab-11eb-991e-5bf2eaff379f.png" style="zoom:50%;" />

下面就具体介绍用法。

## set

当我们为对象的属性赋值时，可以通过 `set` 方法来拦截这个行为，`set` 方法接收四个参数：

- `trapTarget`：`target` 对象
- `key`：要写入的属性
- `value`：被写入的属性值
- `receiver`：操作发生的对象，例如 `proxy.name` 设置属性值时，`receiver` 就是 `proxy`

```javascript
let target = {};

let proxy = new Proxy(target, {
  set(trapTarget, key, value, receiver) {
    if (key === "name") {
      return false;
    }
    Reflect.set(trapTarget, key, value, receiver);
  },
});
```

上面我们拦截了为属性赋值的请求，如果当我们为 `name` 属性赋值时，那么什么都不做，否则就直接将操作转发给 `target`，上面 `Reflect.set(traptarget, key, value, receiver)` 就是将操作转发给 `target`，`Reflect.set` 接收的参数与 `set` 方法一样

```javascript
proxy.name = "Alice";
console.log(proxy.name);  // undefined
console.log(target.name); // undefined

proxy.age = 10;
console.log(proxy.age);   // 10
console.log(target.age);  // 10
```

上面我们通过 `proxy` 为 `name` 属性赋值，发现并没有成功，打印出的结果是 `undefined`，因为我们拦截了赋值请求，无法为 `name` 属性赋值；但是当我们为其他属性赋值时，它会被直接转发到 `target`，所以赋值时成功的。

## get

当我们访问对象的属性值时，可以通过 `get` 方法拦截这个请求，`get` 方法接收三个参数

- `trapTarget`
- `key`
- `receiver`

参数的含义同上，不多做解释。

```javascript
let proxy = new Proxy(target, {
  // ...
  get(trapTarget, key, receiver) {
    if (!(key in receiver)) {
      throw new Error("该属性不存在");
    }
    Reflect.get(trapTarget, key, receiver);
  },
});
```

上面我们拦截了访问对象属性值的请求，首先我们会查找对象中是否存在这个属性，如果不存在则会抛出错误(而不是返回 `undefined`)，如果存在则直接通过 `Reflect.get` 转发给 `target`

```javascript
proxy.gender = "male";
console.log(proxy.sex);
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting/20210121155508.png" style="zoom:50%;" />

## has

当我们使用 `in` 操作符判断对象中是否存在某个属性时，我们可以使用 `has` 方法拦截这个请求，`has` 方法接收两个参数

- `trapTarget`
- `key`

```javascript
let target = {
  name: "Alice",
  age: 18,
};

let proxy = new Proxy(target, {
  has(trapTarget, key) {
    if (key === "age") {
      return false;
    }
    return Reflect.has(trapTarget, key);
  },
});
```

当查找的 `key` 的值是 `age` 时，我们直接返回 `false`，否则直接转发到 `target` 上

```javascript
console.log("name" in proxy); // true
console.log("age" in proxy); // false
```

## deleteProperty

`delete` 操作用以删除对象的属性，我们可以通过 `deleteProperty` 来拦截这一行为，`deleteProperty` 接收两个参数

- `trapTarget`
- `key`

```javascript
let target = {
  name: "Alice",
  age: 18,
};

let proxy = new Proxy(target, {
  deleteProperty(trapTarget, key) {
    if (key === "age") {
      return false;
    } else {
      return Reflect.deleteProperty(trapTarget, key);
    }
  },
});
```

当我们想删除 `age` 属性时，直接返回 `false` 而不做删除操作，表示删除失败，当想删除其他属性时，则将操作转发给 `target`

```javascript
console.log(proxy.age); // 18
console.log(delete proxy.age); // false
console.log(proxy.age); // 18
```

## 对象原型

我们可以通过 `Object.setPrototypeOf` 和 `Object.getPrototypeOf` 为对象设置原型以及访问对象的原型，我们可以通过 `setPrototypeOf` 与 `getPrototypeOf` 来拦截这个行为。`setPrototypeOf` 接收两个参数：

- `trapTarget`
- `proto`：原型

`setPrototypeOf` 返回 `false` 表示设置原型失败，返回任一非 `false` 值 `Object.setPrototype` 会认为原型设置成功

```javascript
let target = {};
let proxy = new Proxy(target, {
  setPrototypeOf(trapTarget, proto) {
    if (!("name" in proto)) {
      return false;
    }
    return Reflect.setPrototypeOf(target, proto);
  },
});
```

上面当我们设置原型时，如果原型对象没有 `name` 属性，我们返回 `false`，表示原型设置失败

```javascript
let proto = {
  age: 18,
};

try {
  Object.setPrototypeOf(proxy, proto);
} catch (e) {
  console.log("原型设置失败"); // √
}

console.log(Object.getPrototypeOf(proxy) === proto); // false
```

`getPrototype` 接收一个参数：

- `tarpTarget`

`getPrototype` 必须返回一个对象或者 `null`，否则会报错

```javascript
let target = {
  name: "Alice",
};
let proxy = new Proxy(target, {
  getPrototypeOf(trapTarget) {
    if ("name" in trapTarget) {
      return null;
    }
    return Reflect.getPrototypeOf(trapTarget);
  },
});
```

如果对象具有 `name` 属性，我们就直接返回 `null`，否则走默认行为

```javascript
console.log(Object.getPrototypeOf(proxy)); // null
```

## 对象扩展

`Object.isExtensible` 是判断对象是否可扩展的，而 `Object.preventExtensions` 是用来设置对象不可扩展的，我们可以通过 `isExtensible` 和 `preventExtensions` 来拦截默认行为，它们都接收一个参数

- trapTar`get

且返回一个布尔值，`isExtensible` 返回的布尔值表示十分可扩展，而 `preventExtensions` 返回的布尔值表示是否设置不可扩展，返回 `false` 表示不设置不可扩展。

`isExtensiable` 返回的值一定要与 `Object.isExtensible` 和 `Reflect.isExtensible` 返回的值相同，当 `preventExtensions` 返回 `false` 时，`Object.preventExtensions` 会认为设置失败，从而抛出一个错误。

```javascript
let target = {};

let proxy = new Proxy(target, {
  isExtensible(target) {
    return Reflect.isExtensible(target);
  },
  preventExtensions(target) {
    return false;
  },
});

console.log(Object.isExtensible(target)); // true
console.log(Object.isExtensible(proxy)); // true

try {
  console.log(Object.preventExtensions(proxy));
} catch (e) {
  console.log(e); // √
}

console.log(Object.isExtensible(target)); // true
console.log(Object.isExtensible(proxy)); // true
```

## 对象描述符

当我们调用 `Object.defineProperty` 时，我们可以通过 `defineProperty` 来拦截这一行为，`defineProperty` 接收三个参数

- `trapTarget`
- `key`
- `descriptor`

```javascript
let target = {
  name: "Alice",
};

let proxy = new Proxy(target, {
  defineProperty(trapTarget, key, descriptor) {
    if (typeof key === "symbol") {
      return false;
    }
    return Reflect.defineProperty(trapTarget, key, descriptor);
  },
});
```

上述拦截了 `defineProperty`，当配置的键为 `Symbol` 类型时，返回 `false`，表示不可配置，而对于字符串类型的键则调用 `Reflect` 转发给 `target` 对象

```javascript
Object.defineProperty(proxy, "age", {
  value: 18,
  writable: true,
  configurable: true,
  enumerable: true,
});

console.log(proxy.age); // 18

let sym = Symbol();
try {
  // 会抛出错误，因为不可配置 Symbol 类型的键
  Object.defineProperty(proxy, sym, {
    value: "Hello",
  });
} catch (e) {
  console.log("不可配置 Symbol 键"); // √
}
```

`Object.getOwnPropertyDescriptor` 方法使用获得属性的描述符的，同样可以使用 `getOwnPropertyDescriptor` 进行拦截，它接收两个参数

- `trapTarget`
- `key`

返回值可以是 **undefined** 或者一个对象，如果返回的是一个对象，那么对象中的属性只能包括

- `enumerable`
- `configurable`
- `value`
- `writable`
- `get`
- `set`

如果返回的对象除了其他的属性，那么 `Object.getOwnPropertyDescriptor` 会抛出一个错误

```javascript
let proxy = new Proxy(target, {
  getOwnPropertyDescriptor(trapTarget, key) {
    if (typeof key === "symbol") {
      return null;
    }
    return Reflect.getOwnPropertyDescriptor(trapTarget, key);
  },
});
```

上面的程序表示，无法获得到键为 `Symbol` 类型的描述符。

## ownKeys

当我们调用 `Object.keys`，`Object.getOwnPropertyNames`，`Object.getOwnPropertySymbols`，`Object.assign` 时，首先会在内部调用 `[[OwnPropertyKeys]]` 来得到一个数组用于上述方法筛选，`Object.keys`，`Object.getOwnPropertyNames` 返回的结果将 `Symbol` 类型的值排除在外，`Object.getOwnPropertySymbols` 将字符串类型的值排除在外，`Object.assign` 两种类型都支持。

我们通过 `ownKeys` 这个方法来拦截 `[[OwnPropertyKeys]]` 的行为，它只接受一个参数

- `trapTarget`

返回值必须为数组或者类数组，否则抛出错误。

```javascript
let target = {
  _name: "Alice",
  age: 18,
};

let proxy = new Proxy(target, {
  ownKeys(trapTarget) {
    return Reflect.ownKeys(trapTarget).filter((item) => {
      return typeof item !== "string" || item[0] !== "_";
    });
  },
});

console.log(Object.keys(proxy)); // [ 'age' ]
```

上面的程序过滤掉了以下划线开头的属性，因为这样的属性表示私有属性，不想被访问到。

## apply 和 construct

`Proxy` 还可以用来代理函数，可以拦截函数的调用。根据调用的方式不同，有两个拦截的方法

- `apply`：正常调用函数
- `construct`：通过 `new` 调用函数

`apply` 方法接收三个参数

- `trapTarget`：执行的函数
- `thisArgument`：函数内部的 this
- `argumentsList`：传入函数的参数，以数组的形式显示

`construct` 方法接收两个参数

- `trapTarget`
- `argumentsList`

```javascript
let target =  function(...values) {
  return values.reduce((previousValue, currentValue) => previousValue + currentValue))
};

let proxy = new Proxy(target, {
  apply(target, thisArg, argArray) {
    argArray.forEach(item => {
      if (typeof item !== 'number') {
          throw new Error("参数必须全部为数字");
      }
    })
    return Reflect.apply(target, thisArg, argArray);
  }
});

const result = proxy(1, 2, 3, 4);
console.log(result);
```

上面的程序利用 `apply` 截获了函数调用来做参数校验，如果传入的参数不全是数字的话，那么就会抛出错误。

有的时候函数只能通过 `new` 的方式调用，这个时候我们会判断 `new.target` 变量来纠正行为，但是我们还可以通过截获普通函数调用，然后返回一个 `new` 调用的结果

```javascript
function Person(name) {
  this.name = name;
}

let PersonProxy = new Proxy(Person, {
  apply(target, thisArg, argArray) {
    return Reflect.construct(target, argArray);
  },
});

let alice = new PersonProxy("Alice");
console.log(alice instanceof Person); // true

let bob = PersonProxy("Bob");
console.log(bob instanceof Person); // true
```

这个时候无论是普通调用还是 `new` 调用，返回的都是 `Person` 的实例对象。

## 撤销代理

上面创建的代理对象都是不可撤销，就是说它一直代理着目标对象，如果我们希望代理在某个时刻失效，那我们就需要撤销代理，我们可以通过 `Promise.revecable` 方法创建能够撤销的代理对象，它接收的参数同 `Proxy` 构造函数相同，返回一个对象，对象中具有两个属性：

- `proxy`：可被撤销的代理对象
- `revoke`：调用该方法可撤销代理，调用该方法后代理对象不可用

```javascript
let target = {
  name: "Alice",
};

let { proxy, revoke } = Proxy.revocable(target, {
  get(target, key, receiver) {
    return "Bob";
  },
});

console.log(proxy.name); // Bob

// 撤销代理，此后不能使用 proxy
revoke();

// 报错，TypeError: Cannot read property 'Symbol(nodejs.util.inspect.custom)' of null
console.log(proxy);
```
