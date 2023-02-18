---
tags: [Obsidian, 插件开发]
---

## 动机

Obsidian 有一个 `Admonition` 提供带样式的 `callout` 容器，但是我觉得我的 VuePress 主题上的 Container 比较好看，所以想将其迁移过来。

## 实现

实现其实没啥说的，就是打开开发者工具，一点点将样式抄过来，效果如下：

```tip
This is Tip!
```

```info
This is Info!
```

```note
This is Note!
```

```warning
This is Warning!
```

```danger
This is danger!
```

