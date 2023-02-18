---
tags: [数据结构/Trie]
---

`Trie` 树又称为字典树、前缀树。如果我们使用一般树结构去查询一个数据集里的单词，它的复杂度是 `O(log n)`，但是如果我们使用 `Trie` 去查询单词的话，查询的复杂度只与单词的长度有关，与数据的规模无关。比如对于一个 $2^{20}$ 规模的数据集，我们去查一个单词 `"word"`，一般树的复杂度为 `O(20)`，而 `Trie` 树的复杂度为 `O(4)`，其中 4 是单词的长度，所以 `Trie` 树是一种很高效的查询字符串的树结构。

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/202204231243012022-04-23-12-43-01.png" style="zoom:50%"/>


```java
class Node {
    char c;
    Node next[26];
}
```

但是这样考虑忽略了大小写，并且没有考虑一些特殊的字符，如 `@` 等符号或标点符号。所以我们每个节点不再是静态的指向 26 个节点，而是动态的指向若干个节点

```java
class Node {
    char c;
    Map<Character,Node> next;
}
```

另外我们通过某个字符来到一个节点，可以通过 `Map`已经知道了，所以我们不必存储这个字符

```java
class Node {
    Map<Character,Node> next;
}
```

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/202204231243202022-04-23-12-43-21.png" style="zoom:50%"/>

另外通过叶子节点是无法区别单词的结尾的，因为有的单词可能为某个单词的前缀，如 `"pan"` 为 `"panda"` 的前缀，所以我们要增加一个变量 `isWord` 来表示是否是单词的结尾

```java
import java.util.TreeMap;

public class Trie {
    private class Node {
        public boolean isWord;
        public TreeMap<Character,Node> next;

        public Node(boolean isWord) {
            this.isWord = isWord;
            next = new TreeMap<>();
        }

        public Node() {
            this(false);
        }
    }

    private Node root;
    private int size;

    public Trie() {
        root = new Node();
        size = 0;
    }

    public int getSize() {
        return size;
    }
}
```

## 实现

### 添加单词

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/202204231243432022-04-23-12-43-44.png" style="zoom:50%"/>

```java
public void add(String word) {
    Node cur = root;
    for (int i = 0; i < word.length(); i++) {
        char c = word.charAt(i);
        //判断是否有指向这个字符的节点
        if (cur.next.get(c) == null) {
            //没有则新建一个节点
            cur.next.put(c, new Node());
        }
        //移动到这个节点
        cur = cur.next.get(c);
    }
    //遍历完毕，判断这个节点是否被标记为单词的结尾 如果没有则标记并且维护size++
    if (!cur.isWord) {
        cur.isWord = true;
        size++;
    }
}
```

### 查询单词

查询单词的逻辑与添加单词的逻辑高度重复，如果在查询过程中遇到没有指向该字符的节点，则直接返回 `false`，如果遍历完毕都没有发生上面的情况，则判断该节点是否被标记为单词的结尾，如果没有则返回 `false`，否则返回 `true`。

```java
public boolean contains(String word) {
    Node cur = root;
    for (int i = 0; i < word.length(); i++) {
        char c = word.charAt(i);
        if (cur.next.get(c) == null) {
            return false;
        }
        cur = cur.next.get(c);
    }

    return cur.isWord;
}
```

### 前缀搜索

查询是否包含某个前缀，与 `contains()` 方法几乎一样，不过最后不用判断是否是单词结尾，直接返回 `true`

```java
public boolean isPrefix(String prefix) {
    Node cur = root;
    for (int i = 0; i < prefix.length(); i++) {
        char c = prefix.charAt(i);
        if (cur.next.get(c) == null) {
            return false;
        }
        cur = cur.next.get(c);
    }
    return true;
}
```

## 简单字符匹配

对于字符串中的字符 `.` 规定它可以匹配任意的字符，那么这样的一个匹配算法如何写，如果我们遇到的字符不是 `.` 的话，逻辑和上面一样，如果遇到的是`.`的话，我们就要去搜索该节点中所有的分叉(子树)

```java
public boolean match(String word) {
    return match(root, word, 0);
}
private boolean match(Node node, String word, int index) {
    //递归终止条件
    if (index == word.length()) {
        return node.isWord;
    }

    char c = word.charAt(index);
    //如果不是.
    if (c != '.') {
        //没有指向该字符的节点 返回false
        if (node.next.get(c) == null) {
            return false;
        } else {
            //否则继续匹配
            return match(node.next.get(c), word, index + 1);
        }
    } else {
        //如果是. 去该节点的所有分叉中搜索
        for (char nextChar : node.next.keySet()) {
            //如果有任一个分叉匹配到了，则返回true
            if (match(node.next.get(nextChar), word, index + 1)) {
                return true;
            }
        }
        //说明上面的没有一个匹配成功了，返回fasle
        return false;
    }
}
```

## 全部代码

```java
import java.util.TreeMap;

public class Trie {
    private class Node {
        public boolean isWord;
        public TreeMap<Character,Node> next;

        public Node(boolean isWord) {
            this.isWord = isWord;
            next = new TreeMap<>();
        }

        public Node() {
            this(false);
        }
    }

    private Node root;
    private int size;

    public Trie() {
        root = new Node();
        size = 0;
    }

    public int getSize() {
        return size;
    }

    public void add(String word) {
        Node cur = root;
        for (int i = 0; i < word.length(); i++) {
            char c = word.charAt(i);
            if (cur.next.get(c) == null) {
                cur.next.put(c, new Node());
            }
            cur = cur.next.get(c);
        }
        if (!cur.isWord) {
            cur.isWord = true;
            size++;
        }
    }

    public boolean contains(String word) {
        Node cur = root;
        for (int i = 0; i < word.length(); i++) {
            char c = word.charAt(i);
            if (cur.next.get(c) == null) {
                return false;
            }
            cur = cur.next.get(c);
        }

        return cur.isWord;
    }

    public boolean isPrefix(String prefix) {
        Node cur = root;
        for (int i = 0; i < prefix.length(); i++) {
            char c = prefix.charAt(i);
            if (cur.next.get(c) == null) {
                return false;
            }
            cur = cur.next.get(c);
        }
        return true;
    }

    public boolean match(String word) {
        return match(root, word, 0);
    }
    private boolean match(Node node, String word, int index) {
        //递归终止条件
        if (index == word.length()) {
            return node.isWord;
        }

        char c = word.charAt(index);
        if (c != '.') {
            if (node.next.get(c) == null) {
                return false;
            } else {
                return match(node.next.get(c), word, index + 1);
            }
        } else {
            for (char nextChar : node.next.keySet()) {
                if (match(node.next.get(nextChar), word, index + 1)) {
                    return true;
                }
            }
            //说明上面的没有一个匹配成功了
            return false;
        }
    }
}
```


