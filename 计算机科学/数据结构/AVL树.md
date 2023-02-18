---
tags: [数据结构/AVL]
---

## 概念及实现

我们在研究二分搜索树时发现，如果我们将数据顺序添加进树中时，它有可能会退化成一个链表，即所有的元素都添加到一个孩子上，这样树结构的优势就体现不出来，为了不使左右孩子的高度相差太大，我们需要对树进行调整，使树达到平衡，成为一棵平衡二叉树，`AVL` 就是一种经典的平衡二叉树

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/202204231251392022-04-23-12-51-40.png" style="zoom:50%"/>


在 `AVL` 中，我们定义的平衡二叉树为：对于任意一个节点，左子树和右子树的高度相差不能超过 `1`。

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/202204231252062022-04-23-12-52-06.png" style="zoom:50%"/>

我们为每一个节点标注好高度值，计算方法为取左右子树高度较高的高度，然后 `+1`

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/202204231252282022-04-23-12-52-28.png" style="zoom:50%"/>

然后我们还有记录节点左右子树的高度差，我们称之为平衡因子(规定用左子树的高度-右子树的高度)

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/202204231252422022-04-23-12-52-43.png" style="zoom:50%"/>

由于我们只是在添加元素和删除元素时对树进行调整，其余的代码同二分搜索树是相同的，所以就不贴出所有的代码，只给出不同的代码，首先我们需要在 `Node` 类中添加一个 `height` 变量来记录高度

```java
private class Node {
    public E e;
    public Node left;
    public Node right;
    //高度
    public int height;
    public Node(E e) {
        this.e = e;
        left = null;
        right = null;
        //高度初始为1
        height = 1;
    }
    @Override
    public String toString() {
        return e.toString();
    }
}
```

新增加一个获得某节点高度的函数和平衡因子的函数

```java
private int getHeight(Node node) {
    if (node == null) {
        return 0;
    }
    return node.height;
}
private int getBalanceFactor(Node node) {
    if (node == null) {
        return 0;
    }
    
    return getHeight(node.left) - getHeight(node.right);
}
```

有了这些因素，我们一般需要在添加元素时进行维护，重新计算高度和平衡因子，从而进行调整

```java
private Node add(Node node, E e) {
    if (node == null) {
        size++;
        return new Node(e);
    }
    if (e.compareTo(node.e) < 0) {
        node.left = add(node.left, e);
    } else if (e.compareTo(node.e) > 0) {
        node.right = add(node.right,e);
    }
    
    //更新高度
    node.height = Math.max(getHeight(node.left),getHeight(node.right)) + 1;
    //计算平衡因子
    int balanceFactor = getBalanceFactor(node);
    if (Math.abs(balanceFactor) > 1) {
        //进行调整
    }
    return node;
}
```

我们后面的内容主要是如何调整，后面所以只给出如何调整的代码，在学如何调整之前，我们来写两个辅助函数来判断这棵树是不是二分搜索树和 `AVL` 树，因为如果我们的代码有问题的话，有可能破坏二分搜索树的性质，这样有利于我们检查，那怎么检查一棵树是不是二分搜索树，我们根据二分搜索树的性质，它的中序遍历的结果是从小到大的特性，我们重写中序遍历为

```java
public boolean isBST() {
    ArrayList<E> arrayList = new ArrayList<>();
    inOrder(root, arrayList);
    
    for (int i = 1; i < arrayList.size(); i++) {
        if (arrayList.get(i-1).compareTo(arrayList.get(i)) > 0) 
            return false;
        }
    }
    return true;
}
private void inOrder(Node node, ArrayList<E> arrayList) {
    if (node == null) {
        return;
    }
    
    inOrder(node.left, arrayList);
    arrayList.add(node.e);
    inOrder(node.right, arrayList);
}
```

现在我们判断这棵树是不是平衡二叉树

```java
public boolean isBalanced() {
    return isBalanced(root);
}
//判断某个节点是不是平衡
private boolean isBalanced(Node node) {
    if (node == null) {
        return true;
    }
    int balanceFactor = getBalanceFactor(node);
    if (Math.abs(balanceFactor) > 1) {
        return false;
    }
    return isBalanced(node.left) && isBalanced(node.right);
}
```

下面对不平衡的四种情形进行讨论，并给出调整方法

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/202204231253082022-04-23-12-53-09.png" style="zoom:50%"/>

```java
// 对节点y进行向右旋转操作，返回旋转后新的根节点x
//        y                              x
//       / \                           /   \
//      x   T4     向右旋转 (y)        z     y
//     / \       - - - - - - - ->    / \   / \
//    z   T3                       T1  T2 T3 T4
//   / \
// T1   T2
private Node rightRotate(Node y) {
    Node x = y.left;
    Node T3 = x.right;
    x.right = y;
    y.left = T3;
    
    //更新x和y的高度值 先更新y的，因为y是x的右孩子，x的更新取决于y
    y.height = Math.max(getHeight(y.left), getHeight(y.right)) + 1;
    x.height = Math.max(getHeight(x.left), getHeight(x.right)) + 1;
    
    return x;
}
// 对节点y进行向左旋转操作，返回旋转后新的根节点x
//    y                             x
//  /  \                          /   \
// T4   x      向左旋转 (y)       y     z
//     / \   - - - - - - - ->   / \   / \
//   T3  z                     T4 T3 T1 T2
//      / \
//     T1 T2
private Node leftRotate(Node y) {
    Node x = y.right;
    Node T3 = x.left;
    x.left = y;
    y.right = T3;
    
    y.height = Math.max(getHeight(y.left), getHeight(y.right)) + 1;
    x.height = Math.max(getHeight(x.left), getHeight(x.right)) + 1;
    
    return x;
}

public void add(E e) {
    root = add(root, e);
}
private Node add(Node node, E e) {
    if (node == null) {
        size++;
        return new Node(e);
    }
    if (e.compareTo(node.e) < 0) {
        node.left = add(node.left, e);
    } else if (e.compareTo(node.e) > 0) {
        node.right = add(node.right,e);
    }
    //更新高度
    node.height = Math.max(getHeight(node.left),getHeight(node.right)) + 1;
    //计算平衡因子
    int balanceFactor = getBalanceFactor(node);
    //调整
    if (balanceFactor > 1 && getBalanceFactor(node.left) >= 0) {
        return rightRotate(node);
    }
    if (balanceFactor < -1 && getBalanceFactor(node.right) <= 0) {
        return leftRotate(node);
    }
    if (balanceFactor > 1 && getBalanceFactor(node.left) < 0) {
        node.left = leftRotate(node.left);
        return rightRotate(node);
    }
    if (balanceFactor < -1 && getBalanceFactor(node.right) > 0) {
        node.right = rightRotate(node.right);
        return leftRotate(node);
    }
    return node;
}
public void remove(E e) {
    root = remove(root, e);
}
private Node remove(Node node, E e) {
    if (node == null) {
        return null;
    }
    Node retNode;
    if (e.equals(node.e)) {
        if (node.right == null) {
            Node leftNode = node.left;
            node.left = null;
            size--;
            retNode = leftNode;
        } else if (node.left == null) {
            Node rightNode = node.right;
            node.right = null;
            size--;
            retNode = rightNode;
        } else {
            Node successor = minimum(node.right);
            // 由于removeMin没有维持balance，所以我们复用remove
            successor.right = remove(node.right,successor.e);
            successor.left = node.left;
            node.left = node.right = null;
            
            retNode = successor;
        }
    } else if (e.compareTo(node.e) < 0) {
        node.left = remove(node.left, e);
        retNode = node;
    } else {
        node.right = remove(node.right, e);
        retNode = node;
    }
    
    //否则retNode.height会有空指针异常
    if (retNode == null) {
        return null;
    }
    
    //更新高度
    retNode.height = Math.max(getHeight(retNode.left),getHeight(retNode.right)) + 1;
    //计算平衡因子
    int balanceFactor = getBalanceFactor(retNode);
    if (balanceFactor > 1 && getBalanceFactor(retNode.left) >= 0) {
        return rightRotate(retNode);
    }
    if (balanceFactor < -1 && getBalanceFactor(retNode.right) <= 0) {
        return leftRotate(retNode);
    }
    if (balanceFactor > 1 && getBalanceFactor(retNode.left) < 0) {
        retNode.left = leftRotate(retNode.left);
        return rightRotate(retNode);
    }
    if (balanceFactor < -1 && getBalanceFactor(retNode.right) > 0) {
        retNode.right = rightRotate(retNode.right);
        return leftRotate(retNode);
    }
    return retNode;
}
```

## 完整代码

```java
import java.util.ArrayList;

public class AVLTree<E extends Comparable<E>> {
    private class Node {
        public E e;
        public Node left;
        public Node right;
        public int height;

        public Node(E e) {
            this.e = e;
            left = null;
            right = null;
            height = 1;
        }

        @Override
        public String toString() {
            return e.toString();
        }
    }

    //根节点
    private Node root;
    //树中元素的个数
    private int size;

    public AVLTree() {
        root = null;
        size = 0;
    }

    public int size() {
        return size;
    }
    public boolean isEmpty() {
        return size == 0;
    }
    private int getHeight(Node node) {
        if (node == null) {
            return 0;
        }

        return node.height;
    }

    private int getBalanceFactor(Node node) {
        if (node == null) {
            return 0;
        }

        return getHeight(node.left) - getHeight(node.right);
    }

    public boolean isBST() {
        ArrayList<E> arrayList = new ArrayList<>();
        inOrder(root, arrayList);

        for (int i = 1; i < arrayList.size(); i++) {
            if (arrayList.get(i-1).compareTo(arrayList.get(i)) > 0) {
                return false;
            }
        }
        return true;
    }
    private void inOrder(Node node, ArrayList<E> arrayList) {
        if (node == null) {
            return;
        }

        inOrder(node.left, arrayList);
        arrayList.add(node.e);
        inOrder(node.right, arrayList);
    }

    public boolean isBalanced() {
        return isBalanced(root);
    }
    //判断某个节点是不是平衡
    private boolean isBalanced(Node node) {
        if (node == null) {
            return true;
        }
        int balanceFactor = getBalanceFactor(node);
        if (Math.abs(balanceFactor) > 1) {
            return false;
        }
        return isBalanced(node.left) && isBalanced(node.right);
    }

    private Node rightRotate(Node y) {
        Node x = y.left;
        Node T3 = x.right;
        x.right = y;
        y.left = T3;

        //更新x和y的高度值 先更新y的，因为y是x的右孩子，x的更新取决于y
        y.height = Math.max(getHeight(y.left), getHeight(y.right)) + 1;
        x.height = Math.max(getHeight(x.left), getHeight(x.right)) + 1;

        return x;
    }

    private Node leftRotate(Node y) {
        Node x = y.right;
        Node T3 = x.left;
        x.left = y;
        y.right = T3;

        y.height = Math.max(getHeight(y.left), getHeight(y.right)) + 1;
        x.height = Math.max(getHeight(x.left), getHeight(x.right)) + 1;

        return x;
    }

    public void add(E e) {
        root = add(root, e);
    }


    private Node add(Node node, E e) {
        if (node == null) {
            size++;
            return new Node(e);
        }

        if (e.compareTo(node.e) < 0) {
            node.left = add(node.left, e);
        } else if (e.compareTo(node.e) > 0) {
            node.right = add(node.right,e);
        }

        //更新高度
        node.height = Math.max(getHeight(node.left),getHeight(node.right)) + 1;
        //计算平衡因子
        int balanceFactor = getBalanceFactor(node);

        if (balanceFactor > 1 && getBalanceFactor(node.left) >= 0) {
            return rightRotate(node);
        }
        if (balanceFactor < -1 && getBalanceFactor(node.right) <= 0) {
            return leftRotate(node);
        }
        if (balanceFactor > 1 && getBalanceFactor(node.left) < 0) {
            node.left = leftRotate(node.left);
            return rightRotate(node);
        }
        if (balanceFactor < -1 && getBalanceFactor(node.right) > 0) {
            node.right = rightRotate(node.right);
            return leftRotate(node);
        }

        return node;
    }

    public boolean contains(E e) {
        return contains(root, e);
    }

    private boolean contains(Node node, E e) {
        if (node == null) {
            return false;
        }

        if (e.equals(node.e)) {
            return true;
        } else if (e.compareTo(node.e) < 0) {
            return contains(node.left, e);
        } else {
            return contains(node.right,e);
        }
    }

    public E minimum() {
        if (size == 0) {
            throw new IllegalArgumentException("树为空");
        }
        return minimum(root).e;
    }

    private Node minimum(Node node) {
        if (node.left == null) {
            return node;
        }
        return minimum(node.left);
    }

    public void remove(E e) {
        root = remove(root, e);
    }
    private Node remove(Node node, E e) {
        if (node == null) {
            return null;
        }
        Node retNode;
        if (e.equals(node.e)) {
            if (node.right == null) {
                Node leftNode = node.left;
                node.left = null;
                size--;
                retNode = leftNode;
            } else if (node.left == null) {
                Node rightNode = node.right;
                node.right = null;
                size--;
                retNode = rightNode;
            } else {
                Node successor = minimum(node.right);
                successor.right = remove(node.right,successor.e);//由于removeMin没有维持balance，所以我们用remove
                successor.left = node.left;
                node.left = node.right = null;
                //size--; 在removeMin中已经维护size了
                retNode = successor;
            }
        } else if (e.compareTo(node.e) < 0) {
            node.left = remove(node.left, e);
            retNode = node;
        } else {
            node.right = remove(node.right, e);
            retNode = node;
        }

        //否则retNode.height会有空指针异常
        if (retNode == null) {
            return null;
        }

        //更新高度
        retNode.height = Math.max(getHeight(retNode.left),getHeight(retNode.right)) + 1;
        //计算平衡因子
        int balanceFactor = getBalanceFactor(retNode);
        if (balanceFactor > 1 && getBalanceFactor(retNode.left) >= 0) {
            return rightRotate(retNode);
        }
        if (balanceFactor < -1 && getBalanceFactor(retNode.right) <= 0) {
            return leftRotate(retNode);
        }
        if (balanceFactor > 1 && getBalanceFactor(retNode.left) < 0) {
            retNode.left = leftRotate(retNode.left);
            return rightRotate(retNode);
        }
        if (balanceFactor < -1 && getBalanceFactor(retNode.right) > 0) {
            retNode.right = rightRotate(retNode.right);
            return leftRotate(retNode);
        }
        return retNode;
    }
}
```

