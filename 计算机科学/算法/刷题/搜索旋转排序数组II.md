---
tags: [二分搜索]
---

## 题目描述

已知存在一个按非降序排列的整数数组 `nums` ，数组中的值不必互不相同。

在传递给函数之前，`nums `在预先未知的某个下标 `k(0 <= k < nums.length)`上进行了 旋转 ，使数组变为 `[nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]`（下标 从 0 开始 计数）。例如， `[0,1,2,4,4,4,5,6,6,7]` 在下标 5 处经旋转后可能变为 `[4,5,6,6,7,0,1,2,4,4]` 。

给你 旋转后 的数组 `nums` 和一个整数 `target` ，请你编写一个函数来判断给定的目标值是否存在于数组中。如果 `nums` 中存在这个目标值 `target` ，则返回 `true` ，否则返回 `false` 。

示例1：

```
输入：nums = [2,5,6,0,0,1,2], target = 0
输出：true
```

示例2：

```
输入：nums = [2,5,6,0,0,1,2], target = 3
输出：false
```

## 解题思路

旋转数组的概念上面介绍的很清晰了，因为数组原来是有序的，所以我们本能的会想到使用二分搜索法去进行搜索，使用二分搜索法可以直接消灭掉一半的搜索范围，但是这个数组经过旋转后，它变得不是有序的了，这为我们使用二分搜索法带来了不便。

为了使用二分搜索法，我们来看旋转数组的一个性质，随意的将旋转数组分为两部分，其中一部分必然是有序的

<img src="https://user-images.githubusercontent.com/29890094/122768280-07c33500-d2d6-11eb-8af1-920a74be40b6.png" style="zoom: 50%;" />

 我们只需要判断 `target` 是否在有序的那一部分中即可，如果 `target` 在有序的那一部分中，那么我们就可以扔掉无序的那一部分，如果不在有序的那一部分，则我们就扔掉有序的部分，在无序的部分中寻找，无论怎样我们都可以消灭一般的搜索范围

<img src="https://user-images.githubusercontent.com/29890094/122769607-45748d80-d2d7-11eb-949a-c85cad057448.png" style="zoom: 50%;" />

所以现在的关键是如何寻找到哪一部分有序，我们比较划分的那个点 `mid` 与 `start` 的大小

- `nums[mid] > nums[start]`：左边有序
- `nums[mid] < nums[start]`：右边有序

<img src="https://user-images.githubusercontent.com/29890094/122770650-3e01b400-d2d8-11eb-99a7-689abe9c869f.png" alt="3" style="zoom: 50%;" />

这里你可能会说，`nums[mid] == nums[start]` 的情况呢? 这个时候其实我们不能判断那边是有序的

<img src="https://user-images.githubusercontent.com/29890094/122771385-f596c600-d2d8-11eb-9fcb-881045a036ba.png" alt="4" style="zoom:50%;" />

这个时候我们就不能使用二分搜索了，只能暴力搜索了。

```java
public boolean search(int[] nums, int target) {
    int start = 0;
    int end = nums.length - 1;

    while (start <= end) {
        int mid = start + (end - start) / 2;
        if (nums[mid] == target) {
            return true;
        }

        if (nums[start] == nums[mid]) {
            start++;
        }

        if (nums[mid] > nums[start]) {
            if (target >= nums[start] && target < nums[mid]) {
                end = mid - 1;
            } else {
                start = mid + 1;
            }
        } else {
            if (target > nums[mid] && target <= nums[end]) {
                start = mid + 1;
            } else {
                end = mid - 1;
            }
        }
    }

    return false;
}
```

## 参考链接

- [搜索旋转排序数组 II](https://leetcode-cn.com/problems/search-in-rotated-sorted-array-ii/)
- [搜索旋转排序数组II 题解](https://leetcode-cn.com/problems/search-in-rotated-sorted-array-ii/solution/zai-javazhong-ji-bai-liao-100de-yong-hu-by-reedfan/)

