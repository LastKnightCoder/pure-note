---
title: Babel 不同配置 polyfill 方式的比较
created: 2023/2/12 11:43:14
updated: 2023/2/12 11:43:14
tags: [Babel, polyfill, core-js, transform-runtime, JavaScript, 配置]
---

不同 babel 配置的编译结果。

## `preset-env` 与  `useBuiltIns: 'entry'`

配置文件：

```js
{
  presets: [
    [
      "@babel/preset-env",
      {
        targets: "chrome 30",
        useBuiltIns: 'entry',
        corejs: 3,
        debug: true,
      }
    ]
  ]
};
```

源码：

```js
import 'core-js/stable';
const adder = (num1, num2) => num1 + num2;

const p = Promise.resolve();
class Person {

}

const pp = new Person();
```

编译后的代码：

```js
"use strict";

function _typeof(obj) { "@babel/helpers - typeof"; return _typeof = "function" == typeof Symbol && "symbol" == typeof Symbol.iterator ? function (obj) { return typeof obj; } : function (obj) { return obj && "function" == typeof Symbol && obj.constructor === Symbol && obj !== Symbol.prototype ? "symbol" : typeof obj; }, _typeof(obj); }
require("core-js/modules/es.symbol.js");
require("core-js/modules/es.symbol.description.js");
require("core-js/modules/es.symbol.async-iterator.js");
require("core-js/modules/es.symbol.has-instance.js");
require("core-js/modules/es.symbol.is-concat-spreadable.js");
require("core-js/modules/es.symbol.iterator.js");
require("core-js/modules/es.symbol.match.js");
require("core-js/modules/es.symbol.replace.js");
require("core-js/modules/es.symbol.search.js");
require("core-js/modules/es.symbol.species.js");
require("core-js/modules/es.symbol.split.js");
require("core-js/modules/es.symbol.to-primitive.js");
require("core-js/modules/es.symbol.to-string-tag.js");
require("core-js/modules/es.symbol.unscopables.js");
require("core-js/modules/es.array.concat.js");
require("core-js/modules/es.array.copy-within.js");
require("core-js/modules/es.array.fill.js");
require("core-js/modules/es.array.filter.js");
require("core-js/modules/es.array.find.js");
require("core-js/modules/es.array.find-index.js");
require("core-js/modules/es.array.flat.js");
require("core-js/modules/es.array.flat-map.js");
require("core-js/modules/es.array.from.js");
require("core-js/modules/es.array.includes.js");
require("core-js/modules/es.array.index-of.js");
require("core-js/modules/es.array.iterator.js");
require("core-js/modules/es.array.last-index-of.js");
require("core-js/modules/es.array.map.js");
require("core-js/modules/es.array.of.js");
require("core-js/modules/es.array.reduce.js");
require("core-js/modules/es.array.reduce-right.js");
require("core-js/modules/es.array.slice.js");
require("core-js/modules/es.array.sort.js");
require("core-js/modules/es.array.species.js");
require("core-js/modules/es.array.splice.js");
require("core-js/modules/es.array.unscopables.flat.js");
require("core-js/modules/es.array.unscopables.flat-map.js");
require("core-js/modules/es.array-buffer.is-view.js");
require("core-js/modules/es.array-buffer.slice.js");
require("core-js/modules/es.date.to-primitive.js");
require("core-js/modules/es.function.has-instance.js");
require("core-js/modules/es.json.to-string-tag.js");
require("core-js/modules/es.map.js");
require("core-js/modules/es.math.acosh.js");
require("core-js/modules/es.math.asinh.js");
require("core-js/modules/es.math.atanh.js");
require("core-js/modules/es.math.cbrt.js");
require("core-js/modules/es.math.clz32.js");
require("core-js/modules/es.math.cosh.js");
require("core-js/modules/es.math.expm1.js");
require("core-js/modules/es.math.fround.js");
require("core-js/modules/es.math.hypot.js");
require("core-js/modules/es.math.log10.js");
require("core-js/modules/es.math.log1p.js");
require("core-js/modules/es.math.log2.js");
require("core-js/modules/es.math.sign.js");
require("core-js/modules/es.math.sinh.js");
require("core-js/modules/es.math.tanh.js");
require("core-js/modules/es.math.to-string-tag.js");
require("core-js/modules/es.math.trunc.js");
require("core-js/modules/es.number.constructor.js");
require("core-js/modules/es.number.epsilon.js");
require("core-js/modules/es.number.is-integer.js");
require("core-js/modules/es.number.is-safe-integer.js");
require("core-js/modules/es.number.max-safe-integer.js");
require("core-js/modules/es.number.min-safe-integer.js");
require("core-js/modules/es.number.parse-float.js");
require("core-js/modules/es.number.parse-int.js");
require("core-js/modules/es.object.assign.js");
require("core-js/modules/es.object.define-getter.js");
require("core-js/modules/es.object.define-properties.js");
require("core-js/modules/es.object.define-property.js");
require("core-js/modules/es.object.define-setter.js");
require("core-js/modules/es.object.entries.js");
require("core-js/modules/es.object.freeze.js");
require("core-js/modules/es.object.from-entries.js");
require("core-js/modules/es.object.get-own-property-descriptor.js");
require("core-js/modules/es.object.get-own-property-descriptors.js");
require("core-js/modules/es.object.get-own-property-names.js");
require("core-js/modules/es.object.get-prototype-of.js");
require("core-js/modules/es.object.is-extensible.js");
require("core-js/modules/es.object.is-frozen.js");
require("core-js/modules/es.object.is-sealed.js");
require("core-js/modules/es.object.keys.js");
require("core-js/modules/es.object.lookup-getter.js");
require("core-js/modules/es.object.lookup-setter.js");
require("core-js/modules/es.object.prevent-extensions.js");
require("core-js/modules/es.object.seal.js");
require("core-js/modules/es.object.set-prototype-of.js");
require("core-js/modules/es.object.to-string.js");
require("core-js/modules/es.object.values.js");
require("core-js/modules/es.parse-float.js");
require("core-js/modules/es.parse-int.js");
require("core-js/modules/es.promise.js");
require("core-js/modules/es.promise.finally.js");
require("core-js/modules/es.reflect.apply.js");
require("core-js/modules/es.reflect.construct.js");
require("core-js/modules/es.reflect.define-property.js");
require("core-js/modules/es.reflect.delete-property.js");
require("core-js/modules/es.reflect.get.js");
require("core-js/modules/es.reflect.get-own-property-descriptor.js");
require("core-js/modules/es.reflect.get-prototype-of.js");
require("core-js/modules/es.reflect.has.js");
require("core-js/modules/es.reflect.is-extensible.js");
require("core-js/modules/es.reflect.own-keys.js");
require("core-js/modules/es.reflect.prevent-extensions.js");
require("core-js/modules/es.reflect.set.js");
require("core-js/modules/es.reflect.set-prototype-of.js");
require("core-js/modules/es.regexp.constructor.js");
require("core-js/modules/es.regexp.exec.js");
require("core-js/modules/es.regexp.flags.js");
require("core-js/modules/es.regexp.to-string.js");
require("core-js/modules/es.set.js");
require("core-js/modules/es.string.code-point-at.js");
require("core-js/modules/es.string.ends-with.js");
require("core-js/modules/es.string.from-code-point.js");
require("core-js/modules/es.string.includes.js");
require("core-js/modules/es.string.iterator.js");
require("core-js/modules/es.string.match.js");
require("core-js/modules/es.string.pad-end.js");
require("core-js/modules/es.string.pad-start.js");
require("core-js/modules/es.string.raw.js");
require("core-js/modules/es.string.repeat.js");
require("core-js/modules/es.string.replace.js");
require("core-js/modules/es.string.search.js");
require("core-js/modules/es.string.split.js");
require("core-js/modules/es.string.starts-with.js");
require("core-js/modules/es.string.trim.js");
require("core-js/modules/es.string.trim-end.js");
require("core-js/modules/es.string.trim-start.js");
require("core-js/modules/es.typed-array.float32-array.js");
require("core-js/modules/es.typed-array.float64-array.js");
require("core-js/modules/es.typed-array.int8-array.js");
require("core-js/modules/es.typed-array.int16-array.js");
require("core-js/modules/es.typed-array.int32-array.js");
require("core-js/modules/es.typed-array.uint8-array.js");
require("core-js/modules/es.typed-array.uint8-clamped-array.js");
require("core-js/modules/es.typed-array.uint16-array.js");
require("core-js/modules/es.typed-array.uint32-array.js");
require("core-js/modules/es.typed-array.copy-within.js");
require("core-js/modules/es.typed-array.every.js");
require("core-js/modules/es.typed-array.fill.js");
require("core-js/modules/es.typed-array.filter.js");
require("core-js/modules/es.typed-array.find.js");
require("core-js/modules/es.typed-array.find-index.js");
require("core-js/modules/es.typed-array.for-each.js");
require("core-js/modules/es.typed-array.from.js");
require("core-js/modules/es.typed-array.includes.js");
require("core-js/modules/es.typed-array.index-of.js");
require("core-js/modules/es.typed-array.iterator.js");
require("core-js/modules/es.typed-array.join.js");
require("core-js/modules/es.typed-array.last-index-of.js");
require("core-js/modules/es.typed-array.map.js");
require("core-js/modules/es.typed-array.of.js");
require("core-js/modules/es.typed-array.reduce.js");
require("core-js/modules/es.typed-array.reduce-right.js");
require("core-js/modules/es.typed-array.reverse.js");
require("core-js/modules/es.typed-array.set.js");
require("core-js/modules/es.typed-array.slice.js");
require("core-js/modules/es.typed-array.some.js");
require("core-js/modules/es.typed-array.sort.js");
require("core-js/modules/es.typed-array.to-locale-string.js");
require("core-js/modules/es.typed-array.to-string.js");
require("core-js/modules/es.weak-map.js");
require("core-js/modules/es.weak-set.js");
require("core-js/modules/web.dom-collections.for-each.js");
require("core-js/modules/web.dom-collections.iterator.js");
require("core-js/modules/web.immediate.js");
require("core-js/modules/web.queue-microtask.js");
require("core-js/modules/web.url.js");
require("core-js/modules/web.url.to-json.js");
require("core-js/modules/web.url-search-params.js");
function _defineProperties(target, props) { for (var i = 0; i < props.length; i++) { var descriptor = props[i]; descriptor.enumerable = descriptor.enumerable || false; descriptor.configurable = true; if ("value" in descriptor) descriptor.writable = true; Object.defineProperty(target, _toPropertyKey(descriptor.key), descriptor); } }
function _createClass(Constructor, protoProps, staticProps) { if (protoProps) _defineProperties(Constructor.prototype, protoProps); if (staticProps) _defineProperties(Constructor, staticProps); Object.defineProperty(Constructor, "prototype", { writable: false }); return Constructor; }
function _toPropertyKey(arg) { var key = _toPrimitive(arg, "string"); return _typeof(key) === "symbol" ? key : String(key); }
function _toPrimitive(input, hint) { if (_typeof(input) !== "object" || input === null) return input; var prim = input[Symbol.toPrimitive]; if (prim !== undefined) { var res = prim.call(input, hint || "default"); if (_typeof(res) !== "object") return res; throw new TypeError("@@toPrimitive must return a primitive value."); } return (hint === "string" ? String : Number)(input); }
function _classCallCheck(instance, Constructor) { if (!(instance instanceof Constructor)) { throw new TypeError("Cannot call a class as a function"); } }
var adder = function adder(num1, num2) {
  return num1 + num2;
};
var p = Promise.resolve();
var Person = /*#__PURE__*/_createClass(function Person() {
  _classCallCheck(this, Person);
});
var pp = new Person();
```

特点：

- 全局导入，污染全局变量
- 产生了很多辅助函数 `_createClass` `_classCallCheck`，并且每个被处理的且用到相同特性文件都会产生相同的辅助函数，产生了重复，增大了包体积
- 将引入的 `core-js/stable` 或 `regenerator-runtime/runtime` 的所有的 polyfill 导入，即使你没有用到
  - `core-js/stable` 在整个项目中只能被导入一次，否则会产生错误，一般在入口文件导入
  - 在 7.4.0 以前，会将 @babel/polyfill 这个包的所有 polyfill 导入

## `preset-env` 与 `useBuiltIns: 'usage'`

配置文件：

```js
{
  presets: [
    [
      "@babel/preset-env",
      {
        targets: "chrome 30",
        useBuiltIns: 'usage',
        corejs: 3,
        debug: true,
      }
    ]
  ]
};
```

源码：

```js
const adder = (num1, num2) => num1 + num2;

const p = Promise.resolve();
class Person {

}

const pp = new Person();
```

编译后的代码：

```js
"use strict";

require("core-js/modules/es.symbol.to-primitive.js");
require("core-js/modules/es.date.to-primitive.js");
require("core-js/modules/es.symbol.js");
require("core-js/modules/es.symbol.description.js");
require("core-js/modules/es.number.constructor.js");
require("core-js/modules/es.object.define-property.js");
require("core-js/modules/es.symbol.iterator.js");
require("core-js/modules/es.array.iterator.js");
require("core-js/modules/es.string.iterator.js");
require("core-js/modules/web.dom-collections.iterator.js");
function _typeof(obj) { "@babel/helpers - typeof"; return _typeof = "function" == typeof Symbol && "symbol" == typeof Symbol.iterator ? function (obj) { return typeof obj; } : function (obj) { return obj && "function" == typeof Symbol && obj.constructor === Symbol && obj !== Symbol.prototype ? "symbol" : typeof obj; }, _typeof(obj); }
require("core-js/modules/es.object.to-string.js");
require("core-js/modules/es.promise.js");
function _defineProperties(target, props) { for (var i = 0; i < props.length; i++) { var descriptor = props[i]; descriptor.enumerable = descriptor.enumerable || false; descriptor.configurable = true; if ("value" in descriptor) descriptor.writable = true; Object.defineProperty(target, _toPropertyKey(descriptor.key), descriptor); } }
function _createClass(Constructor, protoProps, staticProps) { if (protoProps) _defineProperties(Constructor.prototype, protoProps); if (staticProps) _defineProperties(Constructor, staticProps); Object.defineProperty(Constructor, "prototype", { writable: false }); return Constructor; }
function _toPropertyKey(arg) { var key = _toPrimitive(arg, "string"); return _typeof(key) === "symbol" ? key : String(key); }
function _toPrimitive(input, hint) { if (_typeof(input) !== "object" || input === null) return input; var prim = input[Symbol.toPrimitive]; if (prim !== undefined) { var res = prim.call(input, hint || "default"); if (_typeof(res) !== "object") return res; throw new TypeError("@@toPrimitive must return a primitive value."); } return (hint === "string" ? String : Number)(input); }
function _classCallCheck(instance, Constructor) { if (!(instance instanceof Constructor)) { throw new TypeError("Cannot call a class as a function"); } }
var adder = function adder(num1, num2) {
  return num1 + num2;
};
var p = Promise.resolve();
var Person = /*#__PURE__*/_createClass(function Person() {
  _classCallCheck(this, Person);
});
var pp = new Person();
```

特点：

- 按需导入，而不是像 `entry` 一样所有的 polyfill 全部导入，并且不需要手动在入口导入 `core-js/stable`
- 全局导入，污染全局变量
- 同样产生了很多辅助函数，多个文件会同时生成这些辅助函数，会产生重复

## `@balel/plugin-transform-runtim`

除了通过 `preset-env` 来进行 polyfill，还可以通过插件 `@balel/plugin-transform-runtim` 来进行 polyfill。

配置文件：

```js
const options = {
  presets: [
    [
      "@babel/preset-env",
      {
        targets: "chrome 30",
        debug: true,
      }
    ]
  ],
  plugins: [[
    '@babel/plugin-transform-runtime', {
      corejs: 3
    }
  ]]
};
```

源码：

```js
const adder = (num1, num2) => num1 + num2;

const p = Promise.resolve();
class Person {

}

const pp = new Person();
```

编译后的代码：

```js
"use strict";

var _interopRequireDefault = require("@babel/runtime-corejs3/helpers/interopRequireDefault");
var _createClass2 = _interopRequireDefault(require("@babel/runtime-corejs3/helpers/createClass"));
var _classCallCheck2 = _interopRequireDefault(require("@babel/runtime-corejs3/helpers/classCallCheck"));
var _promise = _interopRequireDefault(require("@babel/runtime-corejs3/core-js-stable/promise"));
var adder = function adder(num1, num2) {
  return num1 + num2;
};
var p = _promise.default.resolve();
var Person = /*#__PURE__*/(0, _createClass2.default)(function Person() {
  (0, _classCallCheck2.default)(this, Person);
});
var pp = new Person();
```

- 按需导入，只导入用到的 `polyfill`
- 非全局注入，不会污染全局，如 `_promise`
- 所有的辅助函数均通过 `@babel/runtime-corejs3` 导入，而不是每个文件都生成，可减少文件体积
- 没有 `target` 选项，忽视目前浏览器的版本，即使浏览器已经内置支持，还是会使用 `polyfill`

配置文件（修改 `target: "chrome 100"`）：

```js
{
  presets: [
    [
      "@babel/preset-env",
      {
        targets: "chrome 100",
        debug: true,
      }
    ]
  ],
  plugins: [[
    '@babel/plugin-transform-runtime', {
      corejs: 3
    }
  ]]
}
```

编译后的代码：

```js
"use strict";

var _interopRequireDefault = require("@babel/runtime-corejs3/helpers/interopRequireDefault");
var _promise = _interopRequireDefault(require("@babel/runtime-corejs3/core-js-stable/promise"));
const adder = (num1, num2) => num1 + num2;
const p = _promise.default.resolve();
class Person {}
const pp = new Person();
```

Chrome 100 已经内置支持了 Promise，但是还是使用了 polyfill。

## level-top targets

如果 `@babel/plugin-transform-runtime` 如果能读取 targets 然后条件编译就好了，现在我们可以将 `targets` 选项配置到 level-top 中，而不仅仅是 preset-env 的选项。

配置文件：

```js
{
  targets: "chrome 100",
  presets: [
    [
      "@babel/preset-env",
      {
        debug: true,
      }
    ]
  ],
  plugins: [[
    '@babel/plugin-transform-runtime', {
      corejs: 3
    }
  ]]
};
```

源码：

```js
const adder = (num1, num2) => num1 + num2;

const p = Promise.resolve();
class Person {

}

const pp = new Person();
```

编译后的代码：

```js
"use strict";

const adder = (num1, num2) => num1 + num2;
const p = Promise.resolve();
class Person {}
const pp = new Person();
```

特点：

- 按需引入
- 非全局引入
- 辅助函数从 `runtime` 导入，而非在文件中定义（当前例子看不出来，将浏览器版本改低一点就可以）

## babel/babel-polyfills

preset-env 和 transform-runtime 都可以进行 polyfill，但是在使用上二者是冲突的，如果同时使用，由于 plugin 的执行顺序先于 preset， transform-runtime 插件生效。

为了减少心智负担，官方决定使用一个统一的插件来处理 polyfill，那就是 babel-polyfills。有两个优势：

- 可以选择 polyfill provider，由于历史原因，babel 一直用的都是 `core-js`，但是还有其他的方案，例如 `es-shims`
- 可以选择插入方式，分为三种
  - `entry-global`：相当于 `preset-env + useBuiltIns: 'entry'`
  - `usage-global`：相当于 `preset-env + useBuiltIns: 'usage'`
  - `usage-pure`：相当于 top-level targets 选项 + transform-rumtime

配置文件：

```js
{
  targets: "chrome 30",
  presets: ["@babel/preset-env"],
  plugins: [
    '@babel/plugin-transform-runtime',
    [
      'babel-plugin-polyfill-corejs3',
      {
        method: 'usage-pure'
      }
    ]
  ]
}
```

源码：

```js
const adder = (num1, num2) => num1 + num2;

const p = Promise.resolve();
class Person {

}

const pp = new Person();
```

编译后的代码

```js
"use strict";

var _interopRequireDefault = require("@babel/runtime/helpers/interopRequireDefault");
var _createClass2 = _interopRequireDefault(require("@babel/runtime/helpers/createClass"));
var _classCallCheck2 = _interopRequireDefault(require("@babel/runtime/helpers/classCallCheck"));
var _index = _interopRequireDefault(require("core-js-pure/stable/promise/index.js"));
var adder = function adder(num1, num2) {
  return num1 + num2;
};
var p = _index.default.resolve();
var Person = /*#__PURE__*/(0, _createClass2.default)(function Person() {
  (0, _classCallCheck2.default)(this, Person);
});
var pp = new Person();
```

特点：

- 灵活选择 polyfill provider 以及插入方式

更多用法可以参考 [babel/babel-polyfills](https://github.com/babel/babel-polyfills)。

