---
title: div和span
author: 熊滔
commentid: html:div-and-span
---

`div` 和 `span` 这两个标签不像其他的标签一样有什么功能，其包含的字体也没有特写的样式，例如加粗，斜体等等，这两个标签的作用主要还是配合 `CSS` 用来布局。

```html
<div>
Hello World!
</div>
<span>
Hello World!
</span>
```

<DisplayBox>
<div>
    <div>
    Hello World!
    </div>
    <span>
    Hello World!
    </span>
</div>
</DisplayBox>

我们可以观察到在这两个标签里面的文本没有任何的功能，也没有特别的样式(加粗、斜体、字体变化)。

`div` 与 `span` 的不同之处在于 `div` 独占一行，而 `span` 则是可以与其他标签共处一行

```html
<div>Hello World!</div>
<div>Hello World!</div>
<div>Hello World!</div>

<span>Hello World!</span>
<span>Hello World!</span>
<span>Hello World!</span>
```

<DisplayBox>
<div>
    <div>Hello World!</div>
    <div>Hello World!</div>
    <div>Hello World!</div>
    <span>Hello World!</span>
    <span>Hello World!</span>
    <span>Hello World!</span>
</div>
</DisplayBox>

