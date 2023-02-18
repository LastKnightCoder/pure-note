---
title: Java面向对象
author: 熊滔
commentid: javase:oriented-object
---

在最初的开始，人们编写程序的基于过程的，那时候的编程方式是面向过程的。但是人们发现，随着代码规模的扩大，编写大规模的程序使用面向过程的方法十分的困难，这时候人们必须提出一种新的编程思想，使得编写大型程序变得简单。这种思想就是面向对象的思想。

就像我们学习数学一样，随着我们学习的深入，我们一直在进行抽象，比如从数抽象到代数，并且不断提出新的概念，提出很多的定理，方便我们的学习研究。面向对象也是一样，它把实际中的事物抽象出来，这个事物可以是我们见到的实物，比如椅子，桌子，手机，也可以是我们我们看不见的东西，比如某个系统。这个抽象出来的东西我们叫做类，我们利用类的概念，可以清楚的把握类与类之间的关系，使得程序的结构十分的清晰，便于管理，便于开发大型的程序。

我们把抽象出来的东西叫做类，那么一个类的实例就是对象。比如，我们把人抽象为了一个类，这是一个抽象的概念，那么小明这个具体的人就是该类的一个实例，也叫做对象。我们把椅子抽象为一个类，一个具体的椅子就是一个对象。类可以看做是对象的模板，对象可以看做是类的具体实现。

我们可以通过两个方面是描述一个事物，一个是属性，比如说对于人这个类，它的属性就有姓名，身高，年龄等等，这些都是它的属性，另一个就是行为，比如说人的行为有吃饭，睡觉等等。

## 类的定义

类使用关键字class进行定义，我们在之前用过很多次，但是我们之前不知道这是什么，比如在HelloWorld案例中

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello World!");
    }
}
```

其中HelloWorld表示的就是类名。类定义的格式是

```java
public class 类名 {
    //成员变量
    
    //成员方法
}
```

在上面我们有提到，一个事物可以由属性和行为两部分描述，表现在程序书写中为成员变量和成员方法，其中变量用来描述类的属性，方法用来描述类的行为，前面加上成员二字以表示与局部变量的不同，例如下面定义了一个Person类，它有姓名，年龄属性，有吃和睡的行为

```java
public class Person {
    String name;
    int age;
    
    public void eat() {
        System.out.println("吃吃吃");
    }
    public void sleep() {
        System.out.println("睡睡睡");
    }
}
```

我们注意到成员变量时定义在类当中的，并且成员方法没有使用关键字static修饰。

我们定义了一个类，那我们怎么使用它呢? 从前面我们了解到，类是一个抽象的概念，是一个模板，我们要将它具体化才能够使用它，类是不能够直接使用的，而具体化的过程就是创建一个对象。创建对象的格式为

```java
类名 对象名称 = new 类名();
```

例如下面创建一个per的对象

```java
public class Person {
    String name;
    int age;
    
    public void eat() {
        System.out.println("吃吃吃");
    }
    public void sleep() {
        System.out.println("睡睡睡");
    }
    
    public static void main(String[] args) { 
        Person per = new Person(); //创建一个名为per的对象
    }
}
```

我们创建这个对象的模板是Person类，那么它就应该有name, age属性和eat, sleep行为，那么我们怎么去使用这些东西呢? 方法就是通过点语法

```java
对象名.属性 //使用属性
对象名.方法() //使用方法
```

例如下面我们打印出per对象的属性值，并且调用它的方法

```java
public static void main(String[] args) {
    Person per = new Person(); //创建一个名为per的对象
    System.out.println(per.name);
    System.out.println(per.age);
    per.eat();
    per.sleep();
}
```

输出为

```java
null
0
吃吃吃
睡睡睡
```

我们发现per.name的值是null，per.age的值是0，实际上我们并没有给per.name和per.age赋值，系统在创建对象时会有默认值，规则和数组的一样。

## 类的内存模型

我们来看看执行下面的代码，在内存中发生了什么

```java
public class Person {
    String name;
    int age;
    
    public void eat() {
        System.out.println("吃吃吃");
    }
    public void sleep() {
        System.out.println("睡睡睡");
    }
    
    public static void main(String[] args) {
        Person per = new Person(); //创建一个名为per的对象
        System.out.println(per.name);
        System.out.println(per.age);
        per.eat();
        per.sleep();
    }
}
```

编译器首先会在方法区中找main方法，然后将它推入栈中

<center>
    <img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java08.png"/>
</center>

```java
Person per = new Person();
```

这行语句会在main中创建一个per变量，接着会在堆中开辟出一块空间，存储的是per这个对象，这个对象是以方法区中的类为模板的，该对象具有成员变量和成员方法。但是注意的是，对象的成员方法是地址值，指向方法区中的方法的信息，当调用方法时，会根据该地址值去方法区中寻找该方法。由于每个对象的行为都是一样的，只是属性不同，所以不需要在堆中为每一个对象都开辟空间来保存方法的信息，只需要保存一个地址值即可。这样做可以节省内存空间。

<center>
    <img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java09.png"/>
</center>

```java
System.out.println(per.name);
System.out.println(per.age);
per.eat();
per.sleep();
```

上面的语句是访问对象的成员变量和成员方法，首先会根据per对象保存的地址值，去堆中寻找对应的保存地址，然后根据成员变量名去访问数据。调用成员方法，分为了四步，第一步是根据per对象的地址值找到堆中的方法；第二步，堆中的方法保存的地址值，根据堆中的地址值去方法区中查找方法的信息；第三步，将方法压入栈中，栈会为该方法开辟一块空间；第四步，方法执行完毕，方法被栈移除。

## 对象与方法

对象作为一种数据类型，它也可以作为方法的参数和返回值。与数组一样，传递的是地址值，返回的也是地址值。

### 对象作为参数

```java
public static void printObject(Person per) {
    System.out.println(per.name);
    System.out.println(per.age);
}
```

### 对象作为返回值

```java
public static Person getPersonObject() {
    Person per = new Person();
    return per;
}
```

## 成员变量与局部变量

成员变量与局部变量的区别

- **定义的位置不一样**
  - 局部变量：定义在方法中
  - 成员变量：定义在方法之外，定义在类中
- **作用范围不一样**
  - 局部变量：只能在方法中使用
  - 成员变量：在类中都可以使用
- **默认值**
  - 局部变量：没有默认值
  - 成员变量：有默认值，规则同数组
- 在内存中的位置不一样
  - 局部变量：在栈内存中
  - 成员变量：在堆内存中
- 生命周期
  - 局部变量：随着方法的进栈而产生，随着方法的出栈而消失
  - 成员变量：随着对象的创建而产生，随着对象的被垃圾回收而消失

## 封装

面向对象有三大特性，分别为

- 封装
- 继承
- 多态

下面详细讲述其中的封装。

我们知道，当我们创建了一个对象之后，可以通过点(.)语法去访问成员变量，也可以为它赋值，如下

```java
public class Person {
    String name;
    int age;
    
    public static void main(String[] args) {
        Person per = new Person();
        per.name = "李四";
        per.age = 20;
        System.out.println("我叫做：" + per.name +"，我：" + per.age + "岁。");
    }
}
```

输出为：

```java
我叫做：李四，我：20岁。
```

但是一旦我们将成员变量使用修饰符private修饰的话，那么我们就不能通过.语法访问成员变量了，如下面的例子

```java
public class Person {
    private String name;
    private int age;
    
    public static void main(String[] args) {
        Person per = new Person();
        per.name = "李四"; //编译成功
        per.age = 20; //编译成功
        System.out.println("我叫做：" + per.name +"，我：" + per.age + "岁。");
    }
}
```

我们发现怎么使用private修饰的变量怎么还可以访问，不是说不可以访问了吗? 这是因为main方法是Person类的方法，在一个类里面是可以随便访问的，现在有一个Test类，然后我们去访问数据

```java
public class Test {

    public static void main(String[] args) {
        Person per = new Person();
        per.name = "李四"; //编译失败
        per.age = 20; //编译失败
        System.out.println("我叫做：" + per.name +"，我：" + per.age + "岁。");
    }
}
```

private就是私有的意思，意味的这个变量的私密的，外部不可以访问，如果要访问的话，只能通过getter和setter方法

```java
public class Person {
    private String name;
    private int age;
    
    public String getName() {
        return name;
    }
    public void setName(String who){
        name = who;
    }
    
    public int getAge() {
        return age;
    }
    public void setAge(int num){
        age = num;
    }
    
}
```

现在我们在Test类中创建Person对象访问

```java
public class Test {
    public static void main(String[] args) {
        Person per = new Person();
        per.setName("李四"); //通过setter方法去设置成员变量的值
        per.setAge(20); //通过setter方法去设置成员变量的值
        System.out.println("我叫做：" + per.name +"，我：" + per.age + "岁。");
    }
}
```

输出为

```java
我叫做：李四，我：20岁。
```

明显可见，下面的代码量比上面大了很多，感觉这么做是多此一举，那么为什么要这么做呢？考虑下面这么一种情况，如果没有进行封装，那么我们可以使用`.`语法为age变量赋值，我们可以赋值为-20，这明显是不合理的，但是可以进行赋值，如果我们进行封装，那么我们可以在setter方法中进行判断，使得一些不合理的，有害的操作不能够正常赋值。

```java
public void setAge(int num) {
    if (num > 0) {
        age = num;
    }
}
```

getter方法和setter方法的格式一般为"get变量名"或"set变量名"，根据驼峰命名法，变量名首字母需要大写，如上面的setAge, getAge。但是如果getter方法的返回值是boolean类型的话，我们一般写成isXxx的形式。

## this关键字

考虑上例我们的setter方法

```java
public void setName(String who){
    name = who;
}
```

其实我一直想写成这样

```java
public void setName(String name){
    name = name;
}
```

我的想法是第一个name是成员变量name，第二个name是传入的参数，但是这样不行，因为这时的两个name都被看做是传入的参数，那有什么办法可以解决成员变量与局部变量重名的问题吗？方法就是使用this关键字，修改上面代码如下

```java
public void setName(String name){
    this.name = name;
}
```

这时this.name就代表的是成员变量name。那么this到底是个什么东西？**哪个对象调用这个方法，那么this就是调用这个方法的对象**.

```java
per.setName("李四");
```

per这个对象调用setName方法，那么这时的this就是per。

## 构造方法

构造方法其实就是创建对象的方法，用new在创建对象，就是在调用这个方法。还记得我们是怎么创建对象的吗

```java
Person per = new Person();
```

其中Person()就是构造方法。

### 构造方法的定义

构造方法定义的格式为

```java
public 类名() {
    
}
```

**注意：**

- 构造方法名要与类名的完全一样，包括大小写
- 不要写返回值类型，void也不要写
- 不能return一个具体的返回值

构造方法同成员方法一样，是定义在类中的，但是我们好像到现在从来没有定义过构造方法，但是我们却可以使用，这时为什么？

- 如果我们没有编写构造方法，那么编译器会为我们自动生成一个构造方法，该构造方法没有参数，方法体为空，比如像这样

```java
public Person() {
    
}
```

- 但是一旦当我们定义了一个构造方法，那么编译器不会自动生成一个构造方法

```java
public Person(String name, int age) {
    this,name = name;
    this,age = age;
}
```

我们定义个一个构造方法，此时总共就这一个构造方法，编译器不会自动生成。但是我们一般还会写一个无参的构造方法，继承那里会讲到为什么。

- 构造方法也可以进行重载，在上面我们已经演示了

### 利用构造方法进行初始化

我们一般可以利用构造方法进行初始化，注意我们之前初始化都是这样

```java
Person per = new Person();
per.setName("李四");
per.setAge(20);
```

现在我们定义一个有两个参数的构造方法

```java
public Person(String name, int age) {
    this.name = name;
    this.age = age;
}
```

然后就可以这么调用

```java
Person per = new Person("李四", 20);
```

这一行的代码与上面三行代码的效果是一样的。

## 一个标准的类

一个标准的类应该满足一下特点

- 所有的成员变量都使用private修饰
- 为每一个成员变量编写一个setter,getter方法
- 编写一个无参构造函数
- 编写一个有参构造函数

一个标准的类也叫做Java Bean。

## 匿名对象

所谓的匿名对象，指的就是没有名字的对象。即在创建对象时，并没有为它赋予变量名。由于它没有名字，没有变量保存它的地址，所以它只能够使用一次，如下

```java
new Person().age;
```

如果某个对象只使用一次的话，我们可以考虑使用匿名对象。

### 匿名对象作为方法的参数

匿名对象也是对象，当然可以作为方法的参数。匿名对象传入方法的是地址值，下面举一个例子

```java
import java.util.Scanner;

public class Anonymous {
    //getNum方法接收一个Scanner对象，返回一个int类型的数值
    public static int getNum(Scanner sc) {
        int num = sc.nextInt();
        return num;
    }
    public static void main(String[] args) {
        //传入了一个匿名对象
        int num = getNum(new Scanner(System.in));
        System.out.println(num);
    }
}

```

程序运行效果为

<center>
<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java10.gif"/>
</center>

### 匿名对象作为方法的返回值

匿名对象也可以作为方法的返回值，返回的也是地址值

```java
import java.util.Scanner;

public class Anonymous {
    //返回一个Scanner对象
    public static Scanner getScannerObject() {
        return new Scanner(System.in);
    }
    public static void main(String[] args) {
        //GetScannerObject方法返回一个Scanner对象
        Scanner sc = getScannerObject();
        String str = sc.next();
    }
}

```

程序运行效果为

<center>
<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java11.gif"/>
</center>

## static关键字

static关键字我们在之前见到过很多次了，比如

```java
public static void main(String[] args){
    
}
```

或者在定义方法时

```java
public static void add() {
    
}
```

都用到static，而在成员方法中却没有用到static，所以这里我们就来看看static到底是什么，它有什么用。

### static修饰符的作用

一旦被static修饰，不管是变量还是方法，那么这么变量或方法不再是属于某个对象的，而是属于类，相当于说被static修饰之后，就不是某个人的私有财产，而是大家的公共财产。被static修饰的变量和方法分别叫做静态变量和静态方法。

访问static修饰的变量或方法，可以通过.语法，可以通过类名.，也可以通过对象名.。以前我们调用成员变量和成员方法就是通过对象名`.`出来的。但是这里推荐使用类名.，这样大家一眼就可以看出这是共有财产而不是私有财产。而且即使你使用了对象名.，在编译器编译时也会自动转换为类名.。

为了理解static修饰符，我们来看这么一个例子。假设有一个Student类，里面有一个id成员变量，我们希望每当创建一个对象时，会自动赋予id变量一个值，比如说，如果one第一个创建的学生，那么它的id就是1，以此类推。我们可以使用一个static变量idCounter来计数，每创建一个对象它就加一

```java
public class Student {
    String name;
    int id;
    static int idCounter;

    public Student() {
        //每创建一个对象，id计数器加一，并且赋给id
        this.id = ++idCounter;
    }
    public Student(String name) {
        this.name = name;
        //每创建一个对象，id计数器加一，并且赋给id
        this.id = ++idCounter;
    }

    public static void main(String[] args) {
        Student one = new Student("one");
        System.out.println("我的名字是：" + one.name + "我的id是："  + one.id);
        Student two = new Student("two");
        System.out.println("我的名字是：" + two.name + "我的id是："  + two.id);
    }
}
```

输出为

```java
我的名字是：one我的id是：1
我的名字是：two我的id是：2
```

这里我要解释一下，为什么有的方法使用static修饰了，我们知道，如果使用static修饰，该方法就可以通过类`.`出来，如果这个方法是本类的，那么类名可以省略不写。比如这样

```java
public static void add() {
    
}
public static void main(String[] args) {
    //在这里调用add方法，属于同一个类，可以省略类名
    add();
}
```

如果我们需要直接在main方法中直接使用这个方法，我们就会使用static修饰。这里只是解释前面为什么有的方法要使用static修饰，因为我们希望直接调用。

这里有关static有几个需要注意的事项

- 静态只能访问静态，静态不能访问非静态。即在静态方法里面，不能访问成员变量，也不能调用成员方法。这时因为在内存中，先有静态内容，后有非静态内容。"先人不知后人，后人知道先人"。
- 静态方法中不能使用this。我们知道，谁调用这个方法，那么this就是这个对象。但是调用静态方法是类，而不是对象，即使使用对象调用静态方法，编译器在编译时也会转换为类调用。

### static的内存图

在方法区中有一块内存空间，专门用以保存静态变量的

<center>
<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java12.png"/>
</center>

从上面的图可以看出来，静态变量与对象没有任何关系。

### 静态代码块

静态代码块的格式是

```java
static {
    
}
```

该代码块写在类中。它的特点是在第一次创建对象的时候执行唯一的一次。后面在创建对象不会再执行。它先于构造方法的执行。它的作用一般是为了初始化静态变量。

## 继承

继承，主要解决的是共性抽取。子类继承了父类，就拥有父类所有的成员变量和成员方法。除此之外，子类还可以拥有自己的内容。子类与父类的关系，可以这么理解：子类就是一个父类。比如父类是人，子类是学生，子类就是父类说的就是**学生是人**。也叫is-a关系。

### 继承的格式

子类通过extends关键字基础父类，而父类的定义与一般类的定义相同，现在假设有一个父类Person和一个子类Student。

```java
public class Person {
    String name;
    int age;
    
    public void showA() {
        System.out.println("我是父类的方法");
    }
}
```

```java
public class Student extends Person {
    int id; //子类拥有父类的成员变量和成员方法
}
```

### 子类访问父类成员变量

上面讲过，一旦子类继承了父类，那么子类就拥有子类的所有成员变量和成员方法。那么子类怎么访问父类的成员变量，这里分为重名和不重名两种

- 不重名
  - 这种情况最简单，这时是直接用子类对象`.`父类的成员变量就可以访问

```java
Student stu = new Student();
stu.name;
stu.age;
```

- 重名
  - 直接
    - 如果使用子类对象`.`的话，访问的就是子类的成员变量
  - 间接
    - 间接指的就是通过成员方法访问，此时举个例子说明

假设父类有一个`int num;`，子类也有一个`int num;`。现在父类有一个showA方法如下

```java
public void showA() {
    System.out.println(num);
}
```

那么如果子类对象调用这个方法的话，那么使用的就是父类的num。现在如果子类有一个showB方法，如下

```java
public void showB() {
    System.out.println(num);
}
```

如果子类对象调用这个方法，那么使用就是子类的num。简而言之，方法属于谁，就用谁的。

### 区分三种变量

这三种变量指的就是

- 父类的成员变量
- 子类的成员变量
- 局部变量

之前我们讲过区分局部变量和成员变量，我们使用this关键字加以区分。现在假设在子类的一个成员方法中，父类，子类有成员变量name，而该方法也有一个局部变量，这时我们这么区分，如下：

```java
super.name;      //父类的name
this.name;       //子类的name
name             //局部变量name
```

同理，如果子类有成员方法和父类的一个成员方法重名的话，如果我们想在子类的某个成员方法中使用父类的这个成员方法的话，我们可以使用super.父类方法()调用。super这个关键字指的就是父类对象。

### 重写

之前我们有多次提到重写的概念，那么重写是什么? 重写指的就是子类的某个方法与父类的方法的方法名称一样，参数列表也一样，相当于把父类的这个方法覆盖了。当我们使用子类对象.这个方法时，优先使用子类的方法。

重写的方法需要满足一定的要求，如下：

- 必须父子类方法名相同，参数列表相同
- 子类方法的返回值范围要小于父类方法(Object > String)
- 子类方法的权限必须大于等于父类的权限操作符

```java
//权限修饰符的大小熟悉怒
public > protected > (default) > private
```

**注意：**

- 有一个注解，@Override，把它放在要重写的方法前面，可以检查是否重写正确，比如你重写的方法漏掉了一个字母，这相当于你自己有了一个新的成员方法，并不是重写，编译器不会报错，这可能会造成问题，因为你是想重写的。但是你如果在方法前面加上@Override，它会检查这个方法是不是重写，如果不是会报错。这个是可选的，但是推荐使用。

```java
@Override    //写在方法的前面
public void show() {
    
}
```

### 继承中的构造方法

为了讲解清楚，假设有两个类，一个是Person类，是父类，另一个是Student类，是子类。两个方法的定义如下

```java
public class Person {
    
    public Person() {
        System.out.println("父类的构造方法")
    }
}
```

```java
public class Student extends Person{

    public Student() {
        System.out.println("我是子类的构造方法");
    }
}
```

我要讲的是，在子类构造方法的第一行，默认有`super();`，这个代表调用父类的构造方法，即在调用子类构造方法时，会先调用父类的无参构造方法，现在我们创建一个Student对象，看看打印输出

```java
public class TestExtends {
    public static void main(String[] args) {
        Student student = new Student();
    }
}
```

打印输出为

```java
我是父类的构造方法
我是子类的构造方法
```

可见是默认会调用父类的无参构造方法。所以我们在之前建议为类写一个无参的构造方法，因为在创建子类对象时会先调用父类的无参构造方法，如果父类没有的话，会报错。

**注意：**

- super调用必须是第一个语句，比如下面会报错

```java
public Student() {
    System.out.println("我是子类的构造方法");
    super(); //会报错，因为不是第一个语句
}
```

- 这也意味着在方法中只能调用一次super()方法，因为第二个super不是第一个语句了

```java
public Student() {
    super();
    super("name",12);  //假设父类有一个有参构造方法 由于不是第一个语句，报错
                       //这意味着只能调用一个super()方法
    System.out.println("我是子类的构造方法");
    
}
```

- this()可以调用本类的构造方法，this()也必须是第一个语句，所以this()和super()不能同时出现。
- 如果父类有有参数的构造方法，我们可以在子类的构造方法中显式的调用该方法，如上面super("name",12)，这个时候就不会默认调用无参的构造方法。所以父类中没有无参的构造方法不一定会报错。建议最好还是写一个吧，即使什么都没有，有特殊需求的除外。

### 继承的三个特征

- Java语言是单继承的，只能有一个直接父类
- 可以有多级继承，继承的源头是Object
- 一个子类的直接父类是唯一的，但是可以有多个子类

## 抽象类

抽样方法：使用关键字abstract修饰的方法，抽象方法没有方法体，直接大括号结束。抽样方法所在的类必须是抽样类，抽样类的定义是在class前面加abstract。

如下定义了一个抽样类

```java
public abstract class Animal {
    public abstract void eat();
    public abstract void sleep();
}
```

eat(), sleep()方法是抽样方法，没有方法体，直接分号结束。

**注意：**

- 不能直接创建抽象类对象，应当用一个类继承该抽象类，该类必须重写所有的抽象方法，如果该类没有重写所有的抽象方法，那么该类也必须是抽象类。
- 抽象类可以有构造方法，给抽象类的成员变量初始化
- 抽样类不一定要有抽象方法，但是含抽象方法的类必须是抽象类

如下创建一个Dog类继承抽象类Animal

```java
public class Dog extends Animal {
    @Override
    public void eat() {
        System.out.println("汪汪汪...");
    }

    @Override
    public void sleep() {
        System.out.println("呼呼呼...");
    }

    public static void main(String[] args) {
        Dog dog = new Dog();
        dog.eat();
        dog.sleep();
    }
}
```

输出为

```java
汪汪汪...
呼呼呼...
```

## 接口

接口就是公共的规范标准。

### 接口的定义

我们类的定义是使用class关键字，而接口的定义是使用interface关键字。如下定义了一个接口

```java
public interface MyInterface {
    ...
}
```

接口里面可以有什么，这与版本有关。

- Java 7
  - 常量
  - 抽象方法
- Java 8(新增)
  - 默认方法
  - 静态方法
- Java 9(新增)
  - 私有方法

### 接口的使用

- 接口不能够直接使用，需要一个类去实现它。我们在之前使用extends去继承一个类，而实现一个接口使用implements关键字，如

```java
public class MyClass implements MyInterface {
    
}
```

- 实现必须实现接口中的所有抽样方法。如果没有全部实现，那么该类必须为抽样类
- 创建实现类对象使用

### 抽象方法

接口中的抽样方法的修饰符必须是public abstract，所这两个关键字可以省去，如下的写法都是对的

```java
public abstract void eat(); //不省略
abstract void eat(); //只省略public
public void eat();   //只省略abstract
void eat();          //两个都省略
```

### 默认方法

默认方法的定义为

```java
public default 返回值 方法名(参数列表) { //修饰符必须是public，所以public可以省略
    //方法体
}
```

默认方法就要解决的是升级问题。假设你要升级一个接口，如果你添加一个抽象方法的话，那么由于实现类没有实现该抽象方法，那么实现类就不能使用，这样是不合理的。但是添加默认方法的话，实现类不需要实现该方法，实现类可以正常的使用，并且可以通过实现类对象调用该默认方法。

### 静态方法

静态方法的定义为

```java
public static 返回值 方法名(参数列表){ //同default方法，public可以省略
    //方法体
}
```

实现类不能直接调用静态方法，只能通过接口`.`静态方法调用。因为一个实现类是可以同时实现多个接口的，如何多个接口有相同的静态方法，那么通过实现类调用静态方法，那么调用哪个呢？所以实现类是不能直接调用静态方法的。不同于继承，因为继承只有一个直接父类。

### 私有方法

私有方法是为解决代码重复问题的。假设有两个默认方法，这两个默认方法的代码重复很多，那我们想着可以把重复的代码抽出来形成一个新的方法，然后在这两个默认方法中调用这个新方法就可以。但是这个新方法是一个中间方法，应当是只能在接口内才能够被调用，所以不能是默认方法。这个方法应该被定义为私有方法。

私有方法分为两类

- 普通私有方法
  - 解决默认方法代码重复问题，只使用private修饰
- 静态私有方法
  - 解决静态方法代码重复问题，使用private static修饰

### 常量

接口中也能定义"成员变量"，只不过必须使用public static final修饰，final代表的就是不可变的意思，所以就相当于是一个常量。接口中的常量必须进行赋值，不能不赋值

```java
public static final int NUM = 10；  
```

对于常量，我们采用所有字母大写，并且单词之间使用下划线分割。接口使用常量的方法是接口`.`常量，原因同静态方法一样。

### 注意事项

- 接口没有静态代码块和构造方法
- 一个类可以同时实现多个接口

```java
... implements 接口1，接口2，...
```

- 如果实现的两个接口有相同的抽象方法，只需要实现一个即可
- 不能没有实现所有的抽象方法，那么就必须是抽象类
- 如果实现的两个接口有重复的默认方法，那么实现类必须重写该默认方法
- 一个类的直接父类与接口的默认方法重复的话，优先使用直接父类的方法
- 接口与接口之间多继承的

```java
接口 extends 接口1，接口2，...
```

如果继承的多个接口默认方法重复，那么必须重写，且重写的方法必须为默认方法。如果抽象方法重复，只继承一个。

## 多态

继承和实现接口是多态的基础。

### 多态的定义

多态的定义就是父类引用指向子类对象。

```java
父类 对象名 = new 子类();
接口 对象名 = new 实现类();
```

如假设有一个父类Person和一个子类Student如下

```java
public class Person {
	String name = "父类";	
    public Person() {
    }

    public void show() {
        System.out.println("我是父类的show方法");
    }
}
```

```java
public class Student extends Person{
	String name = "子类";
    @Override
    public void show() {
        System.out.println("我是子类的show方法");
    }
}

```

现在我使用多态的写法，创建一个Student对象指向Person引用，并且调用show方法

```java
public class Muti {
    
    public static void main(String[] args) {
        Person per = new Student();
        per.show();
        
        System.out.println(per.name);
    }
}

```

这时的输出为

```java
我是子类的show方法
父类
```

是不是有点难以理解上面的输出，下面讲解一下为什么会有上面的输出。

### 多态访问成员变量和成员方法的规则

#### 访问成员方法的规则

- 如果多态写法创建的对象，调用成员方法时，在编译时看左边，即看父类有没有这个方法，如果父类没有，那么会报错，如下面我在Student中新建了一个子类特有的方法，如果使用per对象调用的话，将会发生错误。

```java
public class Student extends Person{

    String name = "子类";

    @Override
    public void show() {
        System.out.println("我是子类的show方法");
    }
    
    public void showAgain() {
        System.out.println("我是子类特有的方法");
    }
}
```

<center>
<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java13.jpg"/>
</center>

我们观察到程序报错了，因为父类并没有这个方法。

- 在运行时看右边，即如果这个方法子类重写了，那么调用子类的方法，我们在上面也看到了，输出的是子类的方法
- 口诀："编译看左边，运行看右边"

#### 访问成员变量的规则

访问成员变量的规则与访问成员方法的规则不同。

- 访问成员变量时，编译时的规则也一样，如果父类没有该变量，则访问不了该变量。访问不了子类的变量。
- 即使子类中的成员变量与父类中的成员变量重名，那么访问的也是父类的成员变量，在上面我们已经看到了，我们在打印输出name是输出的是父类的成员变量。
- 口诀："编译看左边，运行也看左边"

### 对象转型

对象转型分为向上转型和向下转型。

#### 对象向上转型

多态就是向上转型，因为是子类对象指向父类的引用。创建的是子类的对象，但是使用却是当做父类对象使用。多态有一个明显的弊端就是无法使用子类特有的方法和子类的成员变量。

#### 对象向下转型

我们提到了多态的弊端，但是我就是要使用子类的特有方法怎么办，这个时候我们可以使用向下转型，比如

```java
Person per = new Student();
Student stu = (Student) per; //向下转型
```

这样我们就可以使用Student对象特有的方法了，比如

```java
public class Muti {

    public static void main(String[] args) {
        Person per = new Student();

        Student stu = (Student) per;
        stu.showAgain();
    }
}
```

输出为

```java
我是子类特有的方法
```

但是，向下转型要注意，我原来是Student对象才能转为Student对象，我如果原来不是，那么运行时会抛出异常，如下面我创建一个Teacher继承了Person，现在我要把per强行转型为Teacher对象

<center>
<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java14.png"/>
</center>

我们观察到，在编译时并没有报错，现在我们来运行一下

<center>
<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java15.png"/>
</center>

抛出了ClassCastException异常，意思是类型转换异常。所以向下转型不安全。



那我们为什么不直接创建一个子类对象指向子类呢？何必多次一举使用多态写法然后又给转回来呢? 我们来看这么一个方法

```java
public void someMethod(Person per)
```

这个类的方法参数只要求是Person就可以，它不管你是Student还是Teacher，所以如果我们把Student对象传进去，这是不是多态的写法，如果我们在里面要用到Student特有的方法，我们是不是要向下转型。



其实常常我们使用接口作为参数，表示的意思就是我才不管你是什么，你只有实现我的接口就可以了，那么我就可以使用你了。比如有一个接口叫做USB，那么传进来的参数，你只要实现了我们USB接口就可以，不管你是鼠标实现了还是键盘实现了，你只要实现了，我就可以用。这就是多态的用法啊。

### instanceof

上面我们说了，对象的向下转型是有风险的，因为如果我不是Student你给我转给Student那么会抛出异常的。现在instanceof关键字可以解决这个问题，他可以判断多态创建的父类引用为哪个子类。具体用法为

```java
多态创建的父类引用 instanceof 子类
```

该表达式返回一个boolean值，如果多态创建的父类引用是这个子类，那么返回true，否则返回false。例如

```java
Person per = new Student();
per instanceof Student; //true
per instanceof Teacher; //false
```

现在考虑这么一个函数

```java
public void someMethod(Person per)
```

我们现在就在方法中根据per instanceof Xxx的结果进行安全的向下转型

```java
public void someMethod(Person per) {
    if (per instanceof Student) {
        Student student = (Student) per;
        //实现Student对象特有的操作
    } else if (per instanceof Teacher) {
        Teacher teacher = (Teacher) per;
        //实现Teacher对象特有的操作
    }
}
```

## final

final关键字可以修饰

- 一个类
  - 修饰一个类时，该类不能够被继承
  - 所以final不能和abstract关键字一起使用，因为抽象类不能自己创建对象，只能被继承
- 方法
  - 修饰一个方法时，子类不能重写该方法
- 局部变量
  - 修饰一个局部变量时，这个变量不能进行更改
  - 下面两种写法都可以

```java
final int num = 10;
```

```java
final int num;
num = 10;
```

- 成员变量
  - 因为成员变量有默认值，所以要手动赋值
  - 要么直接赋值，要么在构造方法中进行赋值，在构造方法中赋值时，所有的构造方法都要进行赋值，即使是无参构造方法，也要进行赋值

```java
final int num = 10;
//下面的写法是错误的
final int num;
num = 10;
```

```java
public Xxx() {
    num = 0;
}
public Xxx(int num) {
    this.num = num;
}
```

## 权限修饰符

权限修饰符总共有四种，权限从大到小的顺序为

- public
- protected
- (default)，就是什么都不写
- private

下表列出了不同修饰符下的访问规则

|             | 同一个类 | 同一个包 | 不同包的子类 | 不同包非子类 |
| ----------- | -------- | -------- | ------------ | ------------ |
| public    | 能       | 能       | 能           | 能           |
| protected | 能       | 能       | 能           | **不能**     |
| (default) | 能       | 能       | **不能**     | **不能**     |
| private   | 能       | **不能** | **不能**     | **不能**     |

## 内部类

内部类是一个很重要的概念，主要分为两类

- 成员内部类
- 局部内部类
  - 匿名内部类

下面我们来看一下内部类的使用

### 成员内部类

成员内部类就像成员变量和成员方法一样定义在类里面，比如有一个叫做Body，内部还有一个类叫做Heart。我们可以这么定义

```java
public class Body {
    public class Heart {
        
    }
}
```

内部类可以随意的访问外部类的成员变量和成员方法，但是外部类要访问内部类要借助内部类对象。

如何使用成员内部类

- 在外部类的方法中使用内部类，然后在main方法中调用外部类的方法。
- 直接创建内部类的对象，创建格式为

```java
外部类.内部类 对象名 = new 外部类().new 内部类();
```

比如上面我想创建一个Heart对象就可以这么写

```java
Body.Heart heart = new Body().new Heart();
```

内部类变量与外部类变量重名问题，假设外部类有一个成员变量num，内部类也有一个成员变量num，在内部类的成员方法中也有一个局部变量num，现在在内部类的这个方法中怎么访问外部类的成员变量，怎么访问内部类的成员变量。如下

```java
num //局部变量
this.num //内部类的成员变量
外部类名.this.name //外部类的成员变量
```

现在来看一个例子

```java
public class Body {
    int num = 10; //外部类的num

    public class Heart {
        int num = 20; //内部类的num

        public void showNum(int num) {
            System.out.println(num); //打印局部变量
            System.out.println(this.num); //打印内部类的成员变量
            System.out.println(Body.this.num); //打印外部类的成员变量
        }

    }

    public static void main(String[] args) {
        Body.Heart heart = new Body().new Heart();
        heart.showNum(30); //局部变量num
    }
}
```

输出结果为

```java
30
20
10
```

### 局部内部类

局部内部类就是定义在方法内部的类，定义如下

```java
class 局部内部类名 {
    
}
```

注意，不要写任何的修饰符。这并不代表它的修饰符权限是(default)，因为修饰符没有意义，类定义在方法的内部，只有在方法内部在能够访问。

我们在这里适时的总结一下修饰符修饰类

- 外部类
  - 只能是public, (default)
- 内部类
  - 成员内部类
    - 可以是四种修饰符的任意一种
  - 局部内部类
    - 什么都不能写

局部内部类如果想访问方法中的局部变量，那么这个变量必须是局部final的，什么叫做有效final呢？指的就是没有使用final修饰符修饰（当然你使用final修饰更加的保险），但是变量的值没有发生改变。比如下面

```java
public void testlocalInnerClass() {
    int num = 10;
    class LocalInnerClass {
        public void printNum() {
            System.out.println(num);
        }
    }
}
```

这里的num虽然没有使用final修饰符修饰，但是它只进行了一次赋值，所以是有效final的，所以在局部内部类中可以访问该变量。假设num又进行了赋值，那么将会编译失败。

### 匿名内部类

匿名内部类是相当重要的概念了。如果接口实现类(或者是父类的子类)只使用一次，那么这种情况可省略掉该类的定义，而改为使用匿名内部类。

比如有一个接口，叫做USB好了，然后在一个Computer类中要使用该接口，如下

```java
public interface USB {
    public abstract void open();
    public abstract void close();
}
```

```java
public class Computer {
    public void useUsb(USB usb) {
        usb.open();
        usb.close();
    }
}
```

现在我们创建一个类去实现USB接口，然后创建对象，传入Computer的这个方法中

```java
public class ImpUSB implements USB {
    @Override
    public void open() {
        System.out.println("打开USB设备");
    }

    @Override
    public void close() {
        System.out.println("关闭USB设备");
    }
}
```

```java
public class Computer {
    public void useUsb(USB usb) {
        usb.open();
        usb.close();
    }

    public static void main(String[] args) {
        Computer computer = new Computer();
        ImpUSB impUSB = new ImpUSB();
        computer.useUsb(impUSB);
    }
}
```

输出为

```java
打开USB设备
关闭USB设备
```

但是这样写未必太麻烦了，这个类只使用了一次，但是为了使用这个接口，我却要写一个类去实现该接口，然后创建对象使用，未必有点麻烦。Java提供了更加方便的方法，那就是匿名内部类。

匿名内部类的定义格式为

```java
接口名称 对象名 = new 接口名称() {
    //在这里覆盖重写接口所有的方法
}
```

现在我们匿名内部类来实现上面同样的效果

```java
public static void main(String[] args) {
    Computer computer = new Computer();
    //创建匿名内部类
    USB usb = new USB() {
        @Override
        public void open() {
            System.out.println("花式打开USB设备");
        }
        @Override
        public void close() {
            System.out.println("花式关闭USB设备");
        }
    };
    computer.useUsb(usb);
}
```

输出为

```java
花式打开USB设备
花式关闭USB设备
```

在这里我们并没有创建一个实现类去实现USB接口，而是创建了一个匿名内部类，这个类没有名字（这也是为什么叫做匿名内部类），所以它不能通过它在创建新的对象。正如匿名对象一样，只能调用一次方法。

匿名内部类的注意事项

- 匿名内部类在创建对象时，只能使用唯一的一次
- 匿名对象在调用方法只能调用唯一的一次
- 匿名内部类是省略了类的名称，匿名对象是省略了对象的名称



<Disqus />