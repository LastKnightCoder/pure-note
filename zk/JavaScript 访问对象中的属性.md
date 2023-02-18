---
tags: [Javascript/Object]
date: 2021-04-30
---

本篇文章关于遍历对象属性的问题，包括 `for ...in`，`Object.keys()`，`Reflect.ownKeys()`， `Object.getOwnPropertyNames()`，`ObjectgetOwnPropertySymbols()` 等方法及区别。

## Object.keys()

`Object.keys(obj)` 方法返回对象 `obj` 上所有**可枚举**的属性，不包括其原型上的属性

```javascript
let parent = {
    category: 'parent'
}

// 设置 person 为 child 的原型
let child = Object.create(parent)

child.name = 'Alice'
child[Symbol('age')] = 18
Object.defineProperty(child, 'gender', {
    enumerable: false,
    value: 'female'
})

console.log(Object.keys(child)) // [ 'name' ]
```

需要注意的是，`Object.keys()` 无法得到键为 `symbol` 类型的属性。

## Reflect.ownKeys()

`Reflect` 是在 `ES6` 中提出的，我们可以通过 `Reflect.ownKeys(obj)` 取得对象 `obj` 上所有的属性，包括不可枚举的属性以及 `symbol` 类型的属性，但是它不能获得其原型上的属性。

```javascript
console.log(Reflect.ownKeys(child)) // [ 'name', 'gender', Symbol(age) ]
```

## for ... in

`for ... in` 同样可以得到对象 `obj` 上所有**可枚举**的属性(不包括 `symbol` 类型的属性)，除此之外，它还可以获得其原型上的属性

```javascript
const keys = []
for (key in child) {
    keys.push(key)
}
console.log(keys) // [ 'name', 'category' ]
```

如果希望只获得当前对象上的属性，可以通过 `obj.hasOwnProperty(key)` 方法进行过滤，如果 `key` 是 `obj` 上的属性，而不是其原型上的属性，则会返回 `true`，否则返回 `false`

```javascript
const keys = []
for (key in child) {
    if (child.hasOwnProperty(key)) {
        keys.push(key)
    }
}
console.log(keys) // [ 'name' ]
```

也可对 `Object.keys()` 或 `Reflect.ownKeys()` 进行遍历

```javascript
let keys = []
for (key of Object.keys(child)) {
    keys.push(key)
}
console.log(keys) // [ 'name' ]

keys = []
for (key of Reflect.ownKeys(child)) {
    keys.push(key)
}
console.log(keys) // [ 'name', 'gender', Symbol(age) ]
```

## Object.getOwnPropertyNames()

通过 `Object.getOwnPropertyNames(obj)` 可以获得对象 `obj` 上所有类型为 `string` 的属性，包括不可枚举的，但不包括其原型上的属性值

```javascript
console.log(Object.getOwnPropertyNames(child)); // [ 'name', 'gender' ]
```

## Object.getOwnPropertySymbols()

通过 `Object.getOwnPropertySymbols(obj)` 可以获得对象 `obj` 上所有类型为 `symbol` 的属性，包括不可枚举的，但不包含其原型上的属性

```javascript
console.log(Object.getOwnPropertySymbols(child)) // [ Symbol(age) ]
```

