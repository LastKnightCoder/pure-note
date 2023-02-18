---
tags: [JavaScript, 加号运算符，ES标准, 题目]
---

今天看到几道有关加号的题目，觉得很怪异如下

```javascript
[] + {} // '[object Object]'
[] + [] // ''
{} + [] // 0
{} + {} // NaN
[] + {} == {} + [] // true
{} + [] != [] + {} // true
```

当时我就完全没有搞懂，所以决定探索一下 `JavaScript` 中的 `+` 号运算符，另外，对于 `toString` 和 `valueOf` 这两个方法一直搞不清会调用哪个，在探索 `+` 号运算符的过程中也一并搞懂了，我将会在下面仔细讲解。

## 一元运算符

`+` 既可以作为一元运算符，也可以作为二元运算符，首先我们先讲解较简单的一元运算符，如下表：

| 类型        | 转换规则                                                     |
| ----------- | ------------------------------------------------------------ |
| `undefiend` | `NaN`                                                        |
| `null`      | `0`                                                          |
| `boolean`   | `true => 1`<br />`false => 0`                                |
| `number`    | 原样返回，如 `+1 => 1`                                       |
| `string`    | 1. 如果字符串为纯数字组成，如 `"5678" => 5678`<br />2. 如果不为纯数字，那么返回 `NaN`<br />3. 空字符串会被转化为 `0`，即 `"" => 0` |
| `symbol`    | 抛出 `TypeError` 异常                                        |
| `object`    | 分为两步：<br />1. 先进行 `toPrimitive` 转化为基本数据类型，得到返回值 `ret`<br />2. 然后对 `ret` 进行上面描述过程的转换，例如如果返回 `true`，得到 `1` |

下表是 `ECMAScript` 中的规范，上面的内容来自这里：

| Argument    | Result                                                       |
| ----------- | ------------------------------------------------------------ |
| `Undefined` | `NaN`                                                        |
| `Null`      | `+0`                                                         |
| `Boolean`   | `The result is 1 if the argument is true.` <br />`The result is +0 if the argument is false.` |
| `Number`    | `The result equals the input argument (no conversion).`      |
| `String`    | `See grammar and note below.`                                |
| `Object`    | `Apply the following steps:`<br />`1. Let primValue be ToPrimitive(input argument, hint Number).`<br />`2. Return ToNumber(primValue).` |

下面来看几个例子，来验证上面的内容

```javascript
console.log(+undefined); // NaN
console.log(+null); // 0
console.log(+true); // 1
console.log(+false); // 0
console.log(+2); // 2
console.log(+Symbol()); // Uncaught TypeError: Cannot convert a Symbol value to a number
console.log(+"123"); // 123
console.log(+"1aa1"); // NaN
```

对于对象首先要进行 `toPrimitive` 转化为原始类型，然后将原始类型转化为数字

```javascript
let obj1 = {
    valueOf() {
        return 12;
    }
};
console.log(+obj1); // 12
let obj2 = {};
console.log(+obj2); // NaN
```

对于空对象，在转化为原始值时得到的是 `"[object Object]"`，将它转化为数字时，这个字符串不是纯数字，所以会被转化为 `NaN`。

## 二元运算符

当把 `+` 作为二元运算符时，遵循以下过程：

1. Let *lref* be the result of evaluating *AdditiveExpression*.
2. Let *lval* be [GetValue](http://www.ecma-international.org/ecma-262/6.0/#sec-getvalue)(*lref*).
3. [ReturnIfAbrupt](http://www.ecma-international.org/ecma-262/6.0/#sec-returnifabrupt)(*lval*).
4. Let *rref* be the result of evaluating *MultiplicativeExpression*.
5. Let *rval* be [GetValue](http://www.ecma-international.org/ecma-262/6.0/#sec-getvalue)(*rref*).
6. [ReturnIfAbrupt](http://www.ecma-international.org/ecma-262/6.0/#sec-returnifabrupt)(*rval*).
7. Let *lprim* be [ToPrimitive](http://www.ecma-international.org/ecma-262/6.0/#sec-toprimitive)(*lval*).
8. [ReturnIfAbrupt](http://www.ecma-international.org/ecma-262/6.0/#sec-returnifabrupt)(*lprim*).
9. Let *rprim* be [ToPrimitive](http://www.ecma-international.org/ecma-262/6.0/#sec-toprimitive)(*rval*).
10. [ReturnIfAbrupt](http://www.ecma-international.org/ecma-262/6.0/#sec-returnifabrupt)(*rprim*).
11. If [Type](http://www.ecma-international.org/ecma-262/6.0/#sec-ecmascript-data-types-and-values)(*lprim*) is String or [Type](http://www.ecma-international.org/ecma-262/6.0/#sec-ecmascript-data-types-and-values)(*rprim*) is String, then
    1. Let *lstr* be [ToString](http://www.ecma-international.org/ecma-262/6.0/#sec-tostring)(*lprim*).
    2. [ReturnIfAbrupt](http://www.ecma-international.org/ecma-262/6.0/#sec-returnifabrupt)(*lstr*).
    3. Let *rstr* be [ToString](http://www.ecma-international.org/ecma-262/6.0/#sec-tostring)(*rprim*).
    4. [ReturnIfAbrupt](http://www.ecma-international.org/ecma-262/6.0/#sec-returnifabrupt)(*rstr*).
    5. Return the String that is the result of concatenating *lstr* and *rstr.*
12. Let *lnum* be [ToNumber](http://www.ecma-international.org/ecma-262/6.0/#sec-tonumber)(*lprim*).
13. [ReturnIfAbrupt](http://www.ecma-international.org/ecma-262/6.0/#sec-returnifabrupt)(*lnum*).
14. Let *rnum* be [ToNumber](http://www.ecma-international.org/ecma-262/6.0/#sec-tonumber)(*rprim*).
15. [ReturnIfAbrupt](http://www.ecma-international.org/ecma-262/6.0/#sec-returnifabrupt)(*rnum*).
16. Return the result of applying the addition operation to *lnum* and *rnum*. See the Note below [12.7.5](http://www.ecma-international.org/ecma-262/6.0/#sec-applying-the-additive-operators-to-numbers).

上面是 `ECMAScript` 中的规范，如果英文不太熟的话，下面我将以中文简单翻译如下：

1. 首先将 `+` 号两边的值通过 `toPrimitive` 转化为基本数据类型(如果已经是基本数据类型，则原样返回)
2. 如果得到的两个基本数据类型中有字符串，那么将二者转化为字符串拼接起来，将结果返回
3. 如果两个基本数据类型中没有字符串，那么就将两个值转化为数字，然后进行相加

下面来看几个例子：

```javascript
// 两个值中有一个为字符串，则调用二者的 toString 方法，然后进行相加
"" + 2; // "2"
true + "abc"; // "trueabc"

// 两个值中没有字符串，那么转化为数字然后相加
2 + true; // => 2 + 1 = 3
null + 2; // => 0 + 2 = 2
[] + 2; // => "" + 2 = "2"
```

对于对象要转化为原始类型，然后进行相加，那么对象转化为原始类型的过程是什么? 下面是调用的过程：

- 如果有 `[Symbol.toPrimitive]` 方法，则调用 `[Symbol.toPrimitive]()` 方法转化为原始类型，该方法的返回必须为原始类型
- 如果没有 `[Symbol.toPrimitive]` 方法，那么调用 `valueOf` 方法，如果 `valueOf` 返回的不是原始值(基本数据类型)，那么就调用 `toString` 方法，如果 `toString` 返回的也不是原始值，那么就会报错

```javascript
[] + 2; // "" + 2 = "2"
```

数组也是对象，因为数组没有 `[Symbol.toPrimitive]` 方法，所以首先会调用数组的 `valueOf` 方法，因为数组的 `valueOf` 方法返回的是数组本身，并不是基本数据类型，所以接着会调用数组的 `toString` 方法，得到一个空字符串 `""` ，通过我们上面的讲解，如果两个值中有一个是字符串的话，则会将二者转化为字符串进行拼接，所以 `[] + 2 => "" + 2 = "2"`。下面在来看一个例子：

```javascript
let obj = {
    [Symbol.toPrimitive](hint) {
        return 20;
    },
    valueOf() {
        return 0;
    }
};
2 + obj; // 22
```

由于对象 `obj` 有 `[Symbol.toPrimitive]` 方法，所以在转化为基本数据类型时会调用该方法，得到的值为 `20`，所以 `2 + obj => 2 + 20 = 22`。

## 题目讲解

回到开头我们提出的几个例子：

```javascript
[] + {} // '[object Object]'
```

首先看 `[] + {}`，首先将两个东西转化为基本数据类型，因为它们都没有 `[Symbol.toPrimitive]` 方法，所以接着会调用它们的 `valueOf` 方法，但是它们的 `valueOf` 方法返回的都是它们本身，所以接着会调用它们的 `toString` 方法，`[]` 的 `toString` 方法得到的是 `''` 空字符串，`{}` 的 `toString` 方法得到的是 `'[object Object]'`，二者都是字符串，将二者进行拼接，得到的结果是 `'[object Object]'`。

```javascript
[] + [] // ''
```

接着看 `[] + []`，有上题的经验，`[] => ''`，两个空字符串进行拼接得到的结果是 `''`。

```javascript
{} + [] // 0
```

接着看 `{} + []`，因为 `JavaScript` 会将以 `{` 开头的语句解析为代码块而不是一个空对象，所以 `{} + []` 相当于 `+[]`，这时的 `+` 相当于是一个一元运算符，根据一元运算符上面讲解的内容，首先将 `[]` 转化为基本数据类型，得到 `""`，接着将 `""` 转化为数字，得到的结果为 `0`。

```javascript
{} + {} // NaN
```

根据上一道题的讲解，`{} + {}` 相当于 `+{}`，`{}` 转换为基本数据类型得到的是 `"[object Object]"`，该字符串转换为数字得到的 `NaN`，所以结果是 `NaN`。

```javascript
[] + {} == {} + [] // true
```

`[] + {}` 得到的结果是 `'[object Object]'`，`{} + []` 得到的结果并不是 `0`，因为这时 `{}` 并不是在语句的开头，会被看做是空对象，所以 `{} + []` 得到的结果也是 `'[object Object]'`，二者是相等的，结果是 `true`

```javascript
{} + [] != [] + {} // true
```

`{} + []` 因为在开头，得到的结果是 `0`，而 `[] + {}` 的结果是 `[object Object]`，二者不相等，所以 `{} + [] != [] + {}` 的结果也是 `true`。

## 参考文献

- [JavaScript 加号运算符详解](https://www.cnblogs.com/polk6/p/js-adv-addopr.html)
- [valueOf() vs. toString() in Javascript](https://stackoverflow.com/questions/2485632/valueof-vs-tostring-in-javascript)
- [The Addition operator ( + )](http://www.ecma-international.org/ecma-262/6.0/#sec-addition-operator-plus-runtime-semantics-evaluation)
- [ToNumber](https://www.ecma-international.org/ecma-262/5.1/#sec-9.3)
- [ECMAScript7规范中的ToPrimitive抽象操作](https://segmentfault.com/a/1190000016325587)
- [Object to primitive conversion](https://javascript.info/object-toprimitive)
- [Symbol.toPrimitive 与 [] + {} == {} + []](https://zhuanlan.zhihu.com/p/74982324)



