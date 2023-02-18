---
tags: [React, JSX, SVG]
---

在 `JSX` 里面使用 SVG，使用了 `xlink:href`，直接报错

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/202204212202002022-04-21-22-02-00.png" style="zoom:50%"/>

解决办法

```js
xlink:href -> xlinkHref
```

参考链接：

- [Rendering Namespace Tags/Attributes Like xmlns:inkscape In React](https://heymanishjain.medium.com/rendering-namespace-attributes-like-xmlns-inkscape-in-react-54d901ae6642#:~:text=Namespace%20tags%20are%20not%20supported,false%60%20to%20bypass%20this%20warning.&text=Adding%20the%20namespace%20tags%20the,xlink%20you%20can%20use%20xmlnsXlink.)