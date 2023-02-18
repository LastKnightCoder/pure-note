---
tags: [栈]
---
## 题目描述

定义栈的数据结构，请在该类型中实现一个能够得到栈的最小元素的 `min` 函数。在该栈中，调用 `min`、`push`、及 `pop` 的时间复杂度都是 $O(1)$。

## 解题思路

我们第一个想到的办法就是使用一个变量来保存栈中的最小值，但是如果这个最小值被弹出了，那么我们怎么拿到此时栈中的最小值呢? 所以这个方法不可取，这里我们的方法是使用一个辅助栈来存储栈中的最小值，当我们向栈中添加一个元素时，我们也要向辅助栈中添加一个元素，但是向辅助栈中添加的元素不是随便添加的，这个栈的栈顶存储的是此时栈中的最小值，所以当栈添加一个元素时，如果这个元素比当前的最小元素还小，那么将这个元素添加到辅助栈中，如果这个元素没有此时的最小元素小，那么向辅助栈中再次添加此时的最小值

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/ImgHosting3@master/包含min函数的栈.drawio2022-04-23-15-29-12.svg" style="zoom:50%"/>

我们使用辅助栈时，即使我们将最小元素弹出了，但是辅助栈中还是保存着下一最小元素，所以我们可以得到此时栈的最小值，即是辅助栈的栈顶元素，如下

```java
import java.util.Stack;

public class StackWithMin {
    private Stack<Integer> stack;
    // 辅助栈
    private Stack<Integer> minStack;

    public StackWithMin() {
        stack = new Stack<>();
        minStack = new Stack<>();
    }

    public void push(Integer value) {
        stack.push(value);
        if (minStack.isEmpty() || minStack.peek() > value) {
            // 比当前最小元素小，那么将这个元素添加到辅助栈中
            minStack.push(value);
        } else {
            // 没有当前最小元素小，那么再次添加当前的最小元素到栈中
            minStack.push(minStack.peek());
        }
    }

    public Integer pop() {
        if (stack.empty()) {
            throw new RuntimeException("stack is empty");
        } 

        // 弹出时，辅助栈也要相应的弹出
        minStack.pop();
        return stack.pop();
    }

    public Integer min() {
        if(minStack.isEmpty()) {
            throw new RuntimeException("stack is empty");
        }
        // 栈的最小值即是辅助栈的栈顶元素
        return minStack.peek();
    }
}
```



