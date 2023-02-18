---
tags: [JavaScript, ES6/Symbol]
---

# Symbol 和 Symbol 属性

在 ES6 中引入一种新的原始类型，那就是 `Symbol`。`Symbol` 类型的值唯一的，**每个被创建出来的 `Symbol` 变量都是不同的**，这是 `Symbol` 这种数据类型最大的特点。

## 创建

`Symbol` 不能通过字面量来进行创建，创建 `Symbol` 变量需要通过 `Symbol()` 来创建

```javascript
let x = Symbol();

console.log(typeof x); // symbol
```

```javascript
let x = Symbol();
let y = Symbol();
console.log(x === y); // false
```

`Symbol` 类型的变量可以作为对象的属性，因为每一个 `Symbol` 变量是唯一的，所以不会出现属性被别人覆盖的问题，以及可以设置为私有属性，别人无法访问

```javascript
let x = Symbol();

let person = {
  [x]: "Alice",
};

console.log(person[x]); // Alice
```

`Symbol` 还可以接收一个字符串作为该 `Symbol` 变量的描述，该字符串纯粹是为了调试方便而设置的，没有特殊的功能，两个 `Symbol` 类型的变量的描述符相同不代表它们是相同，**每一个由 `Symbol()` 创建的变量都是不同的**。

```javascript
let name = Symbol("Alice");
let alice = Symbol("Alice");

console.log(name == alice); // false
```

## 使用

`Symbol` 类型的变量可以作为对象的属性，不仅可以在对象的字面量中通过计算属性进行设置属性，还可以通过 `Object.defineProperty` 和 `Object.defineProperties` 对对象进行定义

```javascript
let first = Symbol("first");

let alice = {
  [first]: "Alice",
};

Object.defineProperty(alice, first, {
  writable: false,
});

let last = Symbol("last");
Object.defineProperties(alice, {
  [last]: {
    value: "Lee",
    writable: false,
  },
});

console.log(alice[first]); // Alice
console.log(alice[last]);  // Lee
```

## 共享

有的时候代码中需要共享一个 `Symbol`，ES6 提供了一个全局的 `Symbol` 管理机构，我们可以在任何时候访问它。我们是通过 `Symbol.for` 方法来访问这个全局机构的，`Symbol.for` 接收一个字符串参数，根据这个字符串参数在全局机构中查找对应的 `Symbol`，如果找到了就返回，如果没有找到，则在全局机构中新建一个 `Symbol` 并返回。

```javascript
// 此时根据字符串 "id" 查不到 Symbol
// 会新建一个 Symbol 并返回
let id = Symbol.for("id");
console.log(typeof id);    // symbol

// 此时可根据 "id" 查找到 Symbol，返回
// 返回的 Symbol 与上次根据 "id" 创建的 Symbol 是同一个 Symbol
let id2 = Symbol.for("id");
console.log(id2 == id);    // true
```

与 `Symbol.for` 方法向对应的是 `Object.keyfor`，它是根据接收的 `Symbol` 来获得在全局机构中对应的字符串，如果全局机构中没有该 `Symbol`，则返回 `undefined`

```javascript
let uid = Symbol.for("uid");
console.log(Symbol.keyFor(uid));  // "uid"

let uid2 = Symbol.for("uid");
console.log(Symbol.keyFor(uid2)); // "uid"

// 不是通过 Symbol.for 从全局获得的 Symbol
let uid3 = Symbol("uid");
console.log(Symbol.keyFor(uid3)); // undefined
```

`Symbol` 类型不能转为其他原始类型，其他原始类型也不能转为 `Symbol` 类型，强行转换会抛出错误。

## 获取 Symbol 属性

`Symbol` 变量可以作为对象的属性，但是当我们通过 `for...in` 以及 `getOwnPropertyNames` 都不能获得 `Symbol` 属性

```javascript
let age = Symbol("age");
let person = {
  name: "Alice",
  [age]: 28,
};

for (let key in person) {
  console.log(key); // name
}

console.log(Object.getOwnPropertyNames(person)); // [ 'name' ]
```

要获得 `Symbol` 类的属性，要通过 `Object.getOwnPropertySymbols` 方法

```javascript
console.log(Object.getOwnPropertySymbols(person)); // [ Symbol(age) ]
```

## 通过 Symbol 暴露内部操作

在以往一些内部操作我们是更改不了的，例如对于 `instanceof` 运算符，我们无法改变它的行为(虽然大多数的时候不需要更改，但是有的时候有这个需求)，现在 ES6 将会暴露这些内部操作给我们，使得我们有机会改变一些默认的内部操作。

我们改变内部操作是通过改变对象的一些 Symbol 属性来决定的，例如改变对象 `Symbol.hasInstance` 属性可以改变 `instanceof` 行为，一些常见的`Symbol` 如下：

- `Symbol.hasInstance`
- `Symbol.isConcatSpreadable`
- `Symbol.iterator`
- `Symbol.match`
- `Symbol.replace`
- `Symbol.search`
- `Symbol.species`
- `Symbol.split`
- `Symbol.toPrimitive`
- `Symbol.toStringTag`
- `Symbol.unscopables`

### Symbol.hasInsatnce

`instanceof` 是用来判断某个对象是否是某个函数的实例，如果该函数在这个对象的原型链上，那么这个对象就是这个函数的实例。在 ES6 中，我们可以 `Symbol.hasInstance` 来改变这一默认行为

```javascript
obj instanceof Array;
// 相当于
Array[Symbol.hasInstance](obj);
```

通过改变构造函数的 `Symbol.hasInstance` 属性，即可改变这一行为。

``` note
必须通过 `Object.defineProperty` 来重写 `Symbol.hasInstance`
```

```javascript
function Person(name) {
  this.name = name;
}

let alice = new Person("alice");
// 默认行为
console.log(alice instanceof Person); // true

// 更改 instanceof 行为
Object.defineProperty(Person, Symbol.hasInstance, {
  value: function (instance) {
    return false;
  },
});

console.log(alice instanceof Person); // false
```

`Symbol.hasInstance` 方法接收一个参数，这个参数就是 `instanceof `前面的那个对象

### Symbol.isConcatSpreadable

要明白这个属性的作用，得要了解数组的 `concat` 方法，`concat` 方法的作用是将两个数组拼接起来，然后返回一个新的数组

```javascript
let colors1 = ["red", "green"];
let colors2 = colors1.concat(["white", "blue"]);

console.log(colors2); // [ 'red', 'green', 'white', 'blue' ]
```

`concat` 除了可以接受数组作为参数，还可以接受非数组参数

```javascript
let colors1 = ["red", "green"];
let colors2 = colors1.concat(["white", "blue"], "pink");

console.log(colors2); // [ 'red', 'green', 'white', 'blue', 'pink' ]
```

`"pink"` 作为非数组参数传入，成为了 `colors2` 数组的第五个元素。为什么数组参数被区别对待(数组参数为什么不是整体拼接到数组中)，因为 JavaScript 规定了数组会被自动的被分割为独立的元素，而其他元素不会。

当我们向 `concat` 传递一个对象，根据的对象的 `Symbol.isConcatSpreadable` 来决定是作为整体拼接，还是将对象分割然后添加到数组中。能被自动分割的对象满足两个条件：

1. 有 `length` 属性(根据 `length` 属性决定分割为多少个元素)
2. `Symbol.isConcatSpreadable` 的值为 `true`

那么就会将对象中的数字属性分割为一项项添加进数组

```javascript
let colors1 = ["red", "green"];
let obj = {
  0: "blue",
  1: "white",
  length: 2,
  name: "obj",
  [Symbol.isConcatSpreadable]: true,
};
let colors2 = colors1.concat(obj);

console.log(colors2); // [ 'red', 'green', 'blue', 'white' ]
```

### Symbol.match, Symbol.replace, Symbol.search, Symbol.split

`string` 类型有几个接受正则表达式的方法：

- `match(regex)`
- `replace(regex, replacement)`
- `search(regex)`
- `split(regex)`

现在 ES6 允许使用对象来代替上面的正则表达式(regex)，从而可以自定义匹配行为，以此达到自己不可告人的目的。以 `match` 为例，当字符串调用 `match` 方法传入一个对象时，会调用对象的 `Symbol.match` 方法，并将自己作为参数传入，`Symbol.match` 的返回结果作为字符串 `match` 方法的返回结果

```javascript
let obj = {
  [Symbol.match]: function (str) {
    return str.length === 10 ? ["Hello Alice!"] : null;
  },
};
let str = "HelloWorld".match(obj);

console.log(str); // [ 'Hello Alice!' ]
```

其他的三个方法 `replace`, `search`, `split` 分别对应于 `Symbol.replace`, `Symbol.search`, `Symbol.split`，其使用方法与 `match` 类似。

### Symbol.toPrimitive

在实际中我们经常需要将对象转化为原始类型，例如对一个对象做加法，那么就会触发将对象转化为原始类型的步骤。根据对象需要转化为数字还是字符串，有两种不同的行为。如果需要转化为数字，遵循以下步骤：

1. 调用对象的 `valueOf` 方法，如果返回的是原始类型，则返回
2. 否则，调用对象的 `toString` 方法，如果返回的是原始类型，则返回
3. 否则，抛出错误

如果需要转化为字符串的话，遵循以下步骤

1. 调用对象的 `toString` 方法，如果返回的是原始类型，则返回
2. 否则，调用对象的 `valueOf` 方法，如果返回的是原始类型，则返回
3. 否则，抛出错误

大多数情况下都是默认转化为数字，少数对象如 `Date` 对象默认转化为字符串。

我们发现转化为数字和字符串时 `valueOf` 和 `toString` 方法的调用顺序不同，为了统一将对象转化为对象的行为，可以通过 `Symbol.toPrimitive` 覆盖默认的转化顺序，以统一转化行为。

`Symbol.toPrimitive` 方法接收一个 `hint` 作为参数，`hint` 是一个字符串，它只有三种取值

- `string`
- `number`
- `default`

根据对象需要被转化为什么类型的原始字符串，`hint` 的取值不同，例如当对象处在需要转化为数字的上下文中，`hint` 的值就是 `number`，需要转化为字符串时，`hint` 就是 `string`，当不确定需要转化为什么时(例如加法运算，既可以用于字符串连接，也可以用于数字相加)，`hint` 的值就是 `default`

```javascript
let obj = {
  [Symbol.toPrimitive]: function (hint) {
    switch (hint) {
      case "number":
        return 10;
      case "string":
        return "hello";
      case "default":
        return "default";
      default:
        throw new Error();
    }
  },
};

const print = console.log;
print(1 * obj); // 10
print(`${obj}`); // hello
print(obj + ""); // default
```

### Symbol.toStringTag

当我们调用自己创建的对象的 `toString` 方法时，得到的是 `[object Object]`

```javascript
let Person = {
  name: "Alice",
};

console.log(Person.toString()); // [object Object]
```

如果我们希望打印出 `[object Person]`，那么可以借助于 `Symbol.toStringTag`

```javascript
let Person = {
  name: "Alice",
};

Person[Symbol.toStringTag] = "Person";

console.log(Person.toString()); // [object Person]
```
