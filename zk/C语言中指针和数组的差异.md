---
title: C语言：指针和数组的差异
created: 2023/2/12 14:18:05
updated: 2023/2/12 14:18:05
tags: [C, 指针, 数组]
---

学 C 语言的时候，会提及数组是一种特殊类型的指针，数组名指向数组首元素的地址，但是经过个人实践发现数组并不能等价于指针，二者存在差异。

## sizeof

使用 `sizeof` 运算符对数组和指针的操作结果不同

```c
#include<stdio.h>

int main() {
  int a[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
  int *p = a;

  printf("sizeof(a) = %zd\\n", sizeof(a)); // sizeof(a) = 40
  printf("sizeof(p) = %zd\\n", sizeof(p)); // sizeof(p) = 8

  return 0;
}
```

数组的大小为 `sizeof(int) * len` ，而指针的大小始终为 8 个字节。

## &

使用 `&` 对数组进行操作，得到的是指向数组的指针

```c
#include<stdio.h>

int main() {
  int a[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
  int (*p)[10] = &a;

  for (int i = 0; i < 10; i++) {
    printf("%d ", (*p)[i]); // 1 2 3 4 5 6 7 8 9 10
  }
  printf("\\n");
  return 0;
}
```

## 赋值

数组和指针都可以加减运算，但是数组不能被重新赋值

```c
#include<stdio.h>

int main() {
  int a[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
  int *p = a;

  printf("%d\\n", *(a + 1)); // 2
  printf("%d\\n", *(p + 1)); // 2
}
```

指针可以被重新赋值，所以指针能进行 `++/--` 自增/自减操作，而数组不能。

```tip
我觉得当且仅当数组作为参数传递给函数的时候，指针和数组完全等同，这个时候传递给函数的数组已经不再是数组了，而是指针。
```