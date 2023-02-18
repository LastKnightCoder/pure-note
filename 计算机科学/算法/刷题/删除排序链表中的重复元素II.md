---
tags: [链表]
---

## 题目描述

存在一个按升序排列的链表，给你这个链表的头节点 `head `，请你删除链表中所有存在数字重复情况的节点，只保留原始链表中没有重复出现的数字。返回同样按升序排列的结果链表。

**示例1：**

```
输入：1 -> 1 -> 2 -> 3 -> 3 -> 4 -> null
输出：2 -> 4 -> null
```

**示例2：**

```
输入：1 -> 2 -> 2 -> null
输出：1 -> null
```

## 解题思路

### 遍历

我们遍历链表中的所有节点，考虑到对于排序的链表来说，如果一个节点是不重复的，那么它与它后面以及前面的节点的值不相同，这里我们使用一个变量 `duplicated` 来表示当前节点是否与前一个节点相同

```java
public ListNode deleteDuplicates(ListNode head) {
    if (head == null) {
        return head;
    }
    // 返回的根节点的前一个节点
    ListNode root = new ListNode();
    // 用以表示返回链表的末尾
    ListNode cur = root;

    // 遍历链表时的当前节点
    ListNode iterCur = head;
    // 是否与前一个节点重复
    boolean duplicated = false;

    while (iterCur.next != null) {
        // 与后面节点的值不同
        if (iterCur.next.val != iterCur.val) {
            // 如果与前面的节点不重复，则添加到返回的链表
            if (!duplicated) {
                cur.next = new ListNode(iterCur.val);
                cur = cur.next;
            } else {
                // 如果与前面的节点重复，则更新为不重复
                duplicated = false;
            }
        } else {
            // 与后面的节点相同，如果更新为重复
            if (!duplicated) {
                duplicated = true;
            }
        }
        iterCur = iterCur.next;
    }

    // 最后一个节点需要单独判断，因为没有下一个节点，所以只需判断是否与前面节点重复
    if (!duplicated) {
        cur.next = new ListNode(iterCur.val);
    }

    return root.next;
}
```

``` info
这里我写的很短，但是想出使用 `duplicated` 变量保存与前面节点是否重复还是想了很久的。
```

### 递归

使用递归的就非常的简单了，首先判断根节点是否与后面的节点重复(因为根节点前面没有节点，所以只需判断是否与后面的节点重复)，如果根节点不重复，则保留根节点，并且递归调用 `deleteDuplicates(head.next)`。

如果根节点是重复的(即与后面的节点的值相同)，则我们删除所有与根节点相同的节点，找到第一个与根节点不同的节点 `node`，然后递归调用 `deleteDuplicates(node)` 即可

```java
public ListNode deleteDuplicates(ListNode head) {
    if (head == null || head.next == null) {
        return head;
    }

    // 如果不重复，则保留头结点
    if (head.val != head.next.val) {
        head.next = deleteDuplicates(head.next);
        return head;
    }

    // 来到这里就说明重复了，删除与 head 重复的所有节点
    while (head.next != null && head.val == head.next.val) {
        head = head.next;
    }

    return deleteDuplicates(head.next);
}
```

## 参考链接

- [删除排序链表中的重复节点 II](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)
