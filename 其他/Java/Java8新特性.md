---
title: Java8新特性
author: 熊滔
commentid: javase:java8-new-feature
---

本篇文章会介绍`Java 8`中的一些新特性(不包括`Lambda`表达式，因为在[Java多线程](https://lastknightcoder.github.io/vuepress-blog/Java/Java多线程)这篇文章中介绍过了)。主要内容是`Java 8`中新增的函数式接口以及`Stream`流，还有方法引用。

## 常用函数式接口

函数式接口指的就是接口里面只含有一个抽象方法。这样我们就可以使用`Lambda`表达式编程，这是一种函数式编程的思想，强调的是怎么做。`Java 8`提供了很多的函数式接口，这里我们介绍常见的函数式接口。

- `Supplier<T>`
- `Consumer<T>`
- `Predicate<T>`
- `Functional<T,R>`

### Supplier\<T>

该接口里面有一个`T get()`方法，按照字面意思，是提供者的意思，表示生产出一个与泛型类型`T`相同的数据。下面我们来讲一个例子说明此接口的使用。假设有一个方法，需要返回一个字符串，该字符串由`Supplier`接口的`get()`方法产生，而产生什么样的字符串，则由程序员在调用该方法是传入`Lambda`决定。如下方法传入一个`Supplier`接口得到一个字符串

```java
public static String getString(Supplier<String> supplier) {
    return supplier.get();
}
```

我们在`main`方法中调用该方法

```java
String str = getString(() -> {
    return "Hello World!";
});
```

输出为

```java
Hello World!
```

### Consumer\<T>

与`Supplier`接口不同的是，`Consumer`接口是消费或者说处理一个与泛型类型相同数据类型的数据，它有一个`accept(T t)`方法，该方法用来消费数据，假设有下面这一个方法

```java
public static void handleString(String str, Consumer<String> consumer) {
    consumer.accept(str);
}
```

我们使用`Consumer`来消费(处理)传入的这个字符串，而怎么消费，就取决与程序员在调用该方法时传入的`Lambda`，这时对程序员来说，就是怎么做的问题，相当于传入一个方法去处理数据，这就是函数式编程，这里我们就简单的将传入的数据进行打印

```java
handleString("Hello Again!", (String str) -> {
    System.out.println(str);
});
```

输出为

```java
Hello Again!
```

`Consumer`接口中有一个默认方法`andThen(Consumer consumer)`，看下面的程序说明它的用处

```java
con1.andThen(con2).accept(str); //con1和con2都是Consumer接口的实现类对象
//相当于下面的代码
con1.accept(str);
con2.accept(str);
```

假设有一个方法有需要传入两个`Consumer`接口对数据进行消费

```java
public static void handleInterger(Integer i, Consumer<Integer> con1, Consumer<Integer> con2) {
    con1.andThen(con2).accept(i);
}
```

在`mian`方法中使用，`con1`对数字进行`+10`然后打印，`con2`对数字进行`*10`然后打印

```java
handleInteger(10, (i) -> {
    i = i + 10;
    System.out.println(i);
}, (i) -> {
    i = i * 10;
    System.out.println(i);
});

```

输出为

```java
20
100

```

### Predicate\<T>

`Predicate`接口中有一个`test(T t)`，它的作用是对某种数据类型进行判断，它返回一个`boolean`值。假设有一个集合，我们对其中的元素进行判断，符合条件放入一个新的集合，看下面的方法。

```java
public static HashMap<String, Integer> getMap(HashMap<String,Integer> map, Predicate<Integer> predicate) {
    //创建一个集合用以放符合条件的元素
    HashMap<String,Integer> resmap = new HashMap<>();
    //遍历集合
    Set<String> key = map.keySet();
    for (String str : key) {
        int val = map.get(str);
        //对值进行判断
        boolean res = predicate.test(val);
        //如果值符合条件，就加入新的集合
        if (res) {
            resmap.put(str,val);
        }
    }
    
    return resmap;
}

```

现在我们在`main`方法中进行调用

```java
HashMap<String,Integer> map = new HashMap<>();
map.put("迪丽热巴",18);
map.put("古力娜扎",19);
map.put("佟丽娅",20);
map.put("奥特曼",100);
//筛选出年龄小于等于20岁的
HashMap<String,Integer> resmap = getMap(map, (Integer i) -> {
   if (i <= 20) {
       return true;
   }
   return false;
});
System.out.println(resmap);

```

运行结果为

```java
{佟丽娅=20, 迪丽热巴=18, 古力娜扎=19}

```

`Predicate`还有三个默认方法

- `and(Predicate\<T> pre)`
  - 与
- `or(Predicate\<T> pre)`
  - 或
- `negate()`
  - 非

假设对于上面的那个方法，我提出一个新的需求，要求不仅年龄要小超过`20`岁，而且年龄要大于`18`

```java
public static HashMap<String, Integer> getMap(HashMap<String,Integer> map,
                                              Predicate<Integer> predicate1,
                                              Predicate<Integer> predicate2) {
    HashMap<String,Integer> resmap = new HashMap<>();
    Set<String> key = map.keySet();
    for (String str : key) {
        int val = map.get(str);
        boolean res = predicate1.and(predicate2).test(val);
        if (res) {
            resmap.put(str,val);
        }
    }
    return resmap;
}

```

在`main`方法中调用该方法

```java
HashMap<String,Integer> resmap = getMap(map, (Integer i) -> {
   if (i <= 20) {
       return true;
   }
   return false;
}, (Integer i) -> {
    if (i > 18) {
        return true;
    }
    return false;
});
System.out.println(resmap);

```

输出结果为

```java
{佟丽娅=20, 古力娜扎=19}

```

至于`or()`和`negate()`的使用方法同上。

### Function\<T,R>

该接口的作用是将`T`这种数据类型转化为`R`这种数据类型，它里面有一个`R apply(T t)`方法。下面这个方法将一个字符串转化为一个整数

```java
public static Integer StrToInt(String str, Function<String, Integer> fun) {
     return fun.apply(str);
}

```

在`main()`方法中调用该方法

```java
Integer integer = StrToInt("123", (String str) -> {
    //将字符串转化为数字
    return Integer.parseInt(str);
});
//打印该数字
System.out.println(integer);

```

输出为

```java
123

```

`Function`接口中还有一个默认方法`andThen(Function<T,R> fun)`，这个方法与在上面介绍的`Consumer`接口的`andThen()`很像，但是有点不同，`Consumer`接口的`andThen`是两个对象消费同一个数据，而`Function`接口的`addThen()`是将第一个`fun`处理后的结果拿给第二个`fun`去处理，相当于`apply(apply())`。比如现在我有一个需求，将一个字符串转化为数字，然后将这个数字，`+10`然后再转化为字符串，这个方法可以这么写

```java
public static String StrPlus(String str, 
                             Function<String,Integer> fun1, 
                             Function<Integer,String> fun2) {
    return fun1.andThen(fun2).apply(str);
}

```

我们在`main`方法中调用该方法

```java
String s =  StrPlus("123", (String str) -> {
    //将字符串转化为数字并加10
    Integer i = Integer.parseInt(str);
    i = i + 10;
    return i;
}, (Integer i) -> {
    //将数字转化为字符串
   return i + "";
});
System.out.println(s);

```

输出为

```java
133
```

## Stream流

`Stream`流是`Java 8`引入的新特性，首先它跟`I/O`流没有任何的关系，它主要是用来处理集合、数组问题的。要看`Stream`流有什么用处，还是要看集合处理有什么缺点。

### 问题引出

有下面这么一个数组

```java
String[] strings = {"张无忌", "张三丰", "赵敏", "张翠山", "小昭", "张良"};
```

现在我们有如下要求

- 筛选出以"张"字开头的字符串，放入一个`Arraylist`集合中
- 在`ArrayList`集合中筛选出字符串长度为`3`的字符串，放入一个新的集合

```java
ArrayList<String> list1 = new ArrayList<>();
for (String string : strings) {
    if (string.startsWith("张")) {
        list1.add(string);
    }
}
System.out.println(list1);
ArrayList<String> list2 = new ArrayList<>();
for (String string : list1) {
    if (string.length() == 3) {
        list2.add(string);
    }
}
System.out.println(list2);
```

输出为

```java
[张无忌, 张三丰, 张翠山, 张良]
[张无忌, 张三丰, 张翠山]
```

现在我们使`Stream`流的方式实现

```java
Stream<String> stream = Stream.of(strings);
stream.filter(str -> str.startsWith("张"))
        .filter(str -> str.length() == 3)
        .forEach(str -> System.out.print(str + " "));
```

输出为

```java
张无忌 张三丰 张翠山 
```

我们发现使用`Stream`流的代码比遍历集合简单很多，因为使用集合直接遍历真正核心的代码就那么一两句，比如

```java
for (String string : strings) {
    if (string.startsWith("张")) {
        list1.add(string);
    }
}
```

这些代码中核心的就是`string.startsWith("张")`，而其他的代码是为了达到这个目的不得不写的代码。这就是集合相较于`Stream`流的局限性所在，观察`Stream`流的写法，根本没有什么遍历集合的代码，直接就是你想要干的事情。

### 获取Stream流的方法

获取`Stream`流有两种方法

- `Collection`中新加的`stream()`方法，该方法可以得到一个`Stream`流，对于`Map`集合，可以通过`keySet(),values(),entrySet()`等方法得到`Set`集合，然后通过`Set`对象调用`stream()`方法得到`Stream`流
- `Stream`流的静态方法`of()`，该方法接收一个可变参数，所以可以传入一个数组

下面做一个演示

```java
import java.util.*;
import java.util.stream.Stream;

public class getStream {
    public static void main(String[] args) {
        ArrayList<String> list = new ArrayList<>();
        list.add("迪丽热巴");
        list.add("古力娜扎");
        list.add("哪吒");
        list.add("杨戬");
        Stream stream1 = list.stream();
        stream1.forEach(s -> System.out.println(s));

        HashMap<String,String> map = new HashMap<>();
        map.put("迪丽热巴","女");
        map.put("古力娜扎","女");
        map.put("哪吒","男");
        map.put("杨戬","男");
        Set<String> key = map.keySet();
        Stream stream2 = key.stream();
        stream2.forEach(s -> System.out.println(s));

        Collection<String> vals = map.values();
        Stream stream3 = vals.stream();
        stream3.forEach(s -> System.out.println(s));

        Set<Map.Entry<String,String>> entries = map.entrySet();
        Stream stream4 = entries.stream();
        stream4.forEach(s -> System.out.println(s));

        String[] strings = {"迪丽热巴", "古力娜扎", "哪吒", "杨戬"};
        Stream stream5 = Stream.of(strings);
        stream5.forEach(s -> System.out.println(s));
        
        Stream<Integer> stream6 = Stream.of(1,2,3,4,5);
        stream6.forEach(s -> System.out.println(s));
    }
}
```

### Stream中的常见方法

`Stream`流中的方法分为两类，一类叫做延迟方法，该方法返回的还是一个`Stream`流对象，所以可以进行链式编程，如`filter()`；另一类叫做终结方法，该方法不返回`Stream`流对象，如`forEach()`， `count()`(终结方法只有这两个，其他的都是延迟方法)。

#### filter()

该方法需要传入的是一个`Predicate\<T>`接口，这个接口我们在常用函数式接口讲过，它是对某中数据进行测试，而`filter`的作用就是如果`test(T t)`返回的是`true`，那么就将这个数据加入到新的流中，遍历完流中所有的元素后返回。

```java
//当字符串以迪开头时返回true，加入到新的流中，这个流会被返回
Stream<String> stream1 = stream.filter(s -> s.startsWith("迪"));
//forEach是后面要介绍的方法，这里只需要理解为遍历流并打印
stream1.forEach(s -> System.out.println(s));
```

输出为

```java
迪丽热巴
```

#### map()

该方法传入的是一个`Function<T,R>`接口，所以它的作用是将一个类型的转转化为另一个类型的流。如下

```java
//得到一个流，这个流是字符串的长度
Stream<Integer> stream1 = stream.map(s -> s.length());
stream1.forEach(s -> System.out.println(s));
```

这时结果报错了

<center>
<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java43.png"/>
</center>

这是因为这个`stream`在调用上面的`filter()`的时候已经使用过了，而流使用了一次就会关闭，不能在使用，这就是为什么会报错的原因，所以我们把代码改为

```java
//得到一个流，这个流是字符串的长度
Stream<Integer> stream2 = Stream.of(strings).map(s -> s.length());
stream2.forEach(s -> System.out.println(s));
```

这时输出为

```java
4
4
2
2
```

#### forEach

该方法传入的是一个`Consumer\<T>`接口，是一个终结方法，该方法会遍历流中的元素，然后使用`Consumer`接口中的`accept()`方法对元素进行处理，比如

```java
stream.forEach(s -> System.out.println(s));
```

会逐个打印出流中的元素。

#### limit

`limit`方法需要传入一个`long`类型的数值`maxSize`，该方法会截取流中的前`maxSize`个元素放到新流中并返回，如

```java
//这是链式编程
Stream.of(strings).limit(2).forEach(s -> System.out.println(s));
```

输出为

```java
迪丽热巴
古力娜扎
```

#### skip

该方法接收一个`long`类型的数据`n`，它会跳过流中的前`n`个元素，将剩下的元素放入到一个新流中并返回，如

```java
Stream.of(strings).skip(2).forEach(s -> System.out.println(s));
```

输出为

```java
哪吒
杨戬

```

#### count

该方法不需要传入参数，返回一个`long`类型的整数，该整数是流中元素的个数，这个方法是一个终结方法，不返回`Stream`流

```java
long num = Stream.of(strings).count();
System.out.println(num);

```

输出为

```java
4

```

## 方法引用

我们之前在`Stream`流使用`forEach()`去打印流中的元素，如

```java
stream.forEach(s -> System.out.println(s));
```

但是打印这个方法`(System.out.println())`是已经存在了的，我们可不可以直接传入这个方法，在这里或者说是引用这个方法，答案是可以的，如下

```java
stream.forEach(System.out::println);
```

在这里我们引用了`System.out`对象的`println`方法，这行语句的作用是上面的语句作用是完全相同的，这就是方法的引用，`::`就是方法引用的运算符，这是新增的运算符。

那方法引用也要遵循一定的原则，比如你引用的对象必须是存在的，你引用的方法需要传入的参数的个数和类型必须是对的上的，否则就会抛出异常，由于方法的性质不同，所以有很多类型的引用，比如

- 对象引用成员方法
- 类引用静态方法
- `super`引用父类方法
- `this`引用成员方法
- 引用构造方法
- 引用数组构造方法

下面会详细的展开讲解。

### 对象引用成员方法

其实

```java
stream.forEach(System.out::println);
```

就是对象引用成员方法，我们引用了`System.out`对象的成员方法`println`。

### 类引用静态方法

假设有一个接口`Calculate`，里面只有一个抽象方法`cal(int i)`

```java
public interface Calculate {
    int cal(int i);
}
```

所以这是一个函数式接口，现在在有一个方法需要调用这个接口去得到一个数字的绝对值，如

```java
public static int getAbs(int i, Calculate calculate) {
    return calculate.cal(i);
}
```

我们知道`Math`类的静态方法`abs()`可以做到这件事情，所以我们可以直接引用这个方法，如

```java
int num = getAbs(-10,Math::abs);
System.out.println(num);
```

输出结果为

```java
10
```

### super引用父类成员方法

假设有一个`Greet`接口，里面只有一个抽象方法`greet()`，所以这是一个函数接口

```java
public interface Greet {
    void greet();
}
```

现在有一个父类`Person`，里面有一个`greet()`方法，这个方法在后面是要被子类引用的

```java
public class Person {
    String name;

    public Person(String name) {
        this.name = name;
    }

    public Person() {
    }

    public void greet() {
        System.out.println("I'm " + name);
    }
}
```

现在有一个子类`Student`继承了`Person`类

```java
public class Student extends Person {

    public Student(String name) {
        super(name);
    }

    public static void sayHello(Greet gre) {
        gre.greet();
    }

    public void greet() {
        sayHello(super::greet);
    }
}
```

`Student`中的`sayHello()`方法需要一个`Greet`接口，然后我们又在`greet()`方法中调用了这个方法，并且传入一个`super::greet`的方法引用(当然这样的代码没有什么意义，只是为了演示)，我们在main中创建一个对象，并调用此方法

```java
Student student = new Student("小明");
student.greet();
```

输出为

```java
I'm 小明
```

### this引用成员方法

还是以上面的`Student`类为例，假设`Student`类中有一个成员方法为

```java
public void tempt() {
    System.out.println("我今晚有空哦");
}
```

然后在`greet()`方法中再增加一个`sayHello()`，这时方法的引用指向的是`tempt`方法，如下

```java
public void greet() {
    sayHello(super::greet);
    sayHello(this::tempt);
}
```

现在在`main`方法中运行一下，输出为

```java
I'm 小明
我今晚有空哦
```

### 引用构造方法

现在假设有这么一个接口

```java
public interface Personable {
    Person getPerson(String name);
}

```

里面只有一个抽象方法`getPerson`，所以这是一个函数式接口，该方法根据`name`返回一个`Person`对象，现在有一个方法需要传入这个接口得到一个`Person`对象

```java
public static Person getPerson(String name, Personable personable) {
    return personable.getPerson(name);
}

```

现在我们在`main`方法中调用该方法，传入的接口我们使用构造器引用`Person::new`

```java
Person person = getPerson("迪丽热巴",Person::new);
person.greet();

```

运行输出为

```java
I'm 迪丽热巴

```

### 引用数组构造方法

引用数组构造方法的格式是`int[]::new`(这里只以`int`为例，当然也可以`double[]::new`)，具体的使用方法同上面的`Person`类的构造方法引用一致，这里就不多加介绍了。



<Disqus />