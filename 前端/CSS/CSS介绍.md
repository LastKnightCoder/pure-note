---
tags: [CSS]
---

`HTML` 为网页搭建好了骨架，但是这远远是不够的，我们要求页面应该美观，这就需要用到 `CSS`。`CSS` 全称为 Cascading Style Sheets，中文翻译过来为层叠样式表，它可以为标签添加样式，例如改变文字颜色，设置对齐方式等等。

## style属性

通过为设置标签的 `style` 属性便可以为标签添加样式

```antd
let { Tabs } = antd;
const { TabPane } = Tabs;
const { ThemeProvider } = components;
Tabs = ThemeProvider(Tabs);

const code = `\`\`\`html
<body>
  <h1 style="color: red;">Hello World!</h1>
  <p style="text-align: center;">今天的天气真好</p>
</body>
\`\`\``

const html = await renderMarkdown(code)

const CodeTab = () => (
  <Tabs defaultActiveKey="1">
    <TabPane tab={'HTML'} key="1">
	  <div dangerouslySetInnerHTML={{ __html: html }} />
    </TabPane>
    <TabPane tab={'RESULT'} key="2">
	  <h1 style={{color: 'red', border: 'none'}}>Hello World!</h1>
	  <p style={{textAlign: 'center'}}>今天的天气真好</p>
    </TabPane>
  </Tabs>
);

const root = ReactDOM.createRoot(el)
root.render(<CodeTab />)
```

我们为 `h1` 标签添加了 `color: red` 的样式，此样式的作用是设置字体颜色为红色，我们为 `p` 标签设置了 `text-align: center` 的样式，此样式的作用是设置文本水平居中。

## style标签

除了可以通过 `style` 属性添加样式，还可以通过 `style` 标签添加样式

```html {5-12}
<!DOCTYPE html>
<html lang="en">
<head>
    <title>CSS介绍</title>
    <style>
        h1 {
            color: blue; /* 设置一级标题的字体颜色为蓝色*/
        }
        p {
            text-align: right; /* 设置 p 中文本对齐的方式为右对齐 */
        }
    </style>
</head>
<body>
    <h1>Hello World!</h1>
    <p>今天天气真好</p>
</body>
</html>
```

在 `head` 标签中有一个 `style` 标签，在  `style` 标签中我们书写几个样式，为 `h1` 添加了 `color: blue` 的样式，设置字体颜色为蓝色，为 `p` 标签添加了一条 `text-align: right` 的样式，设置对齐方式为右对齐。

<div class="displaybox">
  <h1 style="color: blue;">Hello World!</h1>
  <p style="text-align: right;">今天天气真好</p>
</div>

```` info
注释：在上面的 `CSS` 代码中，我们混入了以下内容

```css
h1 {
    color: blue; /* 设置一级标题的字体颜色为蓝色*/
}
p {
    text-align: right; /* 设置 p 中文本对齐的方式为右对齐 */
}
```
````

其中在 `/*  */` 中的内容为注释，其中的内容是写给人看的，是为了帮助阅读代码的人搞懂代码的意思，浏览器会自动忽略注释。

## link标签

当网页的规模不大，样式较少时，可以将样式写在 `style` 标签中，但是当网页变得复杂，将样式写在 `style` 标签不好调试 ，所以我们一般将样式写在一个单独的文件中，然后通过 `link` 标签引入该样式文件。假设目录结构如下

```css
index.html
index.css
```

`index.css` 为样式文件，它与 `index.html` 在同一个文件夹下面，我们现在需要在 `index.html` 中引入 `index.css`

```antd
let { Tabs } = antd;
const { TabPane } = Tabs;
const { ThemeProvider } = components;
Tabs = ThemeProvider(Tabs);

const code = `\`\`\`html
<!-- index.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <title>link标签</title>
    <link rel="stylesheet" href="./index.css">
</head>
<body>
    <h1>Hello World!</h1>
    <p>今天天气真好</p>
</body>
</html>
\`\`\``

const cssCode = `\`\`\`css
/* index.css */
h1 {
    color: orange;
}
p {
    font-size: 2em;
}
\`\`\``


const html = await renderMarkdown(code)
const css = await renderMarkdown(cssCode)

const CodeTab = () => (
 <Tabs defaultActiveKey="1">
    <TabPane tab="HTML" key="1">
      <div dangerouslySetInnerHTML={{ __html: html }} />
    </TabPane>
    <TabPane tab="CSS" key="2">
      <div dangerouslySetInnerHTML={{ __html: css }} />
    </TabPane>
    <TabPane tab="RESULT" key="3">
      <div class="displaybox">
          <h1 style={{color: 'orange'}}>Hello World!</h1>
          <p style={{fontSize: '2em'}}>今天的天气真好</p>
      </div>
    </TabPane>
  </Tabs>
);
const root = ReactDOM.createRoot(el)
root.render(<CodeTab />)
```


上面我们设置了 `h1` 的字体颜色为 `orange`，并且设置了 `p` 标签的字体大小为 `2em`，即两倍的字体大小。 `em` 为字体大小单位， `1em` 表示一倍的字体大小，`2em` 则表示两倍的字体大小。

通过 `link` 标签的 `href` 属性来指明 `CSS` 文件的位置，浏览器会根据指出的位置下载文件，然后解析该文件，为网页添加样式。`link` 所引用的文件不仅仅可以是本地文件，甚至可以是网络文件

```html
<link href="https://cdn.bootcdn.net/ajax/libs/tailwindcss/2.0.4/base.css" rel="stylesheet">
```

`rel` 属性设置为 `stylesheet`，它的目的是为了告诉浏览器我引入的是层叠样式表文件，在 `HTML5` 中，这一属性可以省略。

## @import

我们还可以通过 `@import` 来导入样式文件

```html
<style>
    @import url("index.css");
</style>
```

除了可以在 `style` 标签中通过 `@import` 导入样式文件，还可以在 `CSS` 文件中使用 `@import`

```css
/* main.css */
@import "https://cdn.bootcdn.net/ajax/libs/tailwindcss/2.0.4/base.css"
```

``` tip
通过 `link` 引入 `CSS` 文件与通过 `@import` 引入 `CSS` 文件有何不同? 

二者差别不是很大，`link` 标签是 `HTML` 引入样式表的机制，而 `@import` 是 `CSS` 引入样式表的机制。但是浏览器处理他们的方式是不同的，就性能而言，`link` 标签具有明显的优势，所以推荐使用 `link` 引入样式。
```

