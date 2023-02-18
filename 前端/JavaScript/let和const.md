---
tags: [JavaScript, ES6]
---

## var 声明变量

使用 `var` 声明变量的特点：

- 变量提升

  ```js
  console.log(value); // undefiend
  var value = "hello";
  ```

  上述代码在变量 `value` 声明之前就访问了 `value`，在其他的类 C 语言中，要使用变量必须先声明变量，如果没有声明则会报错，但是对于上述的代码，JavaScript 并没有报错，而是打印出了 `undefiend`，这时因为 JavaScript 对使用 `var` 声明的变量做了变量提升，将变量 `value` 的声明放置在所在作用域的最前面，所以上面的代码相当于如下

  ```javascript
  var value;
  console.log(value);
  value = "hello";
  ```

- 无块级作用域

  对于其他的类 C 语言，变量的作用域一般是在一个块级作用域中的，一旦出了所在的块级作用域，就无法访问到该变量。但是 JavaScript 并没有块级作用域，只有全局作用域和函数作用域

  ```javascript
  if (true) {
    var value = "hello";
  }

  console.log(value); // hello
  ```

  变量 `value` 声明在 `if` 的块级作用域中，但是对于 JavaScript 来说没有块级作用域，所以 `value` 是个全局变量，即使在 `if` 的块级作用域之外还是可以访问。

## let 和 const

### 块级作用域

使用 `let` 和 `const` 可以声明块级作用域

```javascript
if (true) {
  let value = "hello";
}

console.log(value);
```

因为使用 `let` 声明的变量拥有块级作用域，所以如果在作用域外访问变量，那么就会报错，如上面的代码会报下面的错误

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting/20210112224158.png" style="zoom:50%;" />

`const` 的用法和 `let` 是相同的，不同的是使用 `const` 声明的变量，它的值不能被更改，所以 `const` 声明的变量在声明时就要做初始化，如果没有做初始化，则会报错

```javascript
const value
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting/20210112224016.png" style="zoom:50%;" />

同样如果对 `const` 定义的变量进行修改的话，也会报错

```javascript
const value = "hello";
value = "world";
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting/20210112224323.png" style="zoom:50%;" />

但是如果 const 修饰的变量是一个对象的话，我们可以修改变量的属性值，例如

```javascript
const person = {
  name: "Alice",
  age: 18,
};

person.name = "Bob";

console.log(person.name); // Bob
```

使用 const 修饰 person，意味着 person 的值不能发生改变，而改变 person 的属性不会改变 person 的值，所以上面的修改是可以的，但是如果你试图对 person 进行赋值，那么就会报错

```javascript
// TypeError: Assignment to constant variable.
person = {
  name: "Bob",
  age: 20,
};
```

### 暂时性死区

对于使用 let 和 const 定义的变量，它们的声明不会被编译器提升到作用域的开头，该变量所在的作用域的开始到该变量声明的地方之间的区域叫做暂时性死区

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting/20210112224355.png" style="zoom:50%;" />

在暂时性死区访问变量会发生错误，即使是 `typeof` 这么安全的操作符都不行

```javascript
// undefined
console.log(typeof value);

if (true) {
  // ReferenceError: Cannot access 'value' before initialization
  console.log(typeof value);
  let value = "hello";
}
```

暂时性死区并**未出现在 ECMAScript 规范**中，但是人们经常使用这个术语来解释为什么在 `let` 和 `const` 定义变量前不能访问该变量。

### 重复声明变量

在**同一个作用域**中，不能声明相同变量名的变量，例如

```javascript
var value = "hello";

// SyntaxError: Identifier 'value' has already been declared
let value = "world";
```

## 循环中的块级作用域

下面我们要看一个遍历的例子

```javascript
const funcs = [];
for (var i = 0; i < 10; i++) {
  funcs.push(function () {
    console.log(i);
  });
}
funcs.forEach(function (func) {
  func();
});
```

上面的程序也许你会以为会输出 `0-9` ，但是最后的输出是 10 个 10

```
10
10
10
10
10
10
10
10
10
10
```

有点反常，但是想想还真是，`funcs` 数组中的函数里面的 `i` 指向的都是循环变量 `i`，当循环执行完毕时，循环变量 `i` 已经变为了 10，当打印 `i` 时，结果就当然全部都是 10 了。如果我们想打印 0-9 的话，就需要将变量 `i` 的 "快照" 传入，如下

```javascript
const funcs = [];
for (var i = 0; i < 10; i++) {
  funcs.push(
    (function (value) {
      return function () {
        console.log(value);
      };
    })(i)
  );
}
funcs.forEach(function (func) {
  func();
});
```

上面的代码开始有点复杂了，如果是新手一时半会儿还看不懂。上面代码就是将变量 `i` 的值传入了立即执行函数，这时 `funcs` 数组中的函数保存的 `value` 值是循环变量 `i` 的 "快照"，而不是循环变量 `i`，所以上面的最终结果是打印出 0-9

```
0
1
2
3
4
5
6
7
8
9
```

现在我们用 let 来实现打印出 0-9

```javascript
const funcs = [];
for (let i = 0; i < 10; i++) {
  funcs.push(function () {
    console.log(i);
  });
}
funcs.forEach(function (func) {
  func();
});
```

上面打印出来的结果是 0-9，和使用 `var` 定义循环变量的情况不同。原因是因为每次遍历时，**`let` 都会创建一个新的变量 `i`**，所以每一个 `func` 中的 `i` 它们都是不同的，每一个 `func` 函数都有一个 i，并且是当时遍历时的值，所以最后处理的结果是 0-9。

```tip
最佳实践：变量使用 `const` 定义，当变量需要改变时，在使用 `let` 定义。
```
