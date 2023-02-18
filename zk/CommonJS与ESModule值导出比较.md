---
tags: [JavaScript, CommonJS, ESModule, 模块]
---

CommonJS，值拷贝，不会更改

```js
// a.js
let a = 1;
const add = () => {
  a += 1;
}

exports.a = a;
exports.add = add;
```

```js
// b.js
const moduleA = require('./a');

console.log(moduleA.a); // 1
moduleA.add();
console.log(moduleA.a); // 1
```

ES Module，实时绑定，实时变化

```js
// c.mjs
export let a = 1;
export function add() {
  a += 1;
}
```

```js
// d.mjs
import { a, add } from './c.mjs';

console.log(a); // 1
add();
console.log(a); // 2
```