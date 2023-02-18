---
tags: [模块, JavaScript, ESModule]
---


```note
模块只通过 HTTP(s) 工作，而非本地。

如果你尝试通过 `file://` 协议在本地打开一个网页，你会发现 `import/export` 指令不起作用。
```

## 模块核心功能：

- 始终使用 `use strict`

  模块始终在严格模式下运行。例如，对一个未声明的变量赋值将产生错误。

  ```html
  <script type="module"> 
    a = 5; // error 
  </script>
  ```
  
- 模块级作用域
	
	在浏览器中，对于 HTML 页面，每个 `<script type="module">` 都存在独立的顶级作用域。
	
- 模块代码仅在第一次导入时被解析
	- 顶层模块代码应该用于初始化，创建模块特定的内部数据结构
	- 如果我们需要多次调用某些东西 —— 我们应该将其以函数的形式导出

	```js
  // 📁 1.js 
  import { admin } from './admin.js'; 
  admin.name = "Pete"; 
  // 📁 2.js 
  import { admin } from './admin.js'; 
  alert(admin.name); // Pete
  // 1.js 和 2.js 引用的是同一个 admin 对象 
  // 在 1.js 中对对象做的更改，在 2.js 中也是可见的_
  ```
  因为该模块只执行了一次。生成导出，然后这些导出在导入之间共享

- `import.meta`

  `import.meta` 对象包含关于当前模块的信息

  - 在浏览器环境中，它包含当前脚本的 URL
  - 如果它是在 HTML 中的话，则包含当前页面的 URL

- 在模块中，`this` 是 `undefined`

  ```html
  <script> 
    alert(this); // window 
  </script> 
  <script type="module"> 
    alert(this); // undefined 
  </script>
  ```

## 浏览器特定功能

- 延迟加载，与 `defer` 的影响相同
  - 不会阻塞 HTML 的处理，它们会与其他资源并行加载
  - 模块脚本会等到 HTML 文档完全准备就绪，然后才会运行。
  - 保持脚本的相对顺序：在文档中排在前面的脚本先执行
  
- Async 适用于内联脚本（inline script）
  ```html
  <!-- 所有依赖都获取完成（analytics.js）然后脚本开始运行 --> 
  <!-- 不会等待 HTML 文档或者其他 <script> 标签 --> 
  <script async type="module"> 
    import {counter} from './analytics.js';
    counter.count();
   </script>
  ```

- 外部脚本

  1. 具有相同 `src` 的外部脚本仅运行一次

    ```html
    <!-- 脚本 my.js 被加载完成（fetched）并只被运行一次 -->
    <script type="module" src="my.js"></script>
    <script type="module" src="my.js"></script>
    ```

  2. 如果一个模块脚本是从另一个源获取的，则远程服务器必须提供表示允许获取的响应头 `Access-Control-Allow-Origin`

- 不允许裸模块

  在浏览器中，`import` 必须给出相对或绝对的 URL 路径。没有任何路径的模块被称为“裸（bare）”模块。

  ```js
  import {sayHi} from 'sayHi'; // Error，“裸”模块
  // 模块必须有一个路径，例如 './sayHi.js' 或者其他任何路径
  ```

  ```note
  某些环境，像 Node.js 或者打包工具（bundle tool）允许没有任何路径的裸模块，因为它们有自己的查找模块的方法和钩子（hook）来对它们进行微调。但是浏览器尚不支持裸模块。
  ```

- nomodule

  旧版本会忽略 `type="module"`，使用 `nomodule` 做为后备

  ```html
  <script nomodule>
    alert("Modern browsers know both type=module and nomodule, so skip this")
    alert("Old browsers ignore script with unknown type=module, but execute this.");
  </script>
  ```

## 总结

1. 一个模块就是一个文件。浏览器需要使用 `<script type="module">` 以使 `import/export` 可以工作。与一般脚本有以下几点不同
   - 默认是延迟解析的（deferred）。
   - Async 可用于内联脚本。
   - 要从另一个源（域/协议/端口）加载外部脚本，需要 CORS header。
   - 重复的外部脚本会被忽略
2. 模块具有自己的本地顶级作用域
3. 模块始终使用 `use strict`
4. 模块代码只执行一次。导出仅创建一次，然后会在导入之间共享
