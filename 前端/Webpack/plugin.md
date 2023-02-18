---
tags: [webpack, plugin]
---

`plugin` 是 `webpack` 的另一大特色，它为我们提供了相当多的功能，在 `webpack` 打包的整个生命周期中会广播出很多事件，而 `plugin` 可以监听这些事件，在合适的时机通过 `webapck` 提供的 `API` 改变输出的结果。下面就介绍一些常用到的 `plugin`。

## mini-css-extract-plugin

在处理 `CSS` 的 `loader` 中，我们最后使用 `style-loader` 将最后的样式以 `style` 标签的形式插入到页面中，但是如果我们希望将样式抽离出一个 `CSS` 文件呢? 这个时候我们就需要用到 `mini-css-extract-plugin` 插件了。首先进行下载

```bash
npm install mini-css-extract-plugin --save-dev
```

接着在 `webpack.config.js` 中进行配置

```javascript
const path = require("path");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
module.exports = {
  // 省略其他配置
  module: {
    rules: [
      // 省略其他 loader 配置
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, "css-loader"],
      },
    ],
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: "main.css", // 抽离出的 css 文件名称
    }),
  ],
};
```

首先我们通过 `require('mini-css-extract-plugin')` 引入该插件，接着在 `plugins` 选项中配置了该插件，并设置了抽离后的 `CSS` 文件的名称，接着因为我们希望最后抽离出一个 `CSS` 文件而不是将文件以 `style` 标签的形式插入到页面中，所以我们使用了 `MiniCssExtractPlugin.loader` 来替换了 `style-loader`。

我们运行 `npm run build` 就可以观察到在 `dist` 目录下生成了一个 `main.css` 文件。为了观察到效果，我们在 `dist/index.html` 中将该 `main.css` 引入

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>Title</title>
  </head>
  <body>
    <link rel="stylesheet" href="./main.css" />
    <script src="bundle.js"></script>
  </body>
</html>
```

打开该 `html` 就可以看到效果了。

## clean-webpack-plugin

该插件的作用是在每次打包前删除 `dist` 文件夹，这样我们每次都可以看到最新的打包文件。可能你还不能理解这个应用场景，现在我们修改 `output.filename` 如下

```javascript
output: {
    filename: "[name]@[chunkHash].js",
    path: path.resolve(__dirname, "dist")
}
```

每次生成的文件由名字和文件内容生成的 `hash` 值组成，每次我们修改程序，然后打包，就会产生不同的哈希值，所以 `dist` 下的文件会不断的增多，我们根本不知道哪个文件是我们最新打包出来的，如下

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/202204221930472022-04-22-19-30-47.png" style="zoom:50%"/>

所以我们就有这个需要，在生成新的打包文件之前，将之前的打包文件进行删除。首先下载 `clean-webpack-plugin`

```bash
npm install clean-webpack-plugin --save-dev
```

接着配置 `webpack.config.js`

```javascript
// 省略其他
const { CleanWebpackPlugin } = require("clean-webpack-plugin");
module.exports = {
  // 省略其他配置
  plugins: [
    new MiniCssExtractPlugin({
      filename: "main.css",
    }),
    new CleanWebpackPlugin(),
  ],
};
```

再次进行打包，发现之前的 `dist` 文件夹下之前的打包文件都被删掉了。

## html-webpack-plugin

使用 `clean-webpack-plugin` 会将我们之前在 `dist` 下新建的 `index.html` 文件也会删除掉，为了观察打包效果，我们不得不在 `dist` 目录下再次新建 `index.html` 并且将打包后的 `JavaScript` 文件和 `CSS` 文件引入，每次打包一次都要这么做实在令人恼火，我们使用 `html-webpack-plugin` 来解决这个问题。

`html-webpack-plugin` 会根据一个 `html` 文件模板，在 `dist` 文件夹下生成一个 `html` 文件，并且会将打包后的 `JavaScript` 和 `CSS` 自动引入，大大的解放了我们的双手。首先我们下载 `html-webpack-plugin`

```bash
npm install html-webpack-plugin --save-dev
```

接着在 `webpack.config.js` 中进行配置

```javascript
// 省略其他
const HtmlWebpackPlugin = require("html-webpack-plugin");
module.exports = {
  // 省略其他配置
  plugins: [
    new MiniCssExtractPlugin({
      filename: "main.css",
    }),
    new CleanWebpackPlugin(),
    new HtmlWebpackPlugin({
      template: "./src/index.html",
      filename: "index.html",
    }),
  ],
};
```

其中我们在 `HtmlWebpackPlugin` 中传入了两个配置项

| 配置项     | 作用                           |
| ---------- | ------------------------------ |
| `template` | 指定模板的路径                 |
| `filename` | 指定生成的 `html` 文件的文件名 |

在上面，我们将模板指定为 `src/index.html`，所以我们在 `src` 新建 `index.html` 如下

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>Title</title>
  </head>
  <body></body>
</html>
```

只是一个标准的 `html` 文件，里面什么都没有。运行 `npm run build` 即可观察到在 `dist` 文件夹下生成了一个 `index.html` 文件

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/202204221932302022-04-22-19-32-30.png" style="zoom:50%"/>

生成的 `index.html` 的内容如下

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>Title</title>
    <link href="main.css" rel="stylesheet" />
  </head>
  <body>
    <script src="bundle%4063ea546939ae13c70c31.js"></script>
  </body>
</html>
```

可见它已经自动为我们引入了打包后的 `JavaScript` 文件和 `CSS` 文件。
