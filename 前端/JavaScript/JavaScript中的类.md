---
tags: [JavaScript, ES6/class]
---

JavaScript 不同于 Java、C++ 等典型的面向对象语言，JavaScript 在过去并没有类的概念，所以一些从 Java 或 C++ 等其他面向语言转过来的开发者会感到困惑，所以在 ES6 中引入了类的概念。

## 基本使用

### 定义类

使用关键字 `class` 声明一个类

```javascript
class Person {
  constructor(name) {
    this.name = name;
  }

  sayHello() {
    console.log(`Hello ${this.name}`);
  }
}

let amy = new Person("Amy");
amy.sayHello(); // Hello Amy
```

需要注意的是 `class` 只是一种语法糖，即一个 `class` 其实本质上就是一个函数

```javascript
class Person {
  // ...
}

console.log(typeof Person); // function
```

类与构造函数有如下不同：

1. 类的声明不会提升到作用域的顶部
2. 在类内部的代码自动运行在严格模式 (strict mode) 之下
3. 类中的方法都是不可枚举的
4. 类中的方法都没有内置的 `[[Construct]]` 方法，所以当你 `new` 一个类内部的方法时，会抛出错误
5. 不使用 `new` 调用类会发生错误
6. 在类中的方法中不能修改类名

   ```javascript
   class Person {
     contructor() {
       // 不能在方法中修改类名
       Person = "foo";
     }
   }
   ```

所以上面类的声明与下面的代码等价

```javascript
// 1. 类的声明不会提升到作用域的顶部
let Person = (function (o, properties) {
  // 2. 在类内部的代码自动运行在严格模式 (strict mode) 之下
  "use strict";

  // 6. 在类中的方法中不能修改类名 (使用 const 定义)
  const Person = function (name) {
    // 5. 不使用 new 调用类会发生错误
    if (typeof new.target === "undefined") {
      throw new Error("Must be called by new");
    }
    this.name = name;
  };

  Object.defineProperty(Person.prototype, "sayHello", {
    value: function () {
      // 4. 类中的方法都没有内置的 [[Construct]] 方法，所以当你 new 一个类内部的方法时，会抛出错误
      if (typeof new.target !== "undefined") {
        throw new Error("Method in class can't be called by new!");
      }
      console.log(`Hello ${this.name}`);
    },
    // 3. 类中的方法都是不可枚举的
    enumerable: false,
    writable: false,
    configurable: false,
  });

  return Person;
})();
```

### 类表达式

类表达式的概念与函数表达式相对应

```javascript
let Person = class {
  constructor(name) {
    this.name = name;
  }
  sayHello() {
    console.log(`Hello ${this.name}`);
  }
};
```

或者

```javascript
let Person = class Person2 {
  // ...
};

console.log(typeof Person); // function
console.log(typeof Person2); // undefined
```

``` info
类与函数一样，都是一等公民，即可以作为参数传入函数，也可以作为返回值返回。
```

### 访问器属性

我们可以在类中定义访问器属性

```javascript
class Person {
  constructor(name) {
    this._name = name;
  }
  set name(value) {
    if (typeof value === "string") {
      this._name = value;
    }
  }
  get name() {
    return this._name;
  }
}

let amy = new Person("Amy");
console.log(amy.name); // Amy

amy.name = 10;
console.log(amy.name); // Amy

amy.name = "Big";
console.log(amy.name); // Big
```

### 计算属性名

与对象一样，我们可以为类中的方法名设置计算属性名

```javascript
let methodName = "sayHello";

class Person {
  constructor(name) {
    this.name = name;
  }
  // 计算属性名
  [methodName]() {
    console.log(`Hello ${this.name}`);
  }
}

let amy = new Person("Amy");
amy.sayHello(); // Hello Amy
```

### 生成器方法

类中的方法还可以是一个生成器

```javascript
class Person {
  *generateNames() {
    yield "Amy";
    yield "Bob";
    yield "Candy";
  }
}

let person = new Person();
let iterator = person.generateNames();

for (let name of iterator) {
  console.log(name); // Amy
                     // Bob
                     // Candy
}
```

还可以为类编写一个默认的迭代器，即 `[Symbol.iterator]`

```javascript
class Stack {
  constructor(...items) {
    this.items = [...items];
  }

  push(value) {
    this.items.push(value);
  }
  pop() {
    return this.items.pop();
  }
  *[Symbol.iterator]() {
    yield* this.items.values();
  }
}
```

上面我们编写一个栈的数据结构，并提供一个迭代器来遍历该栈。

### 静态方法

静态方法是类的方法，不是实例的方法，如

```javascript
function Person(name) {
  this.name = name;
}

Person.print = console.log;
```

上面我们在 `Person` 上定义了一个 `print` 方法，因为 `Person` 的实例继承的是 `Person.prototype`，所以 `Person` 的实例访问不了 `print` 方法，这种只可以通过类名访问的方法我们称为静态方法，在 `class` 定义的类中，通过在方法名前加 `static` 关键字表示该方法是静态方法

```javascript
class Person {
  constructor(name) {
    this.name = name;
  }
  static print(value) {
    console.log(value);
  }
}

const person = new Person("小明");
// TypeError: person.print is not a function
person.print(); // 错误，实例无法访问静态方法
```

## 继承

在 ES5 中实现类的继承，需要费一番额外的功夫

```javascript
function Person(name) {
  this.name = name;
}

Person.prototype.sayHello = function () {
  console.log("Hello World!");
};

function Student(name) {
  Person.call(this, name);
}

Student.prototype = Object.create(Person.prototype, {
  constructor: Student,
  writable: true,
  enumerable: true,
  configurable: true,
});

let stu = new Student("Alice");
stu.sayHello(); // Hello World
console.log(stu instanceof Student); // true
console.log(stu instanceof Person);  // true
```

在 ES6 中，我们只需要通过关键字 `extends` 即可来继承一个类，如

```javascript
class Person {
    constructor(name) {
        this.name = name;
    }

    sayHello() {
        console.log("Hello World");
    }
}

class Student extends Person{
    constructor(name) {
        super(name);
    }
}

let stu = new Student("Alice");
stu.sayHello(); // Hello World
console.log(stu instanceof Student); // true
console.log(stu instanceof Person);  // true
```

继承自其它类的类称为子类，或者派生类。在子类的构造方法 `constructor` 中，必须首先通过调用 `super` 方法来调用父类的构造函数，它会在这里初始化 `this`，如果没有调用 `super` 方法的话，会抛出错误，并且 `super` 方法只能被调用一次，且必须在访问 `this` 变量之前。

唯一不用在子类中调用 `super` 方法的情况就是在构造函数中返回一个对象。

### 方法重写

我们可以在子类中重写父类中的方法

```javascript
class Person {
  constructor(name) {
    this.name = name;
  }

  sayHello() {
    console.log("Hello World");
  }
}

class Student extends Person{
  constructor(name) {
    super(name);
  }

    // 重写父类的方法
  sayHello() {
    console.log("Hi");
  }
}

let stu = new Student("Alice");
stu.sayHello(); // Hi
```

上面在 `Student` 中重写了 `Person sayHello` 方法，所以最后打印出的结果是 `"Hi"`，如果我们需要在子类中调用父类中被重写方法，我们可以通过 `super` 调用，`super` 指向的就是父类的方法

```js
class Student extends Person{
  // ...
  sayHello() {
    super.sayHello();
    console.log("Hi");
  }
}

let stu = new Student("Alice");
stu.sayHello(); // Hello World
                // Hi
```

### 静态方法继承

类中的静态方法也可以被继承

```js
class Person {
  static print(...value) {
    console.log(...value);
  }
}

class Student extends Person {}

Student.print("Hello", "World");
```

上面 `Student` 继承了 `Person`，所以可以通过 `Student` 调用它继承的 `Person` 的静态方法 `print`。

### 继承自表达式

`extends` 后面不仅仅可以是类名，还可以是一个函数，只要函数有 `[[Contructor]]` 方法即可，也可以是一个表达式，只要表达式的结果是具有 `[[Contructor]]` 方法的函数即可

```js
function Person(name) {
  this.name = name;
}

Person.print = console.log;

// 继承自一个函数
class Student extends Person {}

Student.print("Hello", "World"); // Hello World
```

### 继承内置类

我们可以继承内置 (build-in) 类

```js
class MyArray extends Array {}

let arr = new MyArray();
arr[0] = "red";

console.log(arr);        // MyArray(1) [ 'red' ]
console.log(arr.length); // 1

arr.length = 0;
console.log(arr);        // MyArray(0) []
```

上面我们自定类继承了 Array 即数组，因此它的工作方式通数组类似。

### Symbol.species 属性

首先介绍一下数组的 `slice` 方法，`slice` 方法是对数组内的元素进行拷贝，然后返回一个新的数组，该方法接收两个参数，`begin` 和 `end`，表示拷贝的起始位置和终止位置，这两个参数都是可选的，如果默认没有传入参数的话，`begin` 的默认值是 0，`end` 默认表示拷贝到数组的最后一个元素

```js
let arr = ["red", "green", "blue"];

let newArr = arr.slice(0, 2);
console.log(newArr);                  // [ 'red', 'green' ]
console.log(newArr instanceof Array); // true
```

我们知道 `slice` 返回的是一个数组，它是 `Array` 类型的实例，现在如果我们自定义类 `MyArray` 继承 `Array` 类，调用 `slice` 方法

```js
class MyArray extends Array {}

let arr = new MyArray();
arr.push(...["red", "green", "blue"]);

let newArr = arr.slice();
console.log(newArr instanceof MyArray); // true
console.log(newArr instanceof Array);   // true
```

我们发现返回的是 `MyArray` 的实例，而不是 `Array` 实例，这是因为 `Symbol.species` 属性，它控制着 `slice` 方法返回的数组是谁的实例

```js
class MyArray extends Array {
  static get [Symbol.species]() {
    return Array;
  }
}

let arr = new MyArray();
arr.push(...["red", "green", "blue"]);

let newArr = arr.slice();
console.log(newArr instanceof MyArray); // false
console.log(newArr instanceof Array);   // true
```

上面我们在 `MyArray` 中添加了一个静态方法 `[Symbol.species]`，它返回的值时 `Array`，表示调用 `slice` 方法后返回的实例是 `Array` 的实例。

## new.target

我们可以在类的 `contructor` 方法中访问 `new.target`，通过 `new.target` 我们可以构造一个抽象类，所谓的抽象类，指的就是不能被 `new` 出实例的类，它只能被继承

```js
class Shape {
  constructor(name) {
    if (new.target === Shape) {
      throw new Error("Can't be called by new!");
    }
  }
}

class Rectangle extends Shape {
  constructor(name, width, height) {
    super(name);
    this.width = width;
    this.height = height;
  }
}

let rec = new Shape("长方形");
```

`Shape` 类是一个抽象类，它不能通过 `new` 来实例化，上面当我们 `new Shape` 时，会抛出一个错误

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting/20210116191657.png" alt="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting/20210116191657.png" style="zoom:50%;" />

我们可以实例化它的子类

```js
let rec = new Rectangle("长方形", 10, 5);
```
