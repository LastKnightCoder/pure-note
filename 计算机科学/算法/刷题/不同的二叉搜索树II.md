---
tags: [分治, 树]
---

## 题目描述

给你一个整数 `n` ，请你生成并返回所有由 `n` 个节点组成且节点值从 `1` 到 `n` 互不相同的不同二叉搜索树。可以按任意顺序返回答案。

示例：

```java
输入：n = 3
输出：[[1,null,2,null,3],[1,null,3,2],[2,1,3],[3,1,null,null,2],[3,2,null,1]]
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3/不同的二叉搜索树.drawio2021-09-26-11-19-56.png" style="zoom:50%"/>

`TreeNode` 的定义如下

```java
public class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    TreeNode() {
    }
    TreeNode(int val) {
        this.val = val;
    }
    TreeNode(int val, TreeNode left, TreeNode right) {
        this.val = val;
        this.left = left;
        this.right = right;
    }
}
```

## 解题思路

对于 $[1, n]$ 这 $n$ 个数，我们分别以每个数为根节点来形成二叉树，因为根节点不同，得到的二叉树就不会是重复的。对于以数 $i$ 为根节点，可以将数分为两部分，$[0, i -1]$ 这些数组成的二叉树(不止一种)作为它的左子树，而 $[i + 1, n]$ 这些数组成的二叉树(不止一种)作为它的右子树。

那么这些左右子树怎么求呢? 我们还是借助于同样的思想，对于 $[0, i - 1]$ 我们依然以每个数作为根节点，将数划分为两部分，对于 $[i + 1, n]$ 也是同样，具体实现如下


```java
public List<TreeNode> generateTrees(int n) {
    return generateTrees(1, n);
}
private List<TreeNode> generateTrees(int left, int right) {
    List<TreeNode> list = new ArrayList<>();
    if (left > right) {
        // 这里添加 null，是为了保证返回的 List 长度不为 0，在后面遍历的时候不会退出循环
        list.add(null);
        return list;
    }
    if (left == right) {
        list.add(new TreeNode(left));
        return list;
    }
    // 以每个数作为根节点
    for (int i = left; i <= right; i++) {
        // 划分为两部分，这两部分的求法递归调用求解 
        List<TreeNode> leftTreeRoot = generateTrees(left, i - 1);
        List<TreeNode> rightTreeRoot = generateTrees(i + 1, right);
        // 组合左右子树的所有可能
        for (int m = 0; m < leftTreeRoot.size(); m++) {
            for (int n = 0; n < rightTreeRoot.size(); n++) {
                TreeNode root = new TreeNode(i, leftTreeRoot.get(m), rightTreeRoot.get(n));
                list.add(root);
            }
        }
    }
    return list;
}
```

在上面我们定义了一个辅助函数 `generateTrees(int left, int right)`，该函数的作用是返回 `[left, right]` 这些数能够组成的二叉搜索树，因此原问题就相当于 `generateTrees(1, n)`，该函数的实现使用了递归的方法，以 `[left, right]` 中的每个数分别作为根节点，将数划分为两部分，分别作为该根节点的左右子树，而这两个左右子树的求法便是递归调用该函数，接着将所有的左右子树进行组合，形成所有可能的二叉树

```java {3-4}
for (int i = left; i <= right; i++) {
    // 递归调用求解左右子树
    List<TreeNode> leftTreeRoot = generateTrees(left, i - 1);
    List<TreeNode> rightTreeRoot = generateTrees(i + 1, right);
    for (int m = 0; m < leftTreeRoot.size(); m++) {
        for (int n = 0; n < rightTreeRoot.size(); n++) {
            TreeNode root = new TreeNode(i, leftTreeRoot.get(m), rightTreeRoot.get(n));
            list.add(root);
        }
    }
}
```

该函数终止的条件就是 `left > right` 或者  `left == right`，需要**注意**的是，当 `left > right` 时，我们返回了一个包含 `null` 的 `List`，这是因为要保证返回的  `List` 的长度不为能 0，否则在使用 `for` 循环遍历时，无法组合两个子树

```java {2}
// 如果左子树或右子树的长度为 0，将会导致循环立即退出，无法进行组合
for (int m = 0; m < leftTreeRoot.size(); m++) {
    for (int n = 0; n < rightTreeRoot.size(); n++) {
        TreeNode root = new TreeNode(i, leftTreeRoot.get(m), rightTreeRoot.get(n));
        list.add(root);
    }
}
```

例如左子树长度为 `0`，但是右子树长度不为 `0`，此时是能够形成二叉树的，但是因为左子树长度为 `0`，导致 `for` 循环立即退出，无法进行组合形成二叉树，所以我们要保证返回的 `List` 长度不能为 `0`，我们添加一个 `null` 进行组合。

## 参考链接

- [不同的二叉搜索树II](https://leetcode-cn.com/problems/unique-binary-search-trees-ii/)