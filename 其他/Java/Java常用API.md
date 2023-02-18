---
title: Java常用API
author: 熊滔
commentid: javase:use-most-api
---

API全称叫做Application Programming Interface，翻译成应用程序编程接口，其实你把它看做是Java的使用说明书进行，它告诉你Java有哪些类，有哪些方法，你直接用就可以，相当于给你一个插座(接口)，你用的时候插上去就行。

# Scanner

我们之前做的都是从屏幕输出，现在将介绍如何获取从键盘输入。Scanner类是系统提供的一个类，它可以帮助我们从键盘获取输入。一般使用系统提供的类分为三部分

- 导包
- 创建对象
- 使用

导包语句放在package之后，放在public class之前，如果使用的类与当前类属于同一个包，那么不用导入，如果要使用的类在java.lang包下，也不需要导入。导包的格式为

```java
import 包名.类名; //导入指定包下面的类
import 包名.*;  //导入指定包下的所有类
```

Scanner类位于java.util包下，所以我们需要导入，下面介绍如何使用Scanner类

```java
import java.util.Scanner;  //1. 导包

public class TestScanner {
    public static void main(String[] args) {
        //2. 创建一个Scanner对象
        //Scanner的构造方法需要传入参数，这里传入的是System.in，代表的是从键盘输入
        Scanner sc = new Scanner(System.in);
        
        //3. 使用
        int num = sc.nextInt();   //获得从键盘输入的一个整数
        String str = sc.next();   //获得从键盘输入的一个字符串
        //打印输入的结果
        System.out.println(num);
        System.out.println(str);
    }
}
```

程序运行效果为：

<img src="https://cdn.staticaly.com/gh/LastKnightCoder/ImgHosting3@master/Java16.3ckb5dr3gfq0.gif"/>

**注意：**

- 其实从键盘输入全部都是字符串，即使你输入的是65这里的整数，系统得到的只是对于的ASCII码值，而nextInt()方法之所以能获得整数，是因为nextInt()方法做了处理，将字符串转化为了整数。
- next()方法只能获得一个字符串，如输入的是Hello World，它只能得到Hello。

<img src="https://cdn.staticaly.com/gh/LastKnightCoder/ImgHosting3@master/Java17.5k1yvwkrcg00.gif"/>

# Random

Random类的作用主要是用于产生随机数的，它位于java.util包下面。下面介绍它的两个主要的方法

- nextInt()：产生一个随机整数，范围时整个int的大小
- nextInt(int n)：产生一个[0,n)的整数，左闭右开。

下面将介绍Random的使用。下面这个程序将产生10个随机整数，范围为整个int整数的范围

```java
import java.util.Random;

public class TestRandom {
    public static void main(String[] args) {
        Random r = new Random();
        for (int i = 0; i < 10; i++) {
            System.out.println(r.nextInt());
        }
    }
}

```

程序输出为

<img src="https://cdn.staticaly.com/gh/LastKnightCoder/ImgHosting3@master/Java18.66kiw2i3txs0.gif"/>

可见产生的整数是随机的，并且范围时整个int的范围，正值和负值都有。

下面这个程序将产生10范围为1-10的随机整数

```java
import java.util.Random;

public class TestRandomAgain {
    public static void main(String[] args) {
        Random r = new Random();
        for (int i = 0; i < 10; i++) {
            System.out.println(r.nextInt(10) + 1); //nextInt(10)的范围为0-9，+1变为1-10
        }
    }
}

```

程序输出为

<img src="https://cdn.staticaly.com/gh/LastKnightCoder/ImgHosting3@master/Java19.1pe1h3uf2s00.gif"/>

输出的数字的范围在1-10之间，并且输出的数字是随机的。

# String

String这个类是我们最常用的一个类了，因为我们会牵涉到很多的字符串的操作，所以这里要详细讲一下String类。

## String类的定义

String类位于java.lang包中，我们在之前讲过，java.lang是默认导入的，所以我们不需要导入这个包，这也是为什么在之前我们可以直接的使用String这个类。字符串效果上相当于是一个字节数组。

String作为引用类型，所以String对象的创建当然要借助于构造方法了，String的构造方法有很多，这里只讲常见的三种String构造方法。

- public String()
  - 创建一个空字符串
- public String(char[] array)
  - 根据一个字符数组来创建一个字符串
- public String(byte[] array)
  - 根据一个字节数组来创建一个字符串

下面我将演示通过这三种方法创建字符串对象。

```java
public class TestString {
    public static void main(String[] args) {
        String str1 = new String(); //""
        char[] chars = {'a', 'b', 'c'};
        String str2 = new String(chars); //"abc"
        byte[] bytes = {97, 98, 99};
        String str3 = new String(bytes); //"abc"

        System.out.println(str1);
        System.out.println(str2);
        System.out.println(str3);

    }
}
```

输出为

```java
abc
abc
```

因为字符串是在是太常用，通过构造方法创建有点麻烦，所以系统设计出可以通过`""`的字面量的形式来创建一个String对象，这也是我们经常使用的方式

```java
String str = "abc";
```

**注意：**

- Java程序中所有字符串的字面值（`""`）都是String类的实例
- 字符串一旦创建不可改变

## 常量池

我们知道可以通过字面量（`""`）的形式来创建字符串对象，这样创建对象与使用构造方法创建的对象有什么不同呢? 下面我们来看一个字符串比较的例子

```java
public class ConstantString {
    public static void main(String[] args) {
        String str1 = "abc";
        String str2 = "abc";

        String str3 = new String(new char[]{'a','b','c'});
        String str4 = new String(new byte[]{97, 98, 99});

        System.out.println(str1);
        System.out.println(str2);
        System.out.println(str3);
        System.out.println(str4);
    }
}
```

输出为

```java
abc
abc
abc
abc
```

在这里我们创建了四个字符串，他们的内容都是"abc"，现在我要对它们进行比较

```java
System.out.println(str1 == str2);
System.out.println(str1 == str3);
System.out.println(str1 == str4);
System.out.println(str3 == str4);
```

输出结果为

```java
true
false
false
false
```

这里得到的结果可能与你想象的不一样，所以我要详细讲一下。

首先比较符比较的是什么？由于字符串都是引用类型，所以这里比较的是它们的地址，那按道理说，每创建一个对象，会在堆中开辟一个空间，每个空间的地址都不一样，那么它们比较的值应该都是false，那么为什么通过字面量创建的字符串对象比较出来的结果是true呢？

要解释这一个现象，就需要知道一个东西，那就是常量池。程序中直接用双引号写上的（即通过字面量创建的字符串），都在常量池中，而new出来的对象不再常量池中。现在记住这一句话，我们去内存看看到底怎么回事，由于这次不牵涉到方法区，我们只画出栈内存和堆内存。

<img src="https://cdn.staticaly.com/gh/LastKnightCoder/ImgHosting3@master/Java20.5noxcc87lts0.webp" style="zoom: 50%;"/>

## 字符串的相关方法

### 字符串比较

之前我们说比较是基于地址的比较，但是我们如果基于内容比较怎么办？比如说上面的字符串如果进行比较的话，就会返回true。有两个方法，第一个是equals()方法，该方法其实也是基于比较的，不过String类重写了该方法，只要两个字符串的内容相同就会返回true。这里又提到了重写，不懂没关系，只要知道，字符串调用这个方法是基于内容的比较，而不是基于地址的比较，下面演示一番。

```java
public class CompareString {
    public static void main(String[] args) {
        String str1 = "abc";
        String str2 = "abc";
        String str3 = new String(new char[]{'a','b','c'});
        String str4 = new String(new byte[]{97, 98, 99});

        System.out.println(str1.equals(str2));
        System.out.println(str1.equals(str3));
        System.out.println(str1.equals(str4));
        System.out.println(str3.equals(str4));
    }
}
```

输出为

```java
true
true
true
true
```

注意：

- equals()方法具有对称性
- 如果比较的双方一个是常量，一个是变量，推荐使用常量调用方法

针对第二条的原因是，如果变量str未被初始化，那么它的初始值是null，null根本没有equals方法，所以调用该方法会报错

```java
String str; //str = null
str.equals("abc"); //会报错
"abc".equals(str); //不会报错，会返回false
```

字符串比较的第二个方法是equalsIgnoreCase()，该方法与equals不同的是，该方法的比较忽略大小写，而equals()是大小写敏感的。

```java
System.out.println("hello".equalsIgnoreCase("Hello"));
```

输出为

```java
true
```

### 字符串截取

与字符串截取有关的方法是substring()，该方法有两种常用的重载

- substring(int index)
  - 从index截取到末尾
- substring(int begin, int end)
  - 从begin截取到end，左闭右开[begin, end)

### 字符串转换

介绍三个方法

- toCharArray()
  - 将字符串转换为char[]
- getBytes()
  - 将字符串转换为byte[]
- replace(CharSequence oldString, CharSequence newString)
  - 这里的CharSequence是接口，这里不懂也没关系，知道这个可以接收字符串类型就可以
  - 这个方法的作用是新的字符串替换旧的字符串

### 字符串分割

与字符串分割的方法只有一个

- split(String regex)
  - 按照regex的规则分割字符串，返回一个字符串数组

按照regex的规则分割字符串可能有点难以理解，其实这里的regex是正则表达式，不懂的话看懂下面的例子就可以

```java
public class SplitString {
    public static void main(String[] args) {
        String str = "a,b,c";

        String[] strings = str.split(","); //按照逗号的分割字符串，得到的是["a","b","c"]
        for (int i = 0; i < strings.length; i++) {
            System.out.println(strings[i]);
        }
    }
}
```

输出为

```java
a
b
c
```

注意：

- 如果要按"."规则分割的话，不能写"."，要写成"\\."，因为.在正则表达式中有特殊的含义，所以需要转义。

# Arrays

Arrays位于java.util包中。这个类提供了很多的静态方法，实现数组的常见操作。在这里我们介绍两个常用的方法

- toString
  - 接收一个数组参数
  - 将参数数组变成字符串 [元素1，元素2， ...]
- sort
  - 按默认升序（从小到大）对数组进行排序
  - 对于String，按字母在Unicode表中的大小排序
  - 对于自定义的类型，需要Comparable或Comparator接口的支持

下面演示两个方法的使用

```java
import java.util.Arrays;

public class TestArrays {
    public static void main(String[] args) {
        //创建一个整型数组
        int[] arrays = {5, 8, 4, 12, 3, 7};
        //调用Arrays.toString() 我们不用遍历数组打印了
        System.out.println(Arrays.toString(arrays));
        
        //对数组进行排序 这里不是返回一个新的数组 而是对原有数组进行排序
        Arrays.sort(arrays);
        //将排序后的数组打印出来
        System.out.println(Arrays.toString(arrays));
    }
}
```

输出为

```java
[5, 8, 4, 12, 3, 7]
[3, 4, 5, 7, 8, 12]
```

# Math

Math类位于java.util包中，该类包含了很多与数学计算相关的静态方法。这里介绍几个与整数操作有关的方法

- abs()
  - 取绝对值
  - abs(-2) = 2
- ceil()
  - 向上取整
  - ceil(2.1) = 3，ceil(-2.1) = -2
- floor()
  - 向下取整
  - ·floor(2.1) = 2，floor(-2.1) = -3
- round()
  - 四舍五入
  - round(2.1) = 2，round(-2.1) = -2

当然Math类还包含很多的方法，具体的可以查阅资料。

# Object

Object类是所有类的父类，所有类都默认继承了（直接或间接）Object类。所以所有的类都默认有Object类中的成员方法。这里介绍两个比较重要的Object类的成员方法

- toString()
- equals(Object object)

## toString

我们在之前一直有用System.out.println()语句打印信息到控制台，当我们传入一个引用类型变量的时候，它会调用该对象的toString()方法，由于Object类的toString方法是默认打印堆内存的地址值，所以这也是为什么我们在打印数组时，打印出的是地址值。而我们打印String类型时，打印出的却是它的内容，这时因为String重写toString()方法。

为了验证我们的猜想，我们看下面这么一个类

```java
public class Person {

}
```

我们现在创建一个Person对象，并且打印出来，然后调用toString()方法，在打印一遍

```java
public class TestToString {
    public static void main(String[] args) {
        Person per = new Person();
        System.out.println(per);
        System.out.println(per.toString());
    }
}
```

输出为

```java
Person@4554617c
Person@4554617c
```

我们发现结果是一样的，现在我们在Person类中重写toString()方法

```java
public class Person {
    @Override
    public String toString() {
        return "I'm a Person object";
    }
}
```

在运行一遍，输出为

```java
I'm a Person object
I'm a Person object
```

## equals

在前面我们比较字符串时，我们说==是对象的地址值进行比较，而equals方法是基于内容的比较。事实上，Object类的equals方法也是进行对象地址值的比较，只不过是**String类重写了equals方法**。

我们可以通过重写equals方法来设置怎样两个对象才是相等的。比如还是以Person类进行举例，它有姓名和年龄两个成员变量，我们认为如果两个人的姓名和年龄都是一样的，那么我们就认为这两个人的对象是相同的

```java
public class Person {

    String name;
    int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public boolean equals(Object obj) {
        //因为要使用Person对象的name和age属性，所以要向下转型
        if (obj instanceof Person) {
            Person person = (Person) obj;
            //如果姓名和年龄相同，就认为对象是相同的
            if (this.name == person.name && this.age == person.age) {
                return true;
            }
        }
        return false;
    }
}

```

现在我们在测试类类中测试一下

```java
public class TestEquals {
    public static void main(String[] args) {
        Person person1 = new Person("迪丽热巴",20);
        Person person2 = new Person("古力娜扎",28);
        Person person3 = new Person("迪丽热巴",20);

        System.out.println(person1.equals(person2));  //年龄和姓名不一样，返回false
        System.out.println(person1.equals(person3)); //名字和年龄一样，返回true
        System.out.println(person1 == person3); //地址值是不同的，所以是false
    }
}
```

输出为

```java
false
true
false
```

# Date

Date是有关于日期的类，它位于java.util包中。现在主要介绍它的两种常用的构造方法，以及一个有关于格式化输出的类SimpleDateFormat。

## 构造方法

```java
Date date = new Date();  //默认得到的是当前时间的日期
Date date = new Date(long l); //接收一个毫秒值，该毫秒值代表的是距离时间原点消耗的毫秒值
```

时间原点规定为1970年1月1日的`00:00:00`时刻。现在简单演示Date类的使用

```java
import java.util.Date;

public class TestDate {
    public static void main(String[] args) throws ParseException {
        Date date = new Date();
        System.out.println(date);
    }
}
```

输出为

```java
Sat Jul 20 19:26:26 CST 2019
```

可见Date类重写了toString()方法。

Date类有一个getTime的方法，它可以获得该date对象对应的毫秒值，现在我们通过这个方法获得一个毫秒值，然后利用该毫秒值作为第二个构造函数的参数

```java
import java.util.Date;

public class TestDate {
    public static void main(String[] args) throws ParseException {
        Date date = new Date();
        System.out.println(date);
        
        long ms = date.getTime();
        Date date2 = new Date(ms);
        System.out.println(date2);
    }
}

```

输出为

```java
Sat Jul 20 19:29:24 CST 2019
Sat Jul 20 19:29:24 CST 2019
```

## SimpleDateFormat

上面的时间输出其实不符合我们的使用习惯，所以我们可以让输出的日期符合我们的使用习惯。DateFormat这个类是为此而存在的，不过它是一个抽象类，SimpleDateFormat类继承了该类，我们可以使用该类格式化日期输出。

首先就是如何创建一个对象，如下

```java
SimpleDateFormat simpleDateFormat = new SimpleDateFormat(模式);
```

可能这里还不能理解模式是个什么鬼，那么就要看下面这个例子

```java
SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy年MM月dd日");
```

"yyyy年MM月dd日"就是模式，差不多就是模板吧，也就是你想要格式化输出的格式，其中yyyy代表年，MM代表月，dd代表日，HH代表小时，mm代表分，ss代表秒。

这里主要介绍它的两个方法

- format
  - 接收一个日期对象，返回一个字符串，这个字符串的格式与你上面定义的模式相同
- parse
  - 接收一个字符串，这个字符串的格式必须与你定义的模式相同，否则会报错
  - 方法一个Date对象
  - parse方法会抛出一个异常，对于抛出异常的方法，要么继续抛出异常，要么使用try-catch处理

下面介绍这个类的使用

```java
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;

public class SimpleDate {
    public static void main(String[] args) throws ParseException {  //parse方法有可能抛出这个异常，这里我们不处理，继续抛出
        Date date = new Date();  //创建一个Date对象作为后面format方法的参数
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss"); //定义输出格式
        String formatDate = simpleDateFormat.format(date);  //获得由date转化得到的格式化输出字符串
        System.out.println(formatDate);

        Date dateAgain = simpleDateFormat.parse("2017年5月13日 15:23:14"); //根据指定的格式解析出一个Date对象
        System.out.println(dateAgain); //打印该Date对象
    }
}
```

输出为

```java
2019年07月20日 19:43:18
Sat May 13 15:23:14 CST 2017
```

# Calendar

Calendar是一个有关于日期的类，它提供了一些操作日历的方法，它是一个抽象类，所以不能创建Calendar对象，我们可以通过它的静态方法getInstance()得到它的一个子类对象，如下

```java
Calendar calendar = Calendar.getInstance();
```

这里仅简单介绍它的四个方法

- get(int field)
  - 根据指定field值获取日历信息，field值一般为Calendar类的静态常量，如get(Calendar.YEAR)可以获得有关于年份的信息
- set(int field, int value)
  - 设置指定的field为指定的value
- add(int field, int amount)
  - 为指定field加上amount
  - 这里需要注意的是，如果加上数值之后超过范围之后，会有进位。比如现在为7月，我给MONTH加上了10，那么现在年份会加1年，并且此时的月份为7 + 10 -12 = 5。
- getTime()
  - 返回当前日历对应得Date对象

下面简单演示方法的使用

```java
import java.util.Calendar;
import java.util.Date;

public class TestCalendar {
    public static void main(String[] args) {
        Calendar calendar = Calendar.getInstance();
        System.out.println(calendar); //打印出的不是地址，可见Calendar也重写了toString()方法

        System.out.println(calendar.get(Calendar.YEAR));
        System.out.println(calendar.get(Calendar.MONTH) + 1); //西方的月份是从0开始的，所以这里我加1
        System.out.println(calendar.get(Calendar.DATE));
        System.out.println(calendar.get(Calendar.DAY_OF_MONTH)); //与上面DATE的效果是一样的
        System.out.println(calendar.get(Calendar.HOUR));
        System.out.println(calendar.get(Calendar.MINUTE));
        System.out.println(calendar.get(Calendar.SECOND));

        System.out.println("=============");
        calendar.set(Calendar.YEAR, 2020);  //设置年份为2020年
        System.out.println(calendar.get(Calendar.YEAR));
        calendar.add(Calendar.MONTH,10);  //给月份加10个月，现在为2021年5月了
        System.out.println(calendar.get(Calendar.MONTH) + 1);
        Date dateAgain = calendar.getTime();  //得到一个日期对象
        System.out.println(dateAgain);
    }
}
```

输出为

```java
java.util.GregorianCalendar[time=1563623852127,areFieldsSet=true,areAllFieldsSet=true,lenient=true,zone=sun.util.calendar.ZoneInfo[id="Asia/Shanghai",offset=28800000,dstSavings=0,useDaylight=false,transitions=29,lastRule=null],firstDayOfWeek=1,minimalDaysInFirstWeek=1,ERA=1,YEAR=2019,MONTH=6,WEEK_OF_YEAR=29,WEEK_OF_MONTH=3,DAY_OF_MONTH=20,DAY_OF_YEAR=201,DAY_OF_WEEK=7,DAY_OF_WEEK_IN_MONTH=3,AM_PM=1,HOUR=7,HOUR_OF_DAY=19,MINUTE=57,SECOND=32,MILLISECOND=127,ZONE_OFFSET=28800000,DST_OFFSET=0]
2019
7
20
20
7
57
32
=============
2020
5
Thu May 20 19:57:32 CST 2021
```

# System

System是有关于系统的类，这里不会介绍那么高深的内容，主要介绍两个静态方法

- currentTimeMillis()
  - 获得当前系统距时间原点的毫秒值
  - 这个方法可以用来计算程序损耗的实践
  - 在程序开始执行前获取一个时间，在程序执行完成获取一个时间，两个时间相减就可以知道程序执行的时间，就可以知道程序的哪一部分最耗时，从而做出优化
- arraycopy()
  - 该方法的作用是将源数组从指定位置开始复制，有一个参数规定了复制的长度，复制到另一个数组，这个数组也规定了起始的位置
  - 该方法接收五个参数
  - 第一个参数是一个源数组src，第二个参数是源数组的起始位置，第三个参数是目的数组，第四个参数是目的数组的起始位置，第五个参数是复制的长度
  - 如果你在这里没有理解，请看下面的例子

下面简单演示这两个方法的使用

```java
import java.util.Arrays;

public class TestSystem {
    public static void main(String[] args) {
        long start = System.currentTimeMillis(); //循环执行前获取一次时间
        int sum = 0;
        for (int i = 0; i < 10000000; i++) { //1000万次
            sum = sum + i;
        }
        long end = System.currentTimeMillis();  //循环结束后获取一次时间
        double time = (end - start) / 1000.0;  //将单位转化为s
        System.out.println("共花费" + time + "s");

        //我们将源数组的前三个元素替换目的数组的前三个元素
        int[] array1 = {1, 2, 3, 4, 5};      //源数组
        int[] array2 = {6, 7, 8, 9, 10};     //目的数组
        System.out.println("转换前");
        System.out.println(Arrays.toString(array1));
        System.out.println(Arrays.toString(array2));
        System.arraycopy(array1, 0, array2, 0, 3); //前三个元素，所以都是从索引0开始
        System.out.println("转换后");
        System.out.println(Arrays.toString(array1));
        System.out.println(Arrays.toString(array2));
    }
}
```

输出为

```java
共花费0.011s
转换前
[1, 2, 3, 4, 5]
[6, 7, 8, 9, 10]
转换后
[1, 2, 3, 4, 5]
[1, 2, 3, 9, 10]
```

可见程序是执行的相当的快。

<Disqus />