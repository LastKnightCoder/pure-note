---
tags: [字符串]
---

## 题目描述

给定一个只包含数字的字符串，用以表示一个 IP 地址，返回所有可能从 s 获得的 有效 IP 地址 。你可以按任何顺序返回答案。有效 IP 地址正好由四个整数(每个整数位于 0 到 255 之间组成，且不能含有前导 0)，整数之间用 `'.'` 分隔。例如：`"0.1.2.201"` 和 `"192.168.1.1"` 是有效 IP 地址，但是 `"0.011.255.245"`、`"192.168.1.312"` 和 `"192.168@1.1"` 是无效 IP 地址。

**示例1：**

```java
输入：s = "25525511135"
输出：["255.255.11.135","255.255.111.35"]
```

**示例2：**

```java
输入：s = "0000"
输出：["0.0.0.0"]
```

## 解题思路

定义辅助函数，递归调用辅助函数解决问题。辅助函数接收未处理的字符串以及部分 ip 分组，例如对于一个字符串 `"25525511135"`，那么传入该辅助函数的参数可能为 `("11135", [255, 255])`，当字符串处理完毕时结束递归调用，或者 ip 分组中元素个数大于 `4` 也可停止递归调用。

```java
List<String> results = new ArrayList<>();
public List<String> restoreIpAddresses(String s) {
    // 长度不够，直接返回
    if (s.length() < 4) {
        return results;
    }
    restoreIpAddresses(s, new ArrayList<>());
    return results;
}
/**
 *
 * @param leftString 剩余未处理的字符串
 * @param ip 保存ip地址的各个部分，如 [1, 1, 1] [255, 255, 1, 3]
 */
private void restoreIpAddresses(String leftString, List<String> ip) {
    // ip 超过四个部分，无效
    if (ip.size() > 4) {
        return;
    }
    // 长度不够或者超出，无效
    int leftL = leftString.length();
    if (leftL < (4 - ip.size()) || leftL > (4 - ip.size()) * 3) {
        return;
    }
    // 字符串处理完毕，并且ip中正好有四个部分，取出ip中的元素拼接
    if (ip.size() == 4 && leftL == 0) {
        StringBuilder stringBuilder = new StringBuilder();
        for (int i = 0; i < ip.size(); i++) {
            stringBuilder.append(ip.get(i));
            if (i != ip.size() - 1) {
                stringBuilder.append(("."));
            }
        }
        results.add(stringBuilder.toString());
        return;
    }
    // 有未处理的字符串，递归调用
    // 为了防止 subString 调用异常，需要判断剩余未处理字符串与 3 的大小
    // 这个 3 表示 ip 中每个部分最大的长度
    for (int i = 1; i <= Math.min(3, leftL); i++) {
        String str = leftString.substring(0, i);
        // 如果取出的字符串是无效的，则后面的也是无效的，直接退出循环
        if (!isValidIp(str)) {
            break;
        }
        ip.add(str);
        restoreIpAddresses(leftString.substring(i), ip);
        ip.remove(ip.size() - 1);
    }
}
/**
 * 判断输入的字符串是否为有效的ip组成部分
 * @param s 字符串，如 255 18 7777
 * @return 布尔值，是否是有效ip组成
 */
private boolean isValidIp(String s) {
    // 长度不对，肯定不是
    int l = s.length();
    if (l < 1 || l > 3) {
        return false;
    }
    // 大小范围不对，肯定不是，如 278
    int n = Integer.parseInt(s);
    if (n < 0 || n > 255) {
        return false;
    }
    // 有前置的0，也不是，如 09, 024, 007
    if (l == 2 && n <= 9) {
        return false;
    } else if (l == 3 && n <= 99) {
        return false;
    }
    return true;
}
```

## 参考链接

- [复原IP地址](https://leetcode-cn.com/problems/restore-ip-addresses/)