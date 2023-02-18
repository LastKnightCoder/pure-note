---
tags: [JavaScript, ES6/Set, ES6/Map]
---

在 ES6 以前，JavaScript 只有数组这一种集合来存储数据(当然有的人会使用对象来存储数据)，在 ES6 中引入了两种新的集合来存储数据，它们是 `Set` 和 `Map`：

- `Set`：`Set` 集合中的数据都不相同
- `Map`：数据格式为键值对，与对象类似

## ES5 中的 Set 和 Map

### 模拟 Set 和 Map

在 ES5 中，开发者使用对象来模拟 `Set` 和 `Map`

```javascript
let set = Object.create(null);
set.foo = true;
if (set.foo) {
  // do something
}
```

上面在 `Set` 集合中存储了一个名为 `foo` 的数据，通过将它的值设置为 `true` 来指明集合中有这个数据。

```javascript
let map = Object.create(null);
map.foo = "bar";

console.log(map.foo); // bar
```

上述代码创建了一个 `map` 集合，并且在其中存储 `foo-bar` 这个键值对。

### 存在的问题

问题是由于对象的特点引起的，在 JavaScript 中，对象的键值都是字符串

```javascript
let map = Object.create(null);
map[5] = "hello";

console.log(map["5"]); // hello
```

虽然上面是以数字 `5` 为键值存储的数据，但是在内部会被转化为字符串 `"5"`，所以数字 `5` 和字符串 `"5"` 为键值是一样的。因为对象会将键强转为字符串，所以无法以对象作为键值

```javascript
let map = Object.create(null);
let key1 = {},
    key2 = {};

map[key1] = "hello";
console.log(map[key2]); // hello
```

上述当我们以 `key1` 和 `key2` 为键值存储和访问数据时，都会先将对象转化为字符串，然后以该字符串为键值作为对象的键。`key1` 和 `key2` 转化为字符串得到的都是 `[object Object]`，所以以它们作为集合的键是同一个键，无法区分，即以对象模拟 `Set` 和 `Map` 无法以对象作为键。

另外对于 `map` 集合，判断一个键是否存在也存在问题，通常的判断方法如下

```javascript
let map = Object.create(null);
map.count = 1;

if (map.count) {
  // do something
}
```

上面通过 `map.count` 来判断 `count` 键是否存在，但是当 `count` 的值为 `0` 时得到的结果也是 `false`，所以这是一个判断非 `0` 的逻辑，还是判断键不存在的逻辑，无从得知。

## Set

ES6 提供了原生 `Set` 集合，`Set` 集合是一个有序的、没有重复元素的集合。

### 使用 Set

通过 `new Set()` 得到一个 `Set` 对象，`Set` 集合提供如下 API

- `add(item)`：向集合中添加元素 `item`
- `has(item)`：判断集合中是否有 `item` 元素
- `delete(item)`：删除集合中的 `item` 元素
- `clear()`：清空集合中的所有元素

```javascript
let set = new Set();

set.add(5);
set.add("5");

console.log(set.size); // 2
```

**`Set` 集合是通过 `Object.is` 方法来判断两个元素是否相同的**，所以 `5` 和 `"5"` 是两个不同的元素。如果向 `Set` 集合中添加相同的元素，那么 `Set` 集合会忽略重复添加的语句

```javascript
set.add(6);
set.add(6); // 这条语句会被忽略

console.log(set.size); // 3
```

此外，我们还可以像 `Set` 集合中添加对象

```javascript
let key1 = {},
    key2 = {};
set.add(key1);
set.add(key2);

console.log(set.size); // 5
```

上面 `key1` 和 `key2` 是不同的对象，所以二者都可以被添加进集合中。

```javascript
console.log(set.has(key1));    // true
console.log(set.delete(key1)); // true
console.log(set.has(key1));    // false
```

`has` 方法是判断集合中是否有该元素，而 `delete` 方法是删除集合中的元素，如果删除成功返回 `true`，否则返回 `false`。

```javascript
set.clear();
console.log(set.size); // 0
```

`clear` 方法的作用是清空 `Set` 集合。

### 遍历 Set

`Set` 集合提供 `forEach` 的 API 让我们来遍历 `Set` 集合，`forEach` 接收一个回调函数，该回调函数接收三个参数

- 集合中的元素
- 同第一个参数
- `set` 集合本身

这里比较奇怪的是第二个参数与第一个参数相同，之所以这么做是为了与数组以及 `map` 集合的 `forEach` API 保持一致，方便记忆

```javascript
let set = new Set();

set.add(1);
set.add(3);
set.add(4);

set.forEach((value, _, ownerSet) => {
  console.log(value);  // 1
                       // 3
                       // 4
  console.assert(ownerSet === set);
});
```

如果需要在回调函数中使用 `this` 的话，可以通过 `forEach` 的第二个参数将 `this` 传入

```javascript
let set = new Set([1, 2]);

let processor = {
  output(value) {
    console.log(value);
  },
  process(dataSet) {
    dataSet.forEach((value) => {
      this.output(value);
    }, this);
  },
};

processor.process(set);
```

### Set 与数组

`Set` 的构造方法可以接收一个数组作为参数，它会将数组中的元素添加进 `Set` 集合中，但是不会添加重复的元素

```javascript
let set = new Set([1, 3, 2, 3, 4]);
set.forEach((value, _, ownerSet) => {
  // 1 3 2 4
  console.log(value);
});
```

可以通过 `...` 运算符将 `Set` 转化为数组

```javascript
let set = new Set([1, 3, 2, 3, 4]);
let arr = [...set];

console.log(Array.isArray(arr)); // true
console.log(arr); // [ 1, 3, 2, 4 ]
```

利用 `Set` 集合的特点，可以方便的数组进行去重

```javascript
function elimateDuplicates(items) {
  let set = new Set(items);
  return [...set];
}

let arr = [1, 2, 1, 1, 3, 2];
arr = elimateDuplicates(arr);
console.log(arr); // [ 1, 2, 3 ]
```

## WeakSet

上面的介绍的 `Set` 可以看做是 `Strong Set`，所谓的 Strong 指的是集合中的元素保持对对象的引用，例如

```javascript
let set = new Set();
let key = {
  name: "Alice",
};

set.add(key);

key = null;
```

上面我们为 `Set` 集合中添加了一个对象 `key`，随后将对象 `key` 设置为了 `null`，但是集合仍然保持着对对象的引用，这就意味着无法回收此对象(这个对象已经无法访问，按道理应该被回收掉)，可能会造成内存泄漏的问题。

所以相应的提出了 `WeakSet`，所谓的 `WeakSet` 指的是其中的元素没有保持的对对象的引用，如果对象被设置为 `null`，那么该对象就可以被回收

```javascript
let weakSet = new WeakSet();
let key = {
  name: "Alice",
};

weakSet.add(key);

// 设置为 null 了，对象可以被作为垃圾回收了
key = null;
```

`WeakSet` 提供与 `Set` 相似的 API

- `add`
- `has`
- `delete`

下面列举 `Set` 与 `WeakSet` 的不同：

1. `WeakSet` 集合只能存储对象
2. `WeakSet` 不可遍历，没有 `forEach` 方法，没有 `size` 属性
3. 没有 `clear` 方法

## Map

### 使用 Map

`Map` 是用来存储键值对结构的数据的，我们可以通过 `new Map()` 创建一个 `Map` 集合

```js
let map = new Map();

map.set(5, "alice");
map.set(10, "bob");

console.log(map); // Map { 5 => 'alice', 10 => 'bob' }
```

Map 提供如下 API

- `set(key, value)`：根据 `key` 设置 `value`，如集合中没有该 `key`，那么添加这一键值对
- `get(key)`：根据 `key` 查找 `value` 并返回，如果集合中没有该 `key`，返回 `undefined`
- `has(key)`：判断集合中有没有 `key`，有返回 `true`，否则返回 `false`
- `delete(key)`：根据 `key` 删除集合中的键值对，删除成功返回 `true`，失败返回 `false`
- `clear()`：清空集合中的所有元素

```javascript
let map = new Map();

map.set("name", "Alice");
map.set("age", 20);

console.log(map.has("name")); // true
console.log(map.get("name")); // Alice

map.delete("name");
console.log(map.size); // 1
console.log(map.get("name")); // undefined

map.clear();
console.log(map.size); // 0
```

`Map` 集合还可以通过数组进行初始化，它的构造函数接收一个二维数组，形式为 `[[key1, value1], [key2, value2]]` 的形式

```js
let map = new Map([
  [5, "Alice"],
  [10, "Bob"],
]);
console.log(map); // Map { 5 => 'Alice', 10 => 'Bob' }
```

### 遍历 Map

`Map` 集合也提供了一个 `forEach` 方法用来遍历 `Map` 集合，该方法也接受一个回调函数，回调函数接受三个参数

- `value`
- `key`
- `map` 集合本身

```js
let map = new Map([
  ["name", "Alice"],
  ["age", 18],
  ["gender", "female"],
]);

map.forEach((value, key, ownerMap) => {
  console.log(`key: ${key}, value: ${value}`); // key: name, value: Alice
                                               // key: age, value: 18
                                               // key: gender, value: female
  console.assert(map === ownerMap);
});
```

如果回调函数中需要用到 `this` 的话，可以通过 `forEach` 的第二个参数将 `this` 传入。

## WeakMap

`WeakMap` 的提出与 `WeakSet` 相似，都是为了解决垃圾回收问题

```javascript
let map = new WeakMap();

let key1 = {},
  key2 = {};

map.set(key1, "Alice");
map.set(key2, "Bob");

// key1 的值为 null，表示可以被垃圾回收了
key1 = null;
```

`WeakMap` 提供以下 API

- `set`
- `get`
- `has`
- `delete`

```javascript
let map = new WeakMap();

let key1 = {},
    key2 = {};

map.set(key1, "Alice");
map.set(key2, "Bob");

console.log(map.get(key1));    // Alice
console.log(map.delete(key1)); // true
console.log(map.has(key1));    // false
```

`WeakMap` 具有如下限制：

- 键必须为对象，否则抛出错误
- 没有 `size` 属性，无 `clear` 方法
- 无法使用 `forEach` 循环，不可迭代
