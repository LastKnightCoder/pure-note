---
tags: [Obsidian, 插件开发]
---

## 动机

我需要一个 Tab 组件，进行内容的切换，自然而然的想到使用目前的组件库（也不是自然而然，之前还打算从零开始实现，但是发现自己的审美太垃圾，不妨用现有的组件库，简洁又美观），然后文档上有 React 的使用介绍，于是选择 React 框架而不是 Vue，虽然文档只写了React，但是我觉得 Vue 也是可以的，考虑后续接入 Vue 组件。

## 实现

说实话，一开始根本没有想清楚怎么做，因为对 React 不是很熟，后来想直接让用户写 React 代码来实现组件，我直接将 React、ReactDOM、antd、charts 直接暴露到 window 中，然后将用户写的代码直接通过 `eval` 执行，这样有一定的风险，因为如果代码写的有错的话，页面会整个崩溃，不是目前都是自己用，还是没问题的。

实现了可以将 `antd` 的组件拿过来使用

Step 组件：

```antd
const { Steps } = antd;
const { Step } = Steps;
const { ThemeProvider } = components;
const ThemeSteps = ThemeProvider(Steps);

const stepEl = (
  <ThemeSteps current={1} status="error">
    <Step title="Finished" description="This is a description" />
    <Step title="In Process" description="This is a description" />
    <Step title="Waiting" description="This is a description" />
  </ThemeSteps>
);

const root = ReactDOM.createRoot(el)
root.render(stepEl)
```

为了使其支持 JSX 语法，使用了 `@babel/standalone` 进行了转译，另外为了使其支持 `await`，将源代码包裹在了一个匿名 `async` 立即执行函数外面。

不过还有一点瑕疵，就是里面的组件的内容它是不支持 Markdown，如果在里面直接写代码块是不会渲染的，这个任务我决定交给用户，暴露了一个 `renderMarkdown` 的方法，接收 Markdown，返回 `html`，并且是一个异步的方法，这样我就实现了我的目的，一个 Tab 组件

```antd
const { Tabs } = antd;
const { TabPane } = Tabs;
const { ThemeProvider } = components;
const ThemeTabs = ThemeProvider(Tabs);

const code1 = `\`\`\` C
#include<stdio.h>
int main()
{
    printf("Hello World");
    return 0;
}
\`\`\``

const code2 = `\`\`\` JavaScript
const a = 1
const b = 2
const c = a + b
console.log(c)
\`\`\``

const html1 = await renderMarkdown(code1)
const html2 = await renderMarkdown(code2)

const CodeTab = () => (
  <ThemeTabs defaultActiveKey="1">
    <TabPane tab="C" key="1">
      <div dangerouslySetInnerHTML={{ __html: html1 }} />
    </TabPane>
    <TabPane tab="JavaScript" key="2">
      <div dangerouslySetInnerHTML={{ __html: html2 }} />
    </TabPane>
  </ThemeTabs>
);

const root = ReactDOM.createRoot(el)
root.render(<CodeTab />)
```

为了每次操作之后，再次打开的时候能够保持之前的状态，添加了两个 Hook，useLocalStorage 和 useFile，一个是保存在 localStorage 中，一个是保存在文件中

useFile：

```antd
const { Button } = antd;
const { ThemeProvider } = components;
const ThemeButton = ThemeProvider(Button);

const Counter = () => {
    const [counter, setCounter] = useFile('counter.md', "0")
    const handleClick = () => {
	    setCounter(+counter + 1 + "")
    }
	return (
		<ThemeButton type="primary" onClick={handleClick}>
			{counter}++
		</ThemeButton>
	)
}
const root = ReactDOM.createRoot(el)
root.render(<Counter />)
```

useLocalStorage：

```antd
const { Button } = antd;
const { ThemeProvider } = components;
const ThemeButton = ThemeProvider(Button);

const Counter = () => {
    const [counter, setCounter] = useLocalStorage('obsdian-antd-counter', "0")
    const handleClick = () => {
	    setCounter(parseInt(counter) + 1 + "")
    }
	return (
		<ThemeButton type="primary" onClick={handleClick}>
			{counter}++
		</ThemeButton>
	)
}
const root = ReactDOM.createRoot(el)
root.render(<Counter />)
```

二者几乎可以互换使用，有一点不同的是，useFile 只能接收字符串数据，而 useLocalStorage 是可以接收数字的。

## 踩坑点

```` warning
开发插件的时候有一个坑，如果我试图通过如下方式引用 `fs`，编译会不通过

```js
import fs from 'node:fs/promises'
```

但是我通过 `require` 引入的就没问题

```js
const fs = require('fs').promises
```

````

````tip
还获取到的一个知识是，通过 `this.app.valut.adpater.basePath` 可以获得笔记仓库所在文件路径，不过这个方法接口并没有暴露，使用时需要添加 `@ts-ignore`

```typescript
// @ts-ignore
const root = this.app.valut.adpater.basePath
```
````


