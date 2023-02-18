---
tags: [Obsidian, 踩坑]
---

在 $\LaTeX$ 中，通过 `\\` 可以进行换行

```latex
x^2 = 1 \\
y^2 = 2
```

但是在 Obsidian 中无效

$$
x^2 = 1 \\
y^2 = 2
$$

要实现换行的效果，应当将表达式包括在 `\begin{align}\end{align}` 之间（aligned 也是可以的），如 

```latex
\begin{align}
x^2 = 1 \\
y^2 = 2
\end{align}
```

$$
\begin{align}
x^2 = 1 \\
y^2 = 2
\end{align}
$$