---
tags: [二分查找, 牛顿法]
---

## 题目描述

实现 `int sqrt(int x)` 函数。计算并返回 `x` 的平方根，其中 `x` 是非负整数。由于返回类型是整数，结果只保留整数的部分，小数部分将被舍去。

**示例1：**

```
输入: 4
输出: 2
```

**示例2：**

```
输入: 8
输出: 2
```

## 解题思路

碰到这种问题，第一个想法是从 1 开始试，直到某个整数的平方小于等于 `x`，并且其下一个数的平方大于 `x`，这个时候我们可以说找到了。

我们发现我们是在有序的空间进行查找 `1~ x/2`，对于有序查找问题我们使用二分查找法，时间复杂度可以降低到 $O(\log n)$。

```java
public int mySqrt(int x) {
    int start = 0;
    int end = x;
    while (start < end) {
        long mid = start + (end - start) / 2;
        if (mid * mid <= x  && x / (mid + 1) < (mid + 1)) {
            break;
        } else if (mid * mid < x) {
            start = (int)++mid;
        } else {
            end = (int)--mid;
        }
    }
    return start + (end - start) / 2;
}
```

上面有个地方需要注意，我们使用了 `x / (mid + 1) < mid + 1` 而不是 `(mid + 1) * (mid + 1) > x`，这是因为 `(mid + 1) * (mid + 1)` 的结果可能会溢出，导致不正确的判断，所以这里我们使用了 `x / (mid + 1) < (mid + 1)` 的形式。

除了可以使用二分搜索法，还可以使用牛顿迭代法，构建下面的方程

$$
f(x) = x^2 - a
$$

上面方程的解就是 $\sqrt{a}$，现在我们使用牛顿迭代法求解上面的方程。牛顿迭代法的公式是

$$
x_{n + 1} = x_{n} - \frac{f(x_n)}{f^{'}(x_n)}
$$

所以迭代公式为

$$
x_{n + 1} = \frac{1}{2} (x_n + \frac{a}{x_n})
$$

当迭代误差小于 $10^{-5}$，我们就可以将迭代停止了

```java
public int mySqrt(int a) {
    // 起始迭代点
    double x = 0.1;
    // 上一个迭代结果
    double pre = 0;
    // 当误差大于 1e-5 时，继续迭代
    while (Math.abs(x - pre) > 1e-5) {
        pre = x;
        x = 0.5 * (x + a/x);
    }

    // 返回整数部分
    return (int)x;
}
```

牛顿迭代法的收敛速度是平方收敛的，其收敛速度比二分搜索法快。

## 参考链接

- [x的平方根](https://leetcode-cn.com/problems/sqrtx/)

