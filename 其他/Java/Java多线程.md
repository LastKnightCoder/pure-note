---
title: Java多线程
author: 熊滔
commentid: javase:multi-threads
---

## 多线程概述

一个软件可以同时干多件事情，就是多线程，目前我们编写的程序都是单线程的，在main方法中从上到下的进行，执行完上面的程序才能执行后面的程序。下面就将讲解如何写多线程程序。

在Java中创建多线程有两种放法，一种是继承Thread类，然后重写它的run()方法。第二种是实现Runnable接口，并且实现run()方法。

### 继承Thread类

继承Thread类实现多线程的步骤为

- 继承Thread类，重写run()方法
- 创建类对象，调用继承的start()方法

我们写一个MyThread类继承自Thread类，并重写run方法如下

```java
public class MyThread extends Thread {
    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            System.out.println("thread" + i);
        }
    }
}
```

新建一个测试类，在其main方法中创建MyThread对象，并且调用对象继承自Thread的start()方法

```java
MyThread thread = new MyThread();
thread.start();
for (int i = 0; i < 20; i++) {
    System.out.println("main" + i);
}
```

程序输出为

<center>
<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java23.png"/>
</center>

观察到main方法中的程序和MyThread的run()方法中的程序在交替的进行。而不是在等待我执行完了，另一个在执行，而是两个在同时的执行，这就是多线程。

### 实现Runnable接口

实现Runnable接口实现多线程的步骤为

- 实现Runnable接口并实现run()方法
- 在main中创建实现类对象
- 将实现类对象多为参数传入Thread()的构造方法，得到一个Thread对象
- 该Thread对象调用start()方法

下面进行演示，首先创建一个类实现Runnable接口

```java
public class MyRun implements Runnable{
    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            System.out.println("Run" + i);
        }
    }
}
```

下面在实现类中实现多线程，如下

```java
Runnable run = new MyRun(); //创建实现类对象
Thread thread = new Thread(run); //实现类对象作为参数传入Thread的构造方法
thread.start(); //Thread对象调用start()方法启动线程

for (int i = 0; i < 20; i++) {
    System.out.println("main" + i);
}
```

输出为

<center>
<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java24.png"/>
</center>

可以观察到main中的程序和run()方法中的程序是在同时进行的。

### Thread中的常见方法

- getName()
  - 获得线程的名称
- currentThread()
  - 获得正在执行的线程
- setName(String str)
  - 设置线程的名字
  - 也可以通过new Thread(String str)设置线程的名字
- sleep(long l)
  - 线程休眠l毫秒

我们修改MyRun中的run()方法为

```java
for (int i = 0; i < 20; i++) {
    //获得当前线程并且获得当前线程的名字
    System.out.println(Thread.currentThread().getName() + i); 
} 
```

修改main方法为

```java
Runnable run = new MyRun();
Thread thread = new Thread(run);
thread.setName("run"); //增加了这一行，设置线程的名字
thread.start();

for (int i = 0; i < 20; i++) {
    System.out.println("main" + i);
}
```

输出结果为

<center>
<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java25.png"/>
</center>

## 线程安全

### 安全问题

现在考虑这么一个问题，有三个售票机在售票，那么它们不能发生售出同样的票，也不能售出不存在的票。现在我要用一个类模拟售票机售票，并使用多线程模拟同时售票，新建一个SellTicket类

```java
public class SellTicket implements Runnable{
    private int ticket = 10; //总共10张票

    @Override
    public void run() {
        while (true) { //表示一直售票
            if (ticket > 0) { //如果还有票
                try {
                    //为了增加出错的可能，使当前的线程休眠1ms
                    Thread.sleep(1);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

                System.out.println(Thread.currentThread().getName() + "正在卖第" + ticket + "张票");
                ticket --;
            }
        }
    }
}

```

在测试类的main方法中创建三个线程同时售票

```java
Runnable sellTicket = new SellTicket();
//创建三个线程 注意必须使用的同一个售票机对象 否则他们就是各自10张票而不是总共10张票
new Thread(sellTicket).start();
new Thread(sellTicket).start();
new Thread(sellTicket).start();
```

输出为

<center>
<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java26.png"/>
</center>

我们发现出现了不同的售票线程售出了相同的票，并且有的售票线程售出了不存在的票-1。

### 线程同步

之所以会出现上面的问题，就是因为在有的售票机卖出了票，即进入了if语句后，但是还未进行ticket--操作，但是这个时候这个线程失去了CPU的执行权，并且别的线程拿到了CPU的执行权，由于未对ticket--，所以它们拿到的是同一张票，所以这就是为什么它们能卖出同一张票的原因。同理卖出不存在的票也是同种原因。

那么如何解决这个问题，我们必须要求在售票机在完成售票并且对ticket--之前，别的售票机不能对ticket进行操作，这样就不会出现票重复和卖出不存在的票的情况了，而实现这个的技术就叫做同步。有三种实现方式，分别是

- synchronized代码块
- 同步方法
- Lock锁

下面具体介绍用法。

#### sychronized代码块

sychronized代码块的格式为

```java
sychronized(锁对象) {
    //需要同步的代码，也就是可能出现问题的代码
}
```

其中锁对象可以是任意的对象，当一个线程执行到同步代码块时，会将该锁对象交给这个线程，当这个线程执行完同步代码块时，会释放锁对象，所以如果这个线程在同步代码块内失去了CPU的执行权，因为别的线程没有锁对象，就不能进入同步代码块执行，就会进入堵塞状态，等待锁对象被释放。所以锁对象就相当于是钥匙了，要保证多个线程的锁对象要相同，这样就只有一把钥匙了。

我们重新修改SellTicket的类如下

```java
public class SellTicket implements Runnable{
    private int ticket = 10; //总共10张票
    Object object = new Object(); //锁对象
    
    @Override
    public void run() {
        while (true) { //表示一直售票
            //同步代码块 一次只有一个线程执行
            synchronized (object) {
                if (ticket > 0) { //如果还有票
                    try {
                        //为了增加出错的可能，使当前的线程休眠1ms
                        Thread.sleep(1);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println(Thread.currentThread().getName() + "正在卖第" + ticket + "张票");
                    ticket --;
                }
            }
        }
    }
}
```

输出结果为

<center>
<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java27.png"/>
</center>

这次我们发现没有卖出重复的票，也没有卖出不可能的票。

#### 同步方法

同步方法其实就是使用synchronized修饰的方法，这个方法每次也只能有一个线程执行，它的锁对象是this，我们把上面买票的程序抽取出为一个方法sellTicket()

```java
public synchronized void  sellTicket() {
    if (ticket > 0) { //如果还有票
        try {
            //为了增加出错的可能，使当前的线程休眠1ms
            Thread.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(Thread.currentThread().getName() + "正在卖第" + ticket + "张票");
        ticket --;
    }
}
```

这时run()可简化为

```java
@Override
public void run() {
    while (true) { //表示一直售票
        sellTicket();
    }
}
```

输出为

<center>
<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java28.png"/>
</center>

也达到了同样的效果。

注意：

- 同步方法也可以为静态方法，不过这时的锁对象不在是this了，而是本类的class属性，也是一个对象。

#### Lock锁

Lock是一个接口，它比较灵活。之前我们讲到，在线程执行到synchronized代码块时，会获得锁对象，在执行完代码块时，会释放锁对象，但是这些对我们都是不可见的，而Lock灵活在我们自己觉得在哪里加锁，哪里释放锁。它有两个方法

- lock()
  - 加锁
- unlock()
  - 释放锁

lock()一般写在同步代码前，unlock()写在同步代码后。ReentrantLock是Lock的实现类，下面我们将演示如何使用Lock锁同步，修改run方法为

```java
Lock lock = new ReentrantLock(); //创建Lock锁实现类对象
@Override
public void run() {
    while (true) { //表示一直售票
        lock.lock();  //加锁
        if (ticket > 0) { //如果还有票
            try {
                //为了增加出错的可能，使当前的线程休眠1ms
                Thread.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + "正在卖第" + ticket + "张票");
            ticket --;
        }
        lock.unlock(); //释放锁
    }
}
```

输出为

<center>
<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java29.png"/>
</center>

可见达到了同步的效果。

## 等待唤醒

当我们排队买奶茶时，我们对老板说我们要一杯奶茶，然后我们就等着，老板去制作奶茶，等老板制作好奶茶后去喊我们。这其实就是等待唤醒，当多个线程去操作同一个资源时，比如奶茶，就需要一方(顾客)等着，等待另一方(老板)唤醒，总不能奶茶没有好我去抢吧。

### 线程状态

在讲解等待唤醒之前，我们先对线程的状态有一个大致的了解，看图

<center>
<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java30.png"/>
</center>

当我们创建一个线程对象还没有start()时，这时它处于New状态；当我们执行start()方法后，这时的线程状态由New转向Runnable运行状态；如果执行完run()方法，或者调用了stop()方法或者抛出了异常那么该线程进入死亡状态。如果在Runnable状态失去了CPU的执行权，那么就会进入Blocked阻塞状态；线程在这里等待CPU的执行权，拿到了CPU的执行权就会从该状态来到Runnable状态；如果线程在运行时执行了sleep(l)或wait(l)(l为等待的时间)方法，那么就会由Runnable状态进入Timed waiting状态，在这个状态中，线程放弃争夺CPU的执行权，当等待的时间到了之后，如果CPU空闲，那么就进入Runnable状态，如果忙碌，那么就进入Blocked状态，与其他线程一起争夺CPU的执行权。如果在Runnable执行了wait()(不带参数的)方法，就会进入Waiting永久等待状态，直到锁对象执行notify()方法唤醒，如果CPU空闲，就进入Runnable状态，否则进入Blocked状态争夺CPU执行权。

### 等待唤醒

这里的等待唤醒指的就是上面提及的Runnable状态执行wait()方法到Waiting永久等待状态，以及执行notiify()方法有永久等待状态到Runnable状态。前者为等待，后者为唤醒。

注意：

- 只有锁对象才能调用wait()和notify()方法
- wait()和notify()的调用者应该是同一锁对象，并且必须写在同步代码块中
- 执行wait()被唤醒后，会继续执行wait()后面的代码
- notify()一次只能唤醒一个线程，唤醒的是睡眠最久的线程，notifyAll()能够唤醒所有的线程

下面以最先提及的买奶茶为例演示这一过程。首先创建Runnable顾客类和老板类和奶茶类

```java
public class MilkTea {
    String taste; //奶茶口味

    boolean flag = false; //奶茶有没有做好
}
```

```java
public class CustomerThread implements Runnable {

    private MilkTea milkTea; //作为锁对象

    public CustomerThread(MilkTea milkTea) {
        this.milkTea = milkTea;
    }

    @Override
    public void run() {
        while (true) {
            synchronized (milkTea) { 
                if (milkTea.flag == false) { //奶茶没有准备好
                    System.out.println("老板来杯珍珠奶茶");
                    try {
                        milkTea.wait(); //等待
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                System.out.println(milkTea.taste + "真好喝"); //被唤醒后会执行这个
                milkTea.flag = false; //奶茶喝完了
                milkTea.notify();
            }
        }
    }
}
```

```java
public class Shopper implements Runnable {
    private MilkTea milkTea; //作为锁对象 和Customer相同

    public Shopper(MilkTea milkTea) {
        this.milkTea = milkTea;
    }

    @Override
    public void run() {
        while (true) {
            synchronized (milkTea) {
                if (milkTea.flag == true) {
                    try {
                        milkTea.wait(); //等待
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                } else {

                    try {
                        System.out.println("做奶茶中 ...");
                        Thread.sleep(3000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }

                    milkTea.taste = "珍珠奶茶";
                    milkTea.flag = true;
                    System.out.println(milkTea.taste + "做好了");
                    milkTea.notify();
                }
            }
        }
    }
}
```

下面在测试类中创建两个线程

```java
public class Test {
    public static void main(String[] args) {
        MilkTea milkTea = new MilkTea(); //作为锁对象
        new Thread(new CustomerThread(milkTea)).start();
        new Thread(new Shopper(milkTea)).start();
    }
}

```



输出为

<center>
<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java31.png"/>
</center>

这就是等待唤醒的过程。

## 线程池

当我们需要一个新的线程执行任务，我们就会创建一个新的线程，但是如果这个线程执行的任务很少，并且我们需要频繁的创建线程，这个创建线程的过程会很耗费时间，所以我们就想有没有一个机制，我们不用创建线程，当我们需要线程时我们去取，当我们用完时，我们还给它。这样就不需要频繁创建线程，省去时间，提高效率。线程池可以帮我们实现这一个想法。

那接下来的问题我们怎么使用Java为我们准备的线程池，Executors提供了一个静态方法newFixedThreadPool(int nThreads)，这个方法接收的参数是线程池中线程的个数，返回一个ExecutorService对象，然后我们就可以使用该对象的submit(Runnable task)方法，传入一个Runnable实现类对象就可以了。下面我们来示例一番

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class ThreadPool {
    public static void main(String[] args) {
        //创建一个匿名内部类
        Runnable run = new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 5; i++) {
                    System.out.println(Thread.currentThread().getName() + " " + i);
                }
            }
        };
		//长度有三个线程的线程池
        ExecutorService threadPool = Executors.newFixedThreadPool(3); 
        //三个线程执行任务
        threadPool.submit(run);
        threadPool.submit(run);
        threadPool.submit(run);
    }
}
```

输出为

```java
pool-1-thread-1 0
pool-1-thread-2 0
pool-1-thread-1 1
pool-1-thread-2 1
pool-1-thread-1 2
pool-1-thread-3 0
pool-1-thread-2 2
pool-1-thread-3 1
pool-1-thread-1 3
pool-1-thread-3 2
pool-1-thread-2 3
pool-1-thread-3 3
pool-1-thread-1 4
pool-1-thread-3 4
pool-1-thread-2 4
```

## Lambda表达式

我们在创建一个线程时，我们一般需要做一下的步骤

- 创建一实现类实现Runnable接口
- 重写run方法
- 创建实现类对象
- 将该对象传入Thread的构造方法中

上面的写法可以简化，省去创建一个实现类，直接创建一个匿名内部类

- 创建一个Runnable匿名内部类
- 重写run方法
- 将该对象传入Thread的构造方法中

其实上面有很多的代码是多余，真正有用的代码就是run()方法里面的代码，但是为了创建一个线程我们不得不要创建一个对象，然后巴拉巴拉。其实有时候我们不关心谁来做，只需要告诉我怎么做，比如一个线程你只需要告诉我run()方法就可以了，告诉我怎么做就可以了，但是我们却要创建一个对象等等一系列的操作才能达到这个目的。

### Lambda的使用

Java在JDK 1.8中引入了Lambda表达式，可以极大简化我们的编程，可以做到我上面所说的只关心怎么做的问题，不需要创建对象。我们来看看下面这段代码用Lambda怎么写

```java
new Thread(new Runnable() {
    @Override
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println(Thread.currentThread().getName() + " " + i);
        }
    }
}).start();
```

Lambda的写法为

```java
new Thread(() -> {
    for (int i = 0; i < 20; i++) {
        System.out.println(Thread.currentThread().getName() + " " + i);
    } 
});
```

现在你可能没有看懂这个写法，下面让我为你解释一番。首先我们注意到

```java
new Runnable() {
    @Override
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println(Thread.currentThread().getName() + " " + i);
        }
    }
}
```

被简单的替换为了

```java
() -> {
    for (int i = 0; i < 20; i++) {
        System.out.println(Thread.currentThread().getName() + " " + i);
    } 
}
```

两段代码很像，但是Lambda省略了很多的东西。首先我们知道Thread()里面传的是一个Runnable实现类的对象，该类重写类run方法，真正有用的就是run方法，所以我们把这些全部省略了，直接传入一个run()就可以了，并且由于run()方法的方法名是确定的，我们连run方法名都可以省去，返回值类型也是确定，所以我们也可以省去，最后只剩一个参数列表，在参数列表与方法体之间加入->就是Lambda表达式。使用Lambda不用创建对象，我们只需要传入一个方法，告诉它怎么做就可以了。这个也叫做函数式编程。

Lambda表达式的格式为

```java
(参数列表) -> {
    //方法体
}
```

为了熟悉Lambda表达式的使用，我们来看一个例子，定义一个Calculator的接口，里面有一个方法叫`calculate(int a, int b);`，如下

```java
public interface Calculator {
    int calculate(int a, int b);
}
```

在测试类中定义一个方法，该方法需要`Calculator`接口作为参数

```java
public static int cal(int a, int b, Calculator calculator) {
    int result = calculator.calculate(a,b);
    return result;
}
```

这个方法表示的是，a,b经过Calculator计算之后得到一个数，而计算方法，根据我们传入的calculator而定，这明显是我们只需要告诉计算器怎么做就行，我们把做的方法告诉它，使用Lambda表达式

```java
//加法计算器
int result1 = cal(2,3, (int a, int b) -> {
    return a + b;
});
System.out.println(result1);
//减法计算器
int result2 = cal(2,3, (int a, int b) -> {
    return a - b;
});
System.out.println(result2);
```

输出为

```java
5
-1
```

根据我们传入的方法不同，这个计算器就不同，计算器关心的就是怎么做，你告诉怎么做就可以。

### Lambda的省略格式

其实上面的Lambda还可以进行化简，因为还有很多是可以推断出来的，比如参数列表里面的参数类型可以省略，因为这个参数类型时确定的，不可能会变的。如

```java
int result1 = cal(2,3, (a, b) -> {
    return a + b;
});
```

如果方法体里面只有一条语句时，那么花括号也可以省略，这时分号也可以省略，如果这条语句是return语句，那么return也可以省略，因为必须是要返回一个值的，这个可以推断出来，所以可以省略，所以上面又可以简写为

```java
int result1 = cal(2,3, (a, b) -> a + b);
```

如果参数列表里面只有一个参数的话，那么小括号也可以省略

```java
param -> {
    //方法体
}
```

### Lambda表达式的使用前提

虽然Lambda表达式这么好用，但是是有使用前提的

- 使用Lambda必须具有接口，且要求**接口中有且仅有一个抽象方法**。
  - 比如Runnable接口，里面只有一个run()方法是抽象方法
  - 比如上面定义的Calculator接口，里面也只有一个抽象方法calculate()
- 使用Lambda必须具有**上下文推断**。
  - 也就是方法的参数或局部变量类型必须为Lambda对应的接口类型，才能使用Lambda作为该接口的实例。
  - 不能是我要一个Calculator接口的calculate()方法，你给我传一个Runnable的run()方法，兄弟，暗号对不上啊。

> 备注：有且仅有一个抽象方法的接口，称为"**函数式接口**"。



<Disqus />