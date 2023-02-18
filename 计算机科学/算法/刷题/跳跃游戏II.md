---
tags: [动态规划]
---

## 题目介绍

给定一个非负整数数组，你最初位于数组的第一个位置，数组中的每个元素代表你在该位置可以跳跃的最大长度，你的目标是使用最少的跳跃次数到达数组的最后一个位置。

**示例：**

```java
输入: [2,3,1,1,4]
输出: 2
解释: 跳到最后一个位置的最小跳跃数是 2。
	 从下标为 0 跳到下标为 1 的位置，跳 1 步，然后跳 3 步到达数组的最后一个位置。
```

## 解题思路

首先的想法就是使用递归，定义一个递归函数，它的输入有 `index` 和 `counts`，表示经过从起点跳了 `counts` 次到 `index` 处，该函数的输出为从 `index` 处跳转到最后一个位置需要跳转的次数，很容易写出这样的代码

```java
private int length;
public int jump(int[] nums) {
    this.length = nums.length;
    return jump(nums, 0, 0);
}

private int jump(int[] nums, int curIndex, int counts) {
    // 跳过头了
    if (curIndex > this.length - 1) {
        return Integer.MAX_VALUE;
    }

    // 正好跳到最后一格
    if (curIndex == this.length - 1) {
        return counts;
    }

    int min = Integer.MAX_VALUE;
    for (int i = 1; i <= nums[curIndex]; i++) {
        // 从 [curIndex + i] 处开始跳，取最小值
        int t = jump(nums, curIndex + i, counts + 1);
        min = Math.min(t, min);
    }

    return min;
}
```

但是代码一提交，发现超时了。仔细分析，发现有重复的计算，对于这种情况我们可以使用动态规划。我们定义一个数组 `dp`，其中 `dp[i]` 表示从下标 `i` 跳转到最后一个位置需要的最少的跳跃次数。因此我们可以得到如下的更新方程
$$
dp[i] = 
\begin{cases}
1 & i+num[i] \geq length - 1\\
\min\{ dp[i + j] \} + 1 (j\in[1, nums[i]]) &i+num[i] < length - 1
\end{cases}
$$

```java
public int jump(int[] nums) {
        int[] dp = new int[nums.length];
        dp[nums.length - 1] = 0;
        for (int i = nums.length - 2; i >= 0; i--) {
            // nums[i] = 0，永远都跳不到最后一个
            if (nums[i] == 0) {
                dp[i] = Integer.MAX_VALUE;
                continue;
            }
            
            if (nums[i] + i >= nums.length - 1) {
                dp[i] = 1;
            } else {
                dp[i] = Integer.MAX_VALUE;
                for (int j = 1; j <= nums[i]; j++) {
                    // 这里判断是防止溢出
                    if (dp[i + j] != Integer.MAX_VALUE) {
                        dp[i] = Math.min(dp[i], dp[i + j] + 1);
                    }
                }
            }
        }
        return dp[0];
    }
```

## 参考链接

- [跳跃游戏II](https://leetcode-cn.com/problems/jump-game-ii/)
- [详解「DP + 贪心 + 双指针」解法，以及该如何猜 DP 的状态定义 ~](https://leetcode-cn.com/problems/jump-game-ii/solution/xiang-jie-dp-tan-xin-shuang-zhi-zhen-jie-roh4/)

