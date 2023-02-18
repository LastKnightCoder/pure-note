---
title: Java集合与泛型
author: 熊滔
commentid: javase:collection-and-generic
---

## Collection

Collection是集合的意思，你可以把它看做一种装东西的容器，就像数组一样。它是一个接口，有很多的类实现了它，比如Arralist，LinkedList，HashMap，等等很多。不同的类使用不同的方法去实现，所以他们之间的某些性能是不同的。

### Collection中的方法

我们这次学习的是Collection接口中的方法，虽然实现它的类很多，但是它们实现的方法的功能都是一样。这里介绍七个方法

- add(E e)
  - 添加元素
- clear()
  - 清空集合中的所有元素
- remove(E e)
  - 删除元素
- contains(E e)
  - 查看是否包含某个元素
- isEmpty()
  - 查看数组是否为空
- size()
  - 返回集合的长度，即元素的个数
- toArray
  - 返回一个Onject类型的数组

下面我们以ArrayList为例来学习上面的方法

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collection;

public class TestCollection {
    public static void main(String[] args) {
        Collection<String> collection = new ArrayList<>(); //多态写法
        //测试add
        collection.add("张无忌");
        collection.add("令狐冲");
        collection.add("郭靖");
        collection.add("杨过");
        System.out.println(collection);
        //测试contains
        System.out.println(collection.contains("杨过"));
        //测试size
        System.out.println(collection.size());
        //测试remove
        collection.remove("杨过");
        System.out.println(collection);
        //测试toArray
        Object[] objects = collection.toArray();
        System.out.println(Arrays.toString(objects));
        //测试clear
        collection.clear();
        System.out.println(collection);
        System.out.println(collection.size());
    }
}
```

我们注意到我们创建Collection对象时，与一般类相比创建多了\<>，这个叫做泛型。是什么意思呢? 我们定义数组的话有不同的数据类型，来定义保存什么类型的数据，同样的Collection也有不同的数据类型，来决定里面保存的是什么类型的数据。而数据类型就是写在里面，目前我们就这么理解泛型，有关泛型更加详细的用法，下面会进行介绍。在=号的右边也有\<>号，从JDK 1.7开始，右边的\<>里面可以什么都不写，之前里面也要写数据类型，但是和右边的一样，所以没必要在写一遍。另一个需要注意的是，泛型（也就是<>里面的数据类型）必须是引用类型，不能是基本类型。

输出为

```java
[张无忌, 令狐冲, 郭靖, 杨过]
true
4
[张无忌, 令狐冲, 郭靖]
[张无忌, 令狐冲, 郭靖]
[]
0
```

### Iterator

我们对集合一个重要的操作就是对集合进行遍历，不同于数组，集合不是所有的都是有序的，所以无法通过索引对集合进行遍历，那我们就只能通过迭代器Iterator来遍历集合，Iterator也是一个接口，集合有一个iterator()方法，可以获取迭代器实现类的对象。迭代器主要有两个方法

- hasNext()
  - 判断集合中是否还有下一个元素，有则返回true
- next()
  - 取出集合中的下一个元素，如何集合中没有元素，使用该方法会抛出异常，所以应该先进行判断是否还有下一个元素

下面我们来示例使用方法

```java
import java.util.Collection;
import java.util.HashSet;

public class TestIterator {
    public static void main(String[] args) {
        //HashSet是一个无序的集合
        Collection<String> collection = new HashSet<>();
        collection.add("亚瑟");
        collection.add("妲己");
        collection.add("安其拉");
        collection.add("狄仁杰");
        collection.add("李白");
        System.out.println(collection);
    }
}
```

输出为

```java
[李白, 妲己, 狄仁杰, 亚瑟, 安其拉]
```

可见HashSet是一个无序的集合，所以不能通过索引去获取集合中的元素，我们使用迭代器去遍历集合中的元素

```java
Iterator<String> iterator = collection.iterator(); //由集合的iterator()方法创建迭代器
while (iterator.hasNext()) { //判断集合中是否还有下一个元素
    String string = iterator.next(); //取出下一个元素，每取出一个元素，指针向后移动
    System.out.println(string);
}
```

输出为

```java
李白
妲己
狄仁杰
亚瑟
安其拉
```

### foreach循环

我们知道不能通过一般的for循环去遍历集合，所以Java中有一个增强的for循环，利用它可以遍历集合和数组，它的实现原理就是迭代器的原理，不过使用的是for循环的形式，我们把它叫做foreach循环，格式如下

```java
for (元素类型 变量名 : 集合)
```

它会自动的取出集合中的元素，并且赋值给变量，然后你就可以在foreach循环中对取出的元素进行操作了，还是以上面的集合为例，演示如何使用foreach循环

```java
for (String string : collection) {
    System.out.println(string);
}
```

输出为

```java
李白
妲己
狄仁杰
亚瑟
安其拉
```

## 泛型

### 概述

泛型我们之前在使用Collection集合时就已经接触过，那么为什么会使用泛型，比如你正在写一个集合给别人使用，但是你不知道别人会保存什么数据类型，你不能写死了说只能保存String类型，这个时候我就会使用泛型，保存什么类型的数据由别人自己觉得，就像数组一样。

如果省略泛型的话，那么默认为Object类型，比如下面我创建一个ArrayList集合，没有写泛型

```java
import java.util.ArrayList;

public class GenericsDemo {
    public static void main(String[] args) {
        ArrayList list = new ArrayList();
        list.add("AA");
        list.add(2);
        list.add('c');

        for (int i = 0; i < list.size(); i++) {
            System.out.println(list.get(i));
        }
    }
}
```

输出为

```java
AA
2
c
```

这个时候ArrayList集合的对象能够添加任何的数据类型，因为所有的类都继承了Object类，而基本数据类型会进行自动装箱操作转化为对应的包装类。其实上面就是多态的写法，而多态的写法我们也知道它的弊端，他不能调用子类特有的方法，如果我们需要调用子类的方法的话就需要向下转型，这很容易发生异常，并且在编译时不会报错。并且一般我们在集合保存相同的数据类型，所以这个时候我们可以使用泛型。使用泛型的话至少有这两个优点

- 避免了类型转换的麻烦
- 编译时就可以看到错误
  - 如果一个String的ArrayList添加Integer数据，那么编译时就会报错

### 泛型的定义

#### 类

泛型定义在类中的格式应该如下

```java
public class ClassName<E> {
    
}
```

其中E就代表泛型，它会在创建对象的时候确定E是什么类型，比如ArrayList\<String> list = new ArrayList\<>();，那么这个时候E就是String。这么定义以后，我们就可以在类中的方法中使用E，如

```java
public void method(E e) {
    
}
```

```java
public E method() {
    
}
```

可以让它作为方法的参数和返回值，如果在创建对象的时候传入的E是String，那么这些方法的参数或者返回全部都是String类型的。我们可以看做E是一个接收类类型的变量。

#### 方法

方法中定义泛型，格式如下

```java
修饰符 <泛型> 返回值 方法名(参数列表) {
    
}
```

比如下面这么定义

```java
public <M> void method (M m) {
    
}
```

泛型会在调用方法时确定，比如传入的是一个String类型的参数，那么M就是String。

#### 接口

在接口中定义的格式如下

```java
public interface InterfaceName<E> {
    
}
```

实现类实现接口可以指定泛型是什么或者不指定，就是这个意思

- 指定

```java
实现类 implements InterfaceName<String> {
    
}
```

- 不指定

```java
实现类<E> implements InterfaceName<E>
```

### 通配符

假设我要写一个方法，输入一个ArrayList对象，要求打印输入列表的所有元素，但是ArrayList是什么类型的不知道，所以方法参数类型不能写死，不能这么写

```java
public void method(ArrayList<String> list) {
    
}
```

但是我们在上面学了方法的泛型，所以我们可以这么写

```java
public <E> void method(ArrayList<E> list) {
    
}
```

Java停供了一种更加方面的写法，即使用通配符

```java
public void show(ArrayList<?> list) {
    
}
```

其中?代表的就是通配符，指的就是能够匹配任何的数据类型。

不仅如此我们还能够对通配符进行限定，如

- ? extends E
  - 说明参数的类型必须是`E`的子类或者`E`本身
- ? super E
  - 说明参数的类型必须是`E`的父类或者`E`本身

比如下面

```java
import java.util.ArrayList;

public class TestTongpeifu {
    //接收的类型必须为Number类的子类或者Number类本身
    public static void show(ArrayList<? extends Number> list) { 
        System.out.println(list);
    }
	
    //接收的类型必须为Number类的父类或者Number类本身
    public static void show2(ArrayList<? super Number> list) {
        System.out.println(list);
    }
    
    public static void main(String[] args) {
        ArrayList<Integer> list1 = new ArrayList<>();
        ArrayList<String> list2 = new ArrayList<>();
        ArrayList<Object> list3 = new ArrayList<>();
        ArrayList<Number> list4 = new ArrayList<>();
        
        show(list1); //Interger是Number的子类，可以
        show(list2); //String类与Number类没有关系，报错
        show(list3); //Object不是Number的子类，报错
        show(list4); //Number本身，可以

        show2(list1); //Interger是子类，报错 
        show2(list2); //String没关系，报错
        show2(list3); //Object是父类，可以
        show2(list4); //Number本身，可以
    }


}
```

<center>
    <img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java21.png"/>
</center>

与我们分析的一致。

## List

List接口是Collection的子接口，实现它的类有ArrayList和LinkedList，ArrayList的底层实现原理是数组，所以它的查询速度快，但是它的增删操作很慢，LinkedList的底层实现原理是链表，所以它的查询操作很慢，它的增删操作很快。

### List集合的特点

List接口的特点有

- 有序，所以可以通过索引访问元素
- 集合中的元素允许重复

因为List接口可以有索引，所以除了Collection中的方法，List还有其特有的方法如下

- add(int index, E e)
  - 在指定索引中的位置添加元素，后面的元素向后推移
- remove(int index)
  - 删除指定索引的元素，并且返回删除的元素
- get(int index)
  - 获得指定索引处的元素
- set(int index, E e)
  - 将索引处为元素替换为e

下面示例上面的四个方法

```java
import java.util.ArrayList;
import java.util.List;

public class TestList {
    public static void main(String[] args) {
        List<Integer> list = new ArrayList<>();
        list.add(1);
        list.add(5);
        list.add(7);
        System.out.println(list); //[1, 5, 7]

        //add(int index, E e)
        list.add(2,8); //在索引为2的地方插入8
        System.out.println(list); //[1, 5, 8, 7]
        //get(int index)
        int num1 = list.get(1); //获得索引为1的元素
        System.out.println(num1); //5
        //set(int index, E e)
        list.set(0, 4); //设置索引为0的元素为4
        System.out.println(list); //[4, 5, 8, 7]
        int num2 = list.remove(2); //删除索引为2的元素
        System.out.println(list); //[4, 5, 7]
    }
}
```

### List的遍历

因为List集合是有序的，所以有三种方法可以进行遍历

- 普通for循环
- 迭代器
- 增强for循环

下面进行示例

```java
//普通for写法
for (int i = 0; i < list.size(); i++) {
    System.out.println(list.get(i));
}

//迭代器写法
Iterator<Integer> iterator = list.iterator();
while (iterator.hasNext()) {
    Integer i = iterator.next();
    System.out.println(i);
}

//增强for写法
for (Integer i : list) {
    System.out.println(i);
}
```

### ArrayList

ArrayList在之前我们讲解Collection时就有接触到，所以这里就简单的介绍它。ArrayList集合的使用非常的简单，在上面已经演示过了，并且它的方法都是实现List里面的方法，而这些方法的使用在上面已经了解了，下面看看ArrayList简单例子即可。

```java
import java.util.ArrayList;

public class TestArrayList {
    public static void main(String[] args) {
        //创建一个ArrayList对象 里面保存的都是String类型
        ArrayList<String> list = new ArrayList<>();

        //向列表中添加三个元素
        list.add("张三");
        list.add("李四");
        list.add("王五");

        //打印列表
        System.out.println(list);  //ArrayList对toString()方法重写了，所以打印输出不是地址值
        //获取列表里面的元素
        System.out.println("列表的第一个元素为：" + list.get(0));
        System.out.println("列表的第二个元素为：" + list.get(1));
        System.out.println("列表的第三个元素为：" + list.get(2));
        System.out.println("列表的长度为：" + list.size());  //获取列表的长度

        System.out.println("===============");
        
        //删除
        System.out.println("删除的元素为：" + list.remove(1)); //删除第二个元素，即删除李四
        System.out.println("列表的长度为：" + list.size()); //列表的长度
        System.out.println(list);
    }
}

```

输出为：

```java
[张三, 李四, 王五]
列表的第一个元素为：张三
列表的第二个元素为：李四
列表的第三个元素为：王五
列表的长度为：3
===============
删除的元素为：李四
列表的长度为：2
[张三, 王五]
```

上面代码的注释已经详细说明上面代码的功能，这里说一下一个特别的地方。当我们打印数组名时，会得到数组的地址，但是当我们打印ArrayList对象，输出的不是地址，而是里面的元素，这是因为ArrayList重写了toString()方法，当System.out.println()里面传入的是引用类型时，会调用该引用类型的toString方法，由于ArrayList重写了toString()方法，所以打印输出的不是地址，没有重写该方法的类，打印输出默认是地址。如果在这里你搞不懂什么重写，toString()方法都是什么，那么没关系，你只要知道**直接打印ArrayList对象名，输出的不是地址，而是里面包含的所有元素**，具体重写是什么，toString()是什么，在后面的继承部分将会有阐述。

需要注意的是，泛型只能是引用类型，不能是基本数据类型，那么如果我们想要保存基本数据类型怎么办。当然是有办法的，Java中为每一个基本数据类型提供了一个包装类，它虽然是一个类，但是你在使用时完全把它当做基本数据类型就可以，因为泛型里面不能是基本数据类型，才会有这么一个包装类。下表是基本数据类型与包装类对应的名称

| 基本数据类型 | 包装类      |
| ------------ | ----------- |
| byte       | Byte      |
| short      | Short     |
| int        | Integer   |
| long       | Long      |
| float      | Float     |
| double     | Double    |
| char       | Character |
| boolean    | Boolean   |

上面除了int和char对应的包装类不只是首字母大写，其他都是只要首字母大写即可。下面我将演示如何存储基本数据类型，以int类型为例

```java
import java.util.ArrayList;

public class BasicDataToArrayList {
    public static void main(String[] args) {
        ArrayList<Integer> list = new ArrayList<>();

        //把Integer当做int看待即可，我们不需要new 一个Integer对象 编译器会帮我们做处理，我们不需要担心
        list.add(100);
        list.add(25);
        list.add(15);

        //打印列表
        System.out.println(list);
    }
}

```

输出为

```java
[100, 25, 15]
```

在基本数据类型与包装类之间有自动装换，基本数据类型转换为包装类的过程叫做**装箱**，包装类转化为基本数据类型的过程叫做**拆箱**，从JDK 1.5开始就支持自动装箱和自动拆箱。即我们可以这么写

```java
Interger a = 12; //自动装箱 int -- Interger
int b = a; //自动拆箱 Interger -- int
```

所以我们在使用时把Interger看做int就可以了。


### LinkedList

LinkedList是List的实现类，它的底层原理是基于链表实现的，所以除了List接口中的方法，它还提供了很多与头尾有关的方法，如

- addFirst
- addLast
- removeFirst
- removeLast
- getFirst
- getLast
- push
  - 同addFirst一样
- pop
  - 同removeFirst

下面进行示范

```java
import java.util.LinkedList;

public class TestLinkedList {
    public static void main(String[] args) {
        LinkedList<String> linkedList = new LinkedList<>();
        linkedList.add("AAA");
        linkedList.add("BBB");

        //addFirst 在"AAA"前面添加元素"CCC"
        linkedList.addFirst("CCC");
        System.out.println(linkedList); //[CCC, AAA, BBB]
        //push 在"CCC"前面添加"DDD"
        linkedList.push("DDD");
        System.out.println(linkedList); //[DDD, CCC, AAA, BBB]
        //pop 删除"DDD"
        System.out.println(linkedList.pop()); //DDD
        //获得第一个元素 getFirst
        System.out.println(linkedList.getFirst()); //CCC
        //removeFirst 删除第一个元素
        System.out.println(linkedList.removeFirst()); //CCC
        System.out.println(linkedList); //[AAA, BBB]
        //removeLast 删除最后一个元素
        System.out.println(linkedList.removeLast()); //BBB
        System.out.println(linkedList); //[AAA]
    }
}
```

## 基本类型与包装类

在前面我们就讲过这个概念，不过当时讲的不够详细，现在深入讲解。首先我们知道包装类是什么，因为基本数据类型它不是引用类型，而由于泛型必须是引用类型，所以才有包装类。包装类就是将基本数据类型包装为一个类，这个类我们完全可以当做基本数据类型使用，并且包装类除此之外，还提供了一些方法用法操作基本数据类型，这是基本数据类型所没有的。

### 装箱与拆箱

首先我们将如何进行装箱操作，即将基本数据类型转换为对象的包装类，下面全部以Integer为例，其余的同理，Integer对象的创建有两种方法，一种是通过构造方法，一种是通过Integer的一个静态方法

- 构造方法
  - Interger(int value)
    - 接收一个int类型的数值
  - Interger(String str)
    - 接收一个字符串，该字符串要符合规定的格式，如"100"
- 静态方法
  - valueOf()
    - 接收的参数同构造方法，也可以接收字符串类型的参数

下面演示如何创建Interger对象

```java
Integer in1 = new Integer(2);
Integer in2 = new Integer("20");
Integer in3 = Integer.valueOf(200);
Integer in4 = Integer.valueOf("2000");
System.out.println(in1 + " " + in2 + " " + in3 + " " + in4);
```

输出为

```java
2 20 200 2000
```

那如何通过包装类得到一个基本数据类型呢？通过intValue方法，如下

```java
int i = in1.intValue();
System.out.println(i);
```

输出为

```java
2
```

### 自动装箱和自动拆箱

自从JDK 1.5以后就实现了自动装箱和拆箱，可以直接把基本数据类型赋值给包装类，也可以把包装类直接赋值给基本数据类型，如下

```java
Integer num = 10; //自动装箱 相当于 Integer num = new Interger(10)  不过现在是自动进行的了
int i = num; //自动拆箱 相当于 int i = num.intValue()
```

### 基本数据类型与字符串类型相互转换

基本数据类型转换为字符串类型

- 基本数据类型 + "",如100 + ""得到"100"
- 包装类的toString(参数)，这个不是Onject的toString()方法，因为它有参数，不是重写
  - 如Integer.toString(100)得到"100"
- String类的valueOf(参数)方法，如String.valueOf(100)得到"100"

字符串转基本数据类型

- 包装类的parseXxx，如Integer.parseInt("100")得到基本数据类型100

## Set

Set接口也是Collection的子接口，它的实现类有HaseSet，LinkedHashSet，HashSet是无序的，LinkedHashSet是有序的。

### Set集合的特点

- 集合中的元素不能重复
- 没有索引

下面看一个例子

```java
import java.util.HashSet;
import java.util.Set;

public class TestSet {
    public static void main(String[] args) {
        Set<Integer> set = new HashSet<>();
        set.add(1);
        set.add(3);
        set.add(2);
        set.add(1);
        System.out.println(set); //无序且不能重复 [1, 2, 3]
    }
}
```

可见集合的顺序与添加的顺序无关，并且添加重复的元素是不行的。

> Set集合不能添加重复元素的原理：Set集合的底层原理是Hash表，首先会根据要添加的元素计算出它的哈希值，根据哈希值添加到数组中，数组中存储的是链表或者红黑树，元素会添加到数组里面的链表或者红黑树中。一般不同的元素会添加到数组的不同索引中，即不同的链表或红黑树中，但是也有可能虽然元素不同，但是它们的哈希值相同，这个就叫做哈希冲突。如果发生了哈希冲突，那么会使用equals()方法判断该链表或红黑树中是否存在与这个元素相同的元素，如果有，那么就不添加，如果没有，那么就添加。

所以如果你要添加自定义的类型，那么就要重写Object类的hashCode()方法和equals()方法。

### Set集合的遍历

Set由于它是无序的，所以它不能使用普通for循环遍历，所以只能使用

- 迭代器
- 增强for

由于与List差不多，这里就不在演示了。

### LinkedHashSet

LinkedHashSet是HashSet的子类，但是它是有序的。如

```java
import java.util.LinkedHashSet;
import java.util.Set;

public class TestLinkedHashSet {
    public static void main(String[] args) {
        Set<String> set = new LinkedHashSet<>();
        set.add("aaa");
        set.add("ccc");
        set.add("bbb");
        System.out.println(set); //[aaa, ccc, bbb] 与添加的顺序相同
    }
}
```

## 可变参数

可变参数指的方法能够接受的参数可以为多个，定义格式如下

```java
数据类型... 变量名
```

它的底层原理是数组，会把这多个元素放到一个数组中。假设下面方法接受多个整数类型的参数，但是不知道能接受多少个，然后打印出这些数的和，那么就可以这么写

```java
public class TestKebiancanshu {
    public static void sum(int... ints) {
        //ints就是一个数组
        int sum = 0;
        for (int i = 0; i < ints.length; i++) {
            sum += ints[i];
        }
        System.out.println(sum);
    }
    public static void main(String[] args) {
        sum(1,2,3); //可以传入多个参数，也可以不传 
        sum(1,5,7,8); 
    }
}
```

输出为

```java
6
21
```

注意：

- 一个方法只能有一个可变参数
- 一个方法如果有多个参数，可变参数要写在末尾

## Collections

Collections是一个工具类，它提供了很多的静态方法用来对Collection集合进行操作。我们下面就简单介绍Collections类的三个方法

- addAll(Collection\<? super T> c, T... elements)
  - 接收两个参数，第一个为Collection集合，第二个参数为可变参数
  - 为集合添加多个元素，如addAll(list, 1, 2, 3)
- shuffle(List\<?> list)
  - 接收一个List集合，将集合里面的元素随机打乱
- sort(List\<T> list)
  - 接收一个list集合，将按照默认升序的规则排序
  - 如果集合里面装的是自定义的类的对象，那么该类要实现Comaprable接口，并且要重写compareTo()方法
- sort(List\<T> list, Comparator\<? super T> c)
  - 接收一个List集合和一个实现Comparator接口的类的对象，可以传入匿名类

下面演示方法的使用

- addAll

```java
List<Integer> list1 = new ArratList<>();
Collections.addAll(list1, 1, 2, 3);
System.out.println(list1); //[1, 2, 3]
```

- shuffle

```java
List<Integer> list2 = new ArrayList<>();
Collections.addAll(list2, 1, 2, 3, 4, 5, 6);
Collections.shuffle(list2);
System.out.println(list2);  //[6, 4, 3, 5, 2, 1]
```

- sort(List\<T> list)

```java
//先演示对Integer类的排序
List<Integer> list3 = new ArrayList<>();
Collections.addAll(list3, 1, 7, 10, 5, 6, 4);
Collections.sort(list3);
System.out.println(list3); //[1, 4, 5, 6, 7, 10]
```

下面演示自定义类的排序，首先我们定义一个Person类，它有name和age属性，我们根据age的大小进行升序排序，如下

```java
public class Person implements Comparable<Person> {
    String name;
    int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public int compareTo(Person o) {
        //升序
        return this.age - o.age; //降序o.age - this.age
    }

    @Override
    public String toString() {
        return "Person{" + "name='" + name + '\'' + ", age=" + age + '}';
    }
}
```

测试该类

```java
Person p1 = new Person("古力娜扎",20);
Person p2 = new Person("迪丽热巴", 18);
Person p3 = new Person("刘亦菲", 21);
List<Person> list4 = new ArrayList<>();
Collections.addAll(list4, p1, p2, p3);
Collections.sort(list4);
System.out.println(list4);
```

输出为

```java
[Person{name='迪丽热巴', age=18}, Person{name='古力娜扎', age=20}, Person{name='刘亦菲', age=21}]
```

- sort(List\<T> list, Comparator\<? super T> c)

这次我们不需要实现Comparable接口，只需要传入一个Comparator的实现类对象就可以，该类重写Comparator中的compare方法，在该方法中规定了排序的规则，我们可以传入一个匿名类

```java
List<Person> list5 = new ArrayList<>();
Collections.addAll(list5, p1, p2, p3);
Collections.sort(list5, new Comparator<Person>() {
    @Override
    public int compare(Person o1, Person o2) {
        //升序
        return o1.age - o2.age;
    }
});
System.out.println(list5);
```

输出为

```java
[Person{name='迪丽热巴', age=18}, Person{name='古力娜扎', age=20}, Person{name='刘亦菲', age=21}]
```

## Map

之前我们介绍的集合如List，Set都是单列集合，下面我们将介绍双列集合Map，它是通过键K去寻找值V的，所以说它是一个双列集合。它是一个接口，它的常用子类有HashMap和LinkedHashMap。其中HashMap是无序的，即在集合中存储的顺序与你添加的顺序是不一致的。LinkedHashMap是有序，即添加顺序与保存的顺序相同。LinkedHashMap是HashMap的子类。我们之前介绍的HashSet类是调用HashMap实现的，它只利用了HashMap的K。

### Map集合的方法

下面介绍Map集合的常用方法

- put(key, value)
  - 向Map中添加一对键值对，由于Map集合中的key是不能重复的，如果Map中已经存在该key，那么将集合中该key所对应的value值替换为添加的value，即相当于更新，并且返回被替换的value值，如果该集合中不存在该key，那么将该键值对添加，并且返回null
- get(key)
  - 通过键值来获得对应的value值，如果集合不存在该key，那么返回null
- remove(key)
  - 根据键来删除该键值对，如果该key不存在，那么返回null，如果存在，那么返回对应的value值
- containsKey(key)
  - 判断集合中是否存在键key，有则返回true，否则返回false

下面简单演示这四个方法

```java
import java.util.HashMap;
import java.util.Map;

public class TestMap {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();
        //添加元素
        map.put("迪丽热巴",18);
        map.put("古力娜扎",19);
        map.put("佟丽娅",20);
        System.out.println(map); //{佟丽娅=20, 迪丽热巴=18, 古力娜扎=19}

        //这里已经有"迪丽热巴"这个键了，所以对应的值会被更改为20，并将该值18返回
        //这里最好使用Integer接收，因为可能返回null 而基本数据类型不能被赋值为null
        Integer val1 = map.put("迪丽热巴",20);
        System.out.println(val1); //18
        System.out.println(map); //{佟丽娅=20, 迪丽热巴=20, 古力娜扎=19}

        //通过键去获得值
        Integer val2 = map.get("古力娜扎");
        System.out.println(val2); //19

        //删除键"佟丽娅"对于的键值对
        Integer val3 = map.remove("佟丽娅");
        System.out.println(val3); //20
        System.out.println(map); //{迪丽热巴=20, 古力娜扎=19}

        //判断是否包含键"佟丽娅"
        System.out.println(map.containsKey("佟丽娅")); //false
    }
}
```

### 遍历Map集合

主要有两个方法用来遍历Map集合

- keySet
  - 该方法会返回Set集合，里面是key的值，然后我们可以遍历该Set集合来遍历Map集合
- entrySet
  - 该方法也返回一个Set集合，不过这个集合里面的是Entry对象，Entry是Map的内部类，该类会在添加键值对时创建一个Entry对象保存相应的key和value的信息，我们可以通过Entry对象的getKey()和getValue()方法来获得键和值。同样我们可以遍历该Set集合来遍历Map集合

下面演示两个方法的使用

- keySet

```java
import java.util.HashMap;
import java.util.Map;
import java.util.Set;

public class TestMap {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();
        //添加元素
        map.put("迪丽热巴",18);
        map.put("古力娜扎",19);
        map.put("佟丽娅",20);
        System.out.println(map); //{佟丽娅=20, 迪丽热巴=18, 古力娜扎=19}

        //获得key的Set集合
        Set<String> set1 = map.keySet();
        //遍历Set集合
        for (String set: set1) {
            System.out.println(set + "=" + map.get(set));
        }

        //获得Entry对象组成的Set集合
        Set<Map.Entry<String,Integer>> set2 = map.entrySet();
        //遍历该集合
        for (Map.Entry<String, Integer> set : set2) {
            System.out.println(set.getKey() + "=" + set.getValue());
        }
    }
}

```

输出为

```java
佟丽娅=20
迪丽热巴=18
古力娜扎=19
佟丽娅=20
迪丽热巴=18
古力娜扎=19
```


## 斗地主案例练习

该案例模拟斗地主的发牌和看牌过程，加强对集合的使用。

### 分析

首先我们要使用Map\<Integer, String>集合来保存一副扑克，Integer是索引，String是对应的扑克牌。我们通过索引去找牌，到时候把索引发给玩家就可以，这样因为玩家拿到的是索引，那么就可以进行排序。我们首先要创建一副扑克，可以使用两个String数组，一个保存花色，一个保存数字，然后通过循环组合两个数组来组合一副扑克牌。因为我们要进行排序，所以Integer的大小和牌的大小要一一对应，即0对应大王，1对应小王，2-5对应四个2，以此类推。洗牌我们可以使用Collections的shuffle()方法，由于该方法要求传入List集合，我们要创建一个List集合，该集合要保存Integer索引。发牌就把索引发给玩家，玩家通过索引去Map集合中看牌。

### 代码实现

```java
import java.util.*;

public class Doudizhu {
    public static void main(String[] args) {
        //使用两个数组组合形成一幅扑克牌
        String[] colors = {"♠","♥","♣","♦"};
        String[] numbers = {"2","A","K","Q","J","10","9","8","7","6","5","4","3"};

        //将扑克牌存储到Map集合中，键为整数索引，方便排序，值为牌，通过索引拿牌
        Map<Integer,String> poker = new HashMap<>();
        //因为使用Collections.shuffle方法洗牌，所以要使用List集合存储索引
        List<Integer> list = new ArrayList<>();

        //将大小王添加到Map中，并将索引添加到List中
        int index = 0;
        poker.put(index,"大王");
        list.add(index);
        index++;
        poker.put(index,"小王");
        list.add(index);
        index++;

        //两个数组组合形成一副牌
        for (String number : numbers) {
            for (String color : colors) {
                poker.put(index,color+number);
                list.add(index);
                index++;
            }
        }

        //洗牌
        Collections.shuffle(list);

        ArrayList<Integer> player1 = new ArrayList<>();
        ArrayList<Integer> player2 = new ArrayList<>();
        ArrayList<Integer> player3 = new ArrayList<>();
        ArrayList<Integer> dipai = new ArrayList<>();

        //发牌，将索引发给玩家
        for (int i = 0; i < list.size(); i++) {
            if (i >= 51) {
                dipai.add(list.get(i));
            } else if (i % 3 == 0) {
                player1.add(list.get(i));
            } else if (i % 3 == 1) {
                player2.add(list.get(i));
            } else if (i % 3 == 2) {
                player3.add(list.get(i));
            }
        }

        //给牌排序
        Collections.sort(player1);
        Collections.sort(player2);
        Collections.sort(player3);
        Collections.sort(dipai);

        //看牌
        lookPoker(player1,poker,"刘德华");
        lookPoker(player2,poker,"周润发");
        lookPoker(player3,poker,"周星驰");
        lookPoker(dipai,poker,"底牌");

    }

    //看牌方法 通过玩家的索引去Map集合中找到对应的牌  并打印出来
    public static void lookPoker(ArrayList<Integer> list, Map<Integer,String> poker,String name) {
        System.out.print(name + "的牌是： ");
        //通过玩家的索引，去poker中取牌
        for (Integer number : list) {
            System.out.print(poker.get(number) + " ");
        }

        System.out.println();
    }
}
```

结果为

<center>
    <img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java22.gif"/>
</center>
<Disqus />