---
tags: [pdf, pdfjs, JavaScript, demo]
---

心心念念写一个网页版的 PDF 笔记软件，支持 Markdown 做笔记，支持跳转，但是发现 pdf.js 真的难用啊，几乎没有什么文档，全靠搜，太难了。

- [x] 显示 PDF
- [x] 支持高分辨率
- [x] 文字可选中
- [ ] 高亮文本
- [ ] Markdown 编辑器

## 显示 PDF

准备呈现 PDF 的容器以及在线引入 `pdf.js`

```html
<div id="container">
  <canvas id="pdf"></canvas>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.6.347/pdf.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.6.347/pdf.worker.min.js"></script>
```

当导入 `pdf.js` 之后，window 对象中会存在一个 `pdfjsLib` 对象，通过该对象的 `getDocument(url)` 方法可以加载 PDF，其取值可以是文件名，也可以是一个在线的 PDF 地址。

```js
;(async () => {
  const pdf = await pdfjsLib.getDocument('demo.pdf').promise;
})()
```

获得 `pdf` 对象后，通过其 `getPage(num)` 方法可以获得页面

```js
const page = await pdf.getPage(1);
```

调用 `page.render(context)` 方法即可将 PDF 页面渲染到网页上

```js
const viewport = page.getViewport({
  scale: 1
})
const canvas = document.getElementById('pdf')
canvas.width = Math.floor(viewport.width)
canvas.height = Math.floor(viewport.height)
const ctx = canvas.getContext('2d')
const renderContext = {
  canvasContext: ctx,
  viewport: viewport
}
await page.render(renderContext)
```

现在就可以在页面上看到 PDF 了。

<iframe src="https://lastknightcoder.github.io/pdfjs-demos/demo01.html" width="100%" height="880"></iframe>

```danger
最新版本的 `pdf.js` 使用了 `structuredClone` 这个 API，但是 Obsidian 估计版本过低，不支持这个 API，导致我通过 `iframe` 嵌入网页时，控制台一直报错，我把版本降到 `2.6.347` 就可以了。
```

## 高分辨率

通过上面嵌入的网页，可以看到显示的 PDF 很模糊，这个问题可以参考 [[Canvas 绘图模糊]]，解决方案就是设置标签上的 `width`、`height` 属性为 CSS 属性 `width`、`height` 的 `dpr` 倍，然后设置画布的 `scale` 为 `dpr`

```js
const outputScale = window.devicePixelRatio || 1;
canvas.width = Math.floor(viewport.width * outputScale)
canvas.height = Math.floor(viewport.height * outputScale)
canvas.style.width = Math.floor(viewport.width) + 'px'
canvas.style.height = Math.floor(viewport.height) + 'px'
const transform =
  outputScale !== 1 ? [outputScale, 0, 0, outputScale, 0, 0] : null

const renderContext = {
  canvasContext: context,
  transform: transform,
  viewport: viewport,
}
await page.render(renderContext)
```

<iframe src="https://lastknightcoder.github.io/pdfjs-demos/demo02.html" width="100%" height="880"></iframe>

## 选择文字

目前渲染出来的 PDF 文字不可选中，为了选中文字，需要添加一个文本层，并且需要引入 `pdf_viewer.js` 以及 `pdf_viewer.css`，如下

```html
<head>
  <style>
    #container {
      position: relative;
    }
  </style>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.6.347/pdf_viewer.css">
<head>

<body>
  <div id="container">
    <canvas id="pdf"></canvas>
    <div id="textLayer" class="textLayer"></div>
  </div>

  <script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.6.347/pdf.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.6.347/pdf.worker.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.6.347/pdf_viewer.js"></script>
</body>
```

为文本层添加上类名 `textLayer`，`pdf_viewer.css` 将其设置为绝对定位，以使得其覆盖在 Canvas 上层，因此我们要将容器 `container` 设置为相对定位。

设置好结构以后，通过 `page.getTextContent` 获得文本内容，通过 `pdfjsViewer.TextLayerBuilder` 类创建文本层实例，并将文本内容传入，调用实例的 `render` 方法将文本层渲染到页面上

```js
// ... ...
await page.render(renderContext)
const textContent = await page.getTextContent()
const textLayer = new pdfjsViewer.TextLayerBuilder({
  textLayerDiv: document.getElementById('textLayer'),
  pageIndex: page.pageIndex,
  viewport: viewport
})
textLayer.setTextContent(textContent)
textLayer.render()
```

<iframe src="https://lastknightcoder.github.io/pdfjs-demos/demo03.html?name=1" width="100%" height="880"></iframe>


