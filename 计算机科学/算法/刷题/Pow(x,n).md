---
tags: [分治]
---

## 题目描述

实现 `pow(x, n)`，即计算 $x^n$。

**示例1：**

```txt
输入：x = 2.00000, n = 10
输出：1024.00000
```

**示例2：**

```txt
输入：x = 2.10000, n = 3
输出：9.26100
```

**提示：**

- $-100.0 < x < 100.0$
- $-2^{31} \leq n \leq 2^{31} - 1$
- $-10^4 \leq x^n \leq 10^4$

## 题目解析

$x^n$ 的计算分为以下两种情况
$$
x^n = 
\begin{cases}
x^{|n|}, & n > 0 \\
\dfrac{1}{x^{|n|}}, & n < 0
\end{cases}
$$
对 $x = 0$ 时需要单独考虑，否则当 $n < 0$，得到的结果为无穷大，我们认为 $0^n = 0$，所以可以写出这样的代码

```java
public double myPow(double x, int n) {
    if (x == 0) {
        return 0;
    }
    double res = myPowWithPositiveExp(x, Math.abs(n));
    return n > 0 ? res : 1 / res;
}
```

上面 `myPowWithPositiveExp` 方法是计算 $x^{|n|}$ 的方法，它只处理指数为正的情况。很快我们可以写出这样的代码

```java
private double MyPowWithPositiveExp(double x, int n) {
    if (n == 0) {
        return 1;
    }

    double res = 1;
    for (int i = 0; i < n; i++) {
        res *= x;
    }

    return res;
}
```

这样做行不行，当然可以。不过我们有更快的方法，重新考虑  $x^n(n>0)$，我们可以划分为
$$
x^{n} = 
\begin{cases}
x^{n/2} * x^{n/2}, & n \% 2 == 0 \\
x * x^{(n-1)/2} * x^{(n-1)/2}, & n \% 2 == 1
\end{cases}
$$
我们把求解  $x^n$ 转化为了求解 $x^{n/2}$，而 $x^{n/2}$ 又可以转化为求解 $x^{n/4}$，大大的减少了计算，所以我们很快可以写出这样的代码

```java
private double myPowWithPositiveExp(double x, int n) {
    if (n == 0) {
        return 1;
    }
    // 当 n 为奇数时 n/2 = (n - 1)/2
    double res = myPowWithPositiveExp(x, n/2);
    if (n % 2 == 0) {
        return res *res;
    } else {
        return x * res * res;
    }
}
```

## 参考链接

-  [Pow(x, n)](https://leetcode-cn.com/problems/powx-n/)

