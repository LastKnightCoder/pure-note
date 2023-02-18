---
tags: [webpack, loader]
---

`loader` 又是一个新的概念，我们可以简单的理解为处理器，处理什么呢? 比如我们需要将 `ES6` 转换为 `ES5`，这就需要一个 `loader` 进行转换；比如我们需要将 `less` 转换为 `CSS`，这需要一个 `loader` 进行转换，比如需要将 `TypeScript` 转换为 `JavaScript`，这也需要一个 `loader` 进行转换；等等。

下面我们就介绍一些常用的 `loader`。

## babel-loader

我们常常有需要将 `ES6` 或更高级的语法转化为 `ES5`的需要，这个时候我们就要用到 `babel-loader`，我们需要下载这些安装包

```bash
npm install babel-loader @babel/core --save-dev
```

`@babel/core` 是 `babel` 的核心包，具体的转换要依赖该包，接着我们需要在 `webpack.config.js` 中进行配置

```javascript
const path = require("path");
module.exports = {
  devServer: {
    port: 3000,
    contentBase: path.resolve(__dirname, "dist"),
  },
  entry: {
    bundle: "./src/index.js",
  },
  output: {
    filename: "[name].js",
    path: path.resolve(__dirname, "dist"),
  },
  mode: "development",
  module: {
    rules: [
      // 配置 loader
      {
        test: /\.js$/,
        use: "babel-loader",
      },
    ],
  },
};
```

我们在 `module.rules` 中配置 `loader`，其中最少需要配置两项 `test` 和 `use`，`test` 用来表示对什么文件进行转换，比如上面对所有以 `.js` 结尾的文件进行转换；`use` 用来指定使用什么 `loader` 进行转换，这里指定为 `babel-loader`。综合上面两个配置，表示的意思就是对所有以 `.js` 结尾的文件使用 `babel-loader` 进行处理。

其中 `use` 也可以是一个对象，如下

```javascript
use: {
  loader: "babel-loader",
  options: {

  }
}
```

当 `use` 是一个对象时，我们可以通过 `options` 为 `loader` 配置一些参数。

除了配置 `test` 和 `use`，还可以配置两个选项

| 选项      | 功能           |
| --------- | -------------- |
| `include` | 指定检查的路径 |
| `exclude` | 指定排除的路径 |

上面的两个选项指定只对哪些路径下的文件进行检查，或者不检查哪些路径下的文件，一般来说二者只需要配置一个即可，但是如果两个都配置了，并且有冲突，那么 `exclude` 的优先级高。

我们接着修改 `index.js` 的内容，为其中添加 `ES6` 的语法

```javascript
let x = 1;
let y = 2;
const add = (x, y) => {
  return x + y;
};

let result = add(1, 2);

let p = new Promise((resolve, reject) => {
  resolve(1);
}).then((value) => {
  console.log(value);
});
```

在 `index.js` 中，我们使用了 `ES6` 才出现的语法，如 `let` `const` `Promise` 箭头函数，现在我们运行 `npm run build` 进行打包，部分内容如下

```javascript
eval(
  "let x = 1;\nlet y = 2;\n\nconst add = (x, y) => {\n  return x + y;\n};\n\nlet result = add(1, 2);\nlet p = new Promise((resolve, reject) => {\n  resolve(1);\n}).then(value => {\n  console.log(value);\n});\n\n//# sourceURL=webpack:///./src/index.js?"
);
```

发现并没有将 `ES6` 的语法转化为 `ES5` 的语法，因为 `babel` 需要特定的插件支持，`babel` 为了实现按需加载的功能，它将 `ES6` 转 `ES5` 的功能分为了 `20+` 个插件，你需要哪个就下哪个，但是这样却是有点麻烦，好在 `babel` 推出了一个插件集合 `preset`，我们直接下载 `@babel/preset-env` 即可，里面包含了所有的 `ES6` 语法转 `ES5` 语法的插件合集

```bash
npm install @babel/preset-env --save-dev
```

接着我们在 `webpack.config.js` 中进行配置

```javascript
test: /\.js$/,
use: {
    loader: "babel-loader",
    options: {
        "presets": [
            "@babel/preset-env"
        ]
    },
    exclude: path.resolve(__dirname, "node_modules/") // 对 node_modules 中的文件不进行转换
}
```

但是观察打包后的文件，`let` `const` 和箭头函数都被转为了 `ES5`，但是 `Promise` 并没有被转换，因为这是新增的 `API` 而不是语法，这种新增的 `API` 靠 `preset-env` 是转化不了的，它只能转化语法，所以 `class` 以及 `Object.assign` 等这些新增的 `API` 它是不能转换的，如果在版本比较低的浏览器中，由于不支持这些 `API`，那么就会报错。

这个时候需要 `polyfill` 为浏览器提供 `ES6` 的环境，即使用 `ES5` 或更低的语法去实现上面的 `API`，有两种解决办法。

1. 下载 `@babel/polyfill`，并且在入口文件即 `index.js` 中引入

   ```bash
   npm install @babel/polyfill --save
   ```

   接着在 `index.js` 中引入

   ```javascript
   // 引入 @babel/polyfill
   import "@babel/polyfill";

   let x = 1;
   let y = 2;
   const add = (x, y) => {
     return x + y;
   };

   let result = add(1, 2);

   let p = new Promise((resolve, reject) => {
     resolve(1);
   }).then((value) => {
     console.log(value);
   });
   ```

   `@babel/polyfill` 使用 `ES5` 的语法实现低版本浏览器中没有的 `API`，所以即使在低版本的浏览器中也能够运行上面的程序，但是 `@babel/polyfill` 同时也覆盖了原有的 `API`，另外我们直接导入了 `@babel/polyfill` 中的所有内容，其中有一些 `API` 我们根本没有用到，比如 `class`，这就会导致打包出的文件体积较大。

2. 为了实现按需加载，我们使用另一种解决办法，下载一个插件 `@babel/plugin-transform-runtime`，因为 `@babel/plugin-transform-runtime` 依赖于 `@babel/runtime`，所以我们也要下载 `@babel/runtime`，并且 `@babel/runtime` 生产环境下也要用到，所以不能使用 `--save-dev` 安装

   ```bash
   npm install @babel/plugin-transform-runtime --save-dev
   npm install @babel/runtime
   ```

   接着我们在 `webpack.config.js` 中加入插件的配置

   ```javascript
   test: /\.js$/,
   use: {
       loader: "babel-loader",
       options: {
           "presets": [
               "@babel/preset-env"
           ],
           "plugins": [
               "@babel/plugin-transform-runtime"
           ]
       }
   }
   ```

   接着我们去掉 `index.js` 头部添加的 `import '@babel/polyfill'`，再次使用 `webpack` 进行打包即可。

## css-loader、style-loader

除了可以对 `JavaScript` 进行打包以外，还可以对 `CSS` 进行打包，不过 `webpack` 只能处理 `JavaScript`，即不能把 `CSS` 文件当做入口文件进行打包，不过我们可以在 `JavaScript` 中导入 `CSS`，当对 `JavaScript` 进行打包，也会同时对导入的 `CSS` 进行打包，在 `src` 中新建 `index.css` 和 `foo.css`

```css
/* index.css */
@import "foo.css";
body {
  background-color: #5d4141;
}
```

```css
/* foo.css */
body {
  color: white;
  font-size: 4em;
}
```

我们在 `index.css` 中通过 `@import` 语法引入了 `foo.css`，接着我们在 `index.js` 中引入 `index.css`

```javascript
import "./index.css";

document.write("Hello World");
```

我们通过 `import './index.css'` 引入了 `CSS`，`webpack` 将一切都看作是模块，`CSS` 文件也可以看做是模块，我们就可以通过 `import` 将该模块导入。

我们希望有一个 `loader` 来处理 `import './index.css'` 的语法，使样式生效，这里我们需要两个 `loader`，分别为 `css-loader` 和 `style-loader`，首先进行安装

```bash
npm install css-loader style-loader --save-dev
```

`css-loader` 的作用是处理 `CSS` 文件中 `@import` 和 `url()` 这样的语法的，而 `style-loader` 是将解析完的 `CSS` 样式作为 `style` 标签插入到页面的 `head` 元素中。我们在 `webpack.config.js` 对 `loader` 进行配置

```javascript
test: /\.css$/,
use: ["style-loader", "css-loader"]
```

`loader` 的执行是有先后顺序，`webpack` 会根据 `use` 中定义的 `loader` 从后往前执行，所以我们把 `css-loader` 写在 `style-loader` 后面，这样就会先执行 `css-loader`，然后执行 `style-loader`。

我们运行 `npm run dev`，在 `http://localhost:3000` 可以观察到下面的页面

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/202204221928132022-04-22-19-28-13.png" style="zoom:50%"/>

说明我们的配置成功了。

除了处理 `CSS` 文件，有时候我们会使用 `less` `sass/scss` 编写样式，但是这些文件浏览器不能解析，所以我们需要使用相应的 `loader` 将 `less` `sass/scss` 转为 `CSS` 文件，例如 `less-loader` `sass-loader`

```bash
npm install less less-loader sass sass-loader --save-dev
```

接着我们在 `webpack.config.js` 中添加相应的配置

```javascript
{
    test: /\.less$/,
    use: ["style-loader", "css-loader", "less-loader"]
},
{
    test: /\.(sass|scss)$/,
    use: ["style-loader", "css-loader", "sass-loader"]
}
```

`less-loader` 和 `sass-loader` 只是把要处理的文件转化为 `CSS` 文件，最后我们还是需要通过 `css-loader` 和 `style-loader` 来处理路径以及将 `CSS` 插入到页面中。

## file-loader、url-loader

下面介绍有关处理图片相关的 `loader`，假设在 `index.js` 的中引用了图片

```javascript
let img = new Image();
img.src = "./beauty.jpg";
document.body.appendChild(img);
```

我们进行打包，发现在浏览器中并没有出现我们想要的图片，因为我们在文件中引用的图片是在 `src` 目录下的，在 `dist` 目录下并没有这张图片，所以我们也需要将图片打包到 `dist` 目录下，如下

```javascript
import beauty from "./beauty.jpg";
let img = new Image();
img.src = beauty;
document.body.appendChild(img);
```

我们将图片作为模块导入，希望得到该图片的打包后的地址，然后将 `img.src` 设置为这个地址。这件事情需要一个 `loader` 去做，这里我们使用 `file-loader`，它的作用就是将引入的文件(一般是图片)打包到(或者说移动到) 指定目录下，并返回打包后的文件所在的路径，我们需要安装 `file-loader`

```bash
npm install file-loader --save-dev
```

并在 `webpack.config.js` 中进行配置

```javascript
{
    test: /\.(png|jpe?g|gif|svg)$/,
    use: {
        loader: "file-loader",
        options: {
            name: "[name].[ext]",
            outputPath: "img/"
        }
    }
}
```

其中 `name` 是用来配置输出的文件名的，这里我们使用了两个变量 `[name]` 和 `[ext]`，`[name]` 代表打包前文件的名称，`[ext]` 表示扩展名；`outputPath` 用来指定打包后文件输出的位置，这个位置是相对于 `output.path` 的路径。所以会在 `dist/img` 下打包出一张与原文件名相同的一张图片，并且将该打包后的图片的地址返回，这样在页面中就会出现图片。

有时候对于较小的图片，我们希望将它转为 `base64`，这样可以减少 `http` 请求次数。我们使用 `url-loader` 来做这个事情，它提供了一个 `limit` 选项，该选项指定一个值，当图片的大小小于该值时就转化为 `base64`，当大于该值时，就使用 `file-loader` 进行转换，我们修改 `webpack.config.js` 的配置

```javascript
test: /\.(png|jpe?g|gif|svg)$/,
use: {
    loader: "url-loader",
    options: {
        name: "[name].[ext]",
        limit: 200*1024 // 小于 200KB 就转化为 base64 编码
    }
}
```

## ts-loader

我们在项目中也经常使用 `TypeScript` 来写程序，因为 `TypeScript` 的种种好处，可以帮我们减少 `bug` 产生的几率，但是浏览器也是不认识 `TypeScript`，我们需要将 `TypeScript` 转化为 `JavaScript`，这需要 `ts-loader` 来帮我们做这件事情，首先我们需要下载相关的包

```bash
npm install typescript ts-loader --save-dev
```

接着我们需要配置 `webpack.config.js` 以及 `tsconfig.json`(在项目根目录下新建该文件)

```javascript
test: /\.ts$/,
use: "ts-loader"
```

```json
{
  "compilerOptions": {
    "module": "commonjs",
    "target": "es5",
    "allowJs": true
  },
  "include": ["./src/"],
  "exclude": ["./node_modules/"]
}
```

关于 `tsconfig.json` 的配置不是重点，可以去网上了解相关内容。现在我们就可以自由的写 `TypeScript` 了。
