---
title: JavaScript 对象中属性为定义与显示定义为 undefined 的区别
created: 2023/2/16 23:09:09
updated: 2023/2/16 23:13:29
tags: [undefined]
---

当我们访问一个对象上不存在的属性时，会返回 `undefined`

```js
const obj = {};
console.log(obj.a); // undefined
```

那么与我们显示定义值为 `undefined` 有什么区别

```js
const obj = {
  a: undefined
};
console.log(obj.a); // undefined
```

1. `Object.hasOwn()` 或 `Object.prototype.hasOwnProperty()` 的返回值不同

    ```js
    const obj = {};
    Object.hasOwn(obj, 'a'); // false
    obj.hasOwnProperty('a'); // false
    ```

    ```js
    const obj = {
      a: undefined
    };
    Object.hasOwn(obj, 'a'); // true
    obj.hasOwnProperty('a'); // true
    ```

2. `Object.keys()` 的返回值不同

    ```js
    const obj = {};
    Object.keys(obj); // []
    ```

    ```js
    const obj = {
      a: undefined
    };
    Object.keys(obj); // ['a']
    ```