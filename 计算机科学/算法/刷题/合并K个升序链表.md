---
tags: [链表, 分治]
---

## 题目描述

给你一个链表数组，每个链表都已经按升序排列。请你将所有链表合并到一个升序链表中，返回合并后的链表。

**示例1：**

```java
输入：lists = [[1,4,5],[1,3,4],[2,6]]
输出：[1,1,2,3,4,4,5,6]
解释：链表数组如下：
[
  1->4->5,
  1->3->4,
  2->6
]
将它们合并到一个有序链表中得到。
1->1->2->3->4->4->5->6
```

**示例2:**

```java
输入：lists = []
输出：[]
```

## 解题思路

我一开始的解题思路就是将两个链表一一合并，思路如下：

- `list1 + list2 -> list`
- `list3 + list -> list`
- `list4 + list -> list`
- `...`

所以第一版的代码如下

```java
public ListNode mergeKLists(ListNode[] lists) {
    ListNode root = null;
    if (lists.length == 0) {
        return root;
    }
    root = Arrays.stream(lists).reduce(null, this::mergeTwoList);
    return root;
}

// 合并两个链表
private ListNode mergeTwoList(ListNode l1, ListNode l2) {
    if (l1 == null) {
        return l2;
    }
    if (l2 == null) {
        return l1;
    }
    ListNode p1 = l1;
    ListNode p2 = l2;
    ListNode dummyNode = new ListNode(0);
    ListNode cuerNode = dummyNode;
    while (p1 != null && p2 != null) {
        if (p1.val <= p2.val) {
            cuerNode.next = p1;
            cuerNode = cuerNode.next;
            p1 = p1.next;
        } else {
            cuerNode.next = p2;
            cuerNode = cuerNode.next;
            p2 = p2.next;
        }
    }
    if (p1 == null) {
        cuerNode.next = p2;
    } else {
        cuerNode.next = p1;
    }
    return dummyNode.next;
}
```

然后一提交，发现只超过了 24% 的人

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210326112949.png" style="zoom:50%;" />

说明肯定有更快的算法，我便去找题解，在题解中发现发现使用分治的思路可以更快的得到排序好的数组。由于老弟我归并排序已经写得炉火纯青了，很快得到了第二版的代码

```java
public ListNode mergeKLists(ListNode[] lists) {
    if (lists.length == 0) {
        return null;
    }
    return mergeKLists(lists, 0 , lists.length - 1);
}
private ListNode mergeKLists(ListNode[] lists, int left, int right) {
    if (left == right) {
        return lists[left];
    }
    ListNode leftList = null;
    ListNode rightList = null;
    int mid = left + (right - left) / 2;
    leftList = mergeKLists(lists, left, mid);
    rightList = mergeKLists(lists, mid + 1, right);
    return mergeTwoList(leftList, rightList);
}

// 合并两个链表的代码同上，这里便不列出
```

再次提交

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting2/20210326113204.png" style="zoom:50%;" />

已经打败 87% 的人了，时间从 120ms 降到了 2ms，再次感受到了算法的魅力。

在最后我稍微分析归并的方法为什么比一个个合并较快，从合并的次数看，一个个合并需要合并 $n - 1$ 次，而归并排序需要合并
$$
\dfrac{n}{2} + \dfrac{n}{4} + \dfrac{n}{8} + \cdots + 1
$$
为了方便计算，不妨假设 $n = 2^i$，即 $n$ 为 $2$ 的整数次幂，则上式可以改为
$$
\sum_{i = 1}^{\log_2 n} \frac{n}{2^i} \approx n - 1
$$
所以从合并的次数上看，几乎是相当的。但是注意到一一合并得到的链表越来越长

- `list + listi -> list`
- `list + list(i + 1) -> list`
- `...`

链表 `list` 越来越长，合并需要的时间就越多。但是对于归并合并，它先将链表数组划分为两个一组然后合并，这两个一组的链表都是较短的，所需合并的速度较快。所以虽然他们合并的次数相差不大，但是归并合并的链表较一一合并短很多，所以便会较快。

## 参考链接

- [合并K个升序链表](https://leetcode-cn.com/problems/merge-k-sorted-lists/)

