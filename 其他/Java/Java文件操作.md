---
title: Java文件操作
author: 熊滔
commentid: javase:file
---

## File

File 类代表的是文件或者文件夹(目录)，将目录和文件抽象为一个类。File 提供了很多方法用来操作文件夹或者文件。下面具体介绍该类。

### 静态成员变量

File 类有四个成员变量

- pathSeparator
- pathSeparatorChar
  - 这两个保存的路径分隔符
  - Windows 下为`;`，Linux 下为`:`
- separator
- separatorChar
  - 这两个保存的是文件名称分隔符
  - Windows 下为`\`反斜杠，Linux 下为`/`正斜杠

下面看这个例子

```java
import java.io.File;

public class TestFile {
    public static void main(String[] args) {
        System.out.println(File.pathSeparator);
        System.out.println(File.pathSeparatorChar);

        System.out.println(File.separator);
        System.out.println(File.separatorChar);
    }

}
```

输出为

```java
;
;
\
\
```

### 构造方法

#### File(String pathname)

- 传入一个字符串类型，这个字符串为第一个路径名
- 传入的路径可以是绝对路径，也可以是相对路径
- 传入的路径可以存在，也可以不存在
- 可以是文件结尾，也可以是文件夹结尾`\`

下面来看看该方法的使用

```java
File file1 = new File("G:\\JavaProject\\FirstProject"); //绝对路径写法
System.out.println(file1);
File file2 = new File("SecondProject"); //相对路径写法
System.out.println(file2);
```

输出为

```java
G:\JavaProject\FirstProject
SecondProject
```

**注意：**

- 由于`\`代表的是转义，所以要写两个`\\`
- 可见 File 类重写了 toString()方法

#### File(String parent,String child)

- 该构造方法需要传入两个参数，一个为父级目录的路径，一个为子级目录的路径
- 该种构造方法相比上面的要灵活一点

下面示例使用

```java
String parent = "C:\\JavaProject\\";
File file3 = new File(parent,"FirstProject");
System.out.println(file3);
File file4 = new File(parent,"SecondProject");
System.out.println(file4);
```

输出为

```java
C:\JavaProject\FirstProject
C:\JavaProject\SecondProject
```

#### File(File parent, String child)

- 同上个构造方法，传入也是父级和子级的路径，不过传入的父级路径是一个 File 对象
- 这样可以调用 File 类的方法，更加灵活

下面示例使用

```java
File parent = new File("C:\\JavaProject");
File file5 = new File(parent,"FirstProject");
System.out.println(file5);
```

输出为

```java
C:\JavaProject\FirstProject
```

### 常用方法

按照功能不同分为了三类

- 获取功能
- 判断功能
- 创建删除

#### 获取功能

与获取功能有关的方法有四个

- getAbsolutePath()
  - 获得文件或文件夹的绝对路径
  - 不论在构造方法中传入的是绝对路径还是相对路径，获得都是绝对路径
- getPath()
  - 在构造方法中传入的是什么，返回的就是什么
  - toString()调用的就是这个方法
- getName()
  - 获得文件或文件夹的名称，即路径的结尾部分
- length()
  - 获得文件的长度，以字节为单位
  - 如果该文件不存在，那么返回 0

下面示例这四个方法的使用

```java
import java.io.File;

public class TestFile {
    public static void main(String[] args) {
        File file = new File("src\\Dog.java"); //传入的相对路径，获得是src目录下的Dog.java文件
        //打印绝对路径
        System.out.println(file.getAbsolutePath()); //G:\JavaProject\SecondProject\src\Dog.java
        //打印路径，传入什么打印什么
        System.out.println(file.getPath()); //src\Dog.java
        //获得文件的名称
        System.out.println(file.getName()); //Dog.java
        //获得文件的大小
        System.out.println(file.length()); //353
    }
}
```

#### 判断功能

与判断功能有关的方法有三个

- exists()
  - 判断传入构造方法的路径是否存在
- isDirectory()
  - 判断是不是目录(文件夹)
  - 如果路径不存在，无论是否为目录(文件夹)，返回 false
- isFile()
  - 判断是否为文件
  - 如果路径不存在，无论是否为文件，返回 false

下面进行示例

```java
File file = new File("src\\Dog.java"); //传入的相对路径，获得是src目录下的Dog.java文件
//判断该路径是否存在
System.out.println(file.exists()); //true
//是否为文件夹
System.out.println(file.isDirectory()); //false
//是否为文件
System.out.println(file.isFile()); //true
```

#### 创建删除

与创建和删除有关的方法有四个

- createNewFile()
  - 如果该文件不存在，则创建该文件，如果存在则不创建
  - 返回布尔值，创建返回 true
  - 不能创建目录，所以该文件所在目录必须存在，否则会抛出异常
- delete()
  - 删除该类代表的文件或文件夹，直接从硬盘删除，不走文件夹，要小心
  - 删除成功返回 true
- makdir()
  - 创建由此 File 类代表的目录，只能创建单级目录
- makdirs()
  - 创建由此 File 类代表的目录，包括必需但不存在的父级目录

下面演示使用

```java
import java.io.File;
import java.io.IOException;

public class TestFileMethod {
    public static void main(String[] args) throws IOException {
        //D:\\目录下没有1.txt
        File file1 = new File("D:\\1.txt");
        //createNewFile使用了throws抛出异常，需要处理，我们在main中直接抛出即可
        System.out.println(file1.createNewFile()); //true

        //D:\\下没有新建文件夹
        File file2 = new File("D:\\新建文件夹");
        //创建这个文件夹
        System.out.println(file2.mkdir());  //true

        File file3 = new File("D:\\abc\\def");
        //创建多级目录
        System.out.println(file3.mkdir()); //不能创建 mkdir只能创建单级目录 false
        System.out.println(file3.mkdirs()); //true

        //将上面创建的文件及文件夹全部删除
        System.out.println(file1.delete()); //true
        System.out.println(file2.delete()); //true
        System.out.println(file3.delete()); //true abc这个文件夹还在
    }
}
```

### 目录遍历

为了遍历目录，有两个方法

- list()
  - 返回一个字符串数组，这些字符串是文件名或文件夹名
- listFiles()
  - 返回时的 File 类对象数组

**注意：**

- 上面两个方法，如果目录不存在或者不是目录，那么会抛出空指针异常
- 隐藏文件和文件夹也能获取到

```java
File file = new File("G:\\JavaProject\\SecondProject\\src");
String[] filename = file.list();
for (int i = 0; i < filename.length; i++) {
    System.out.println(filename[i]);
}
File[] files = file.listFiles();
for (File file4 : files) {
    System.out.println(file4.getName());
}
```

输出为

```java
com
Dog.java
Doudizhu.java
GenericsDemo.java
JustForFun.java
MyException.java
MyThread.java
...
```

## 递归

递归就是在函数里面调用自己。

- 递归必须要用终止条件，否则会不断的调用自己，造成栈内存溢出
- 递归的次数也不能太多，否则也会造成栈内存溢出
- 构造方法

下面做几个小的练习来熟悉递归。

### 递归练习

第一个练习是根据函数接收的参数$n$，来计算$1 + 2  ... + n$，因为我们是从递归的角度看，所以应当这么看

$$
sum(n) = \begin{cases}1, &if \,\, n =1 \\
n + sum(n - 1), &if \,\,n > 1 \end{cases}
$$

所以程序我们应该这么写

```java
//这里没有考虑输入为负数时的处理
public static int sum(int n) {
    if (n == 1) {
        return 1;
    } else {
        return n + sum(n - 1);
    }
}
```

在 main 方法中调用该方法测试

```java
int res = sum(100);
System.out.println(res);
```

输出为

```java
5050
```

可见程序是正确的。

第二个练习是计算阶乘，思路同上面完全是一样的

$$
\text{fac}(n) = 
\begin{cases}
1, &\text{if} &n =0 \\
n * \text{fac}(n - 1), &\text{if} &n > 1
\end{cases}
$$

代码如下

```java
public static int fac(int n) {
    if (n == 0) {
        return 1;
    } else {
        return n * fac(n - 1);
    }
}
```

在 main 方法中调用测试

```java
int res = fac(5);
System.out.println(res);
```

输出为

```java
120
```

### 文件夹遍历

下面使用递归来实现文件夹的遍历，我们 getFile()文件来进行文件夹的遍历，它接收一个 File 对象作为参数

```java
public static void getFile(File file) {
    File[] files = file.listFiles();
    for (File file1 : files) {
        System.out.println(file1.getName());
        //如果是文件夹的，就遍历该文件夹
        if (file1.isDirectory()) {
            getFile(file1);
        }
    }
}
```

我在 D 盘下新建了一个 Test 文件夹，它的目录结构如下

```java
Test
	a
		a.txt
		b.txt
	b
		c.txt
		d.txt
```

我们在 main 方法中遍历该文件夹

```java
getFile(new File("D:\\Test"));
```

输出为

```java
a
a.txt
b.txt
b
c.txt
d.txt
```

## 过滤器

listFiles 其实还可以接收一个叫做过滤器的参数。这个过滤器是一个接口，分为有 FileFilter 和 FilenameFilter 两个接口，这两个接口里面都只有一个方法 accept()，listFiles 会调用这个方法，将它获得的路径传入该方法中，如果得到的结果为 true，那么就保留这个路径在最终返回的数组中，如果返回的是 false，那么就不要这个路径。可见这样就起到了文件过滤的作用，而过滤什么样的文件，需要什么样的文件，完全由 accept()方法决定，所以这也是它们为什么叫做过滤器的原因。

### FileFilter

下面来介绍 FileFilter，FileFilter 里面的 accept(File pathname)接收一个参数，这个参数就是路径名，listFiles()会将它获得的路径传入 accept()，accept()进行过滤，以决定要什么样的路径。下面我们做一个示范，打印出一个图片文件夹里面以.png 结尾的图片

```java
//传入的是图片文件夹的路径
public static void printPNG(File file) {
    //因为接口里面只要一个方法，所以这里使用Lambda表达式
    File[] files = file.listFiles(pathname -> {
        //判断是不是以.png结尾
        boolean b = pathname.getName().endsWith(".png");
        if (b) {
            //如果是以.png结尾，则加入到files数组中
            return true;
        } else {
            return false;
        }
    });
    //打印files数组，查看结果是否正确
    for (File fi : files) {
        System.out.println(fi.getName());
    }
}
```

我们在 main 方法调用测试

```java
printPNG(new File("D:\\images"));
```

打印输出为

<center>
     <img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java39.png"/>
</center>

可见只有.png 结尾的图片才被放入了 files 数组中。

### FilenameFilter

它的 accept(File dir, String name)接收两个参数，其余的与 FileFilter 相同，下面就同样的功能演示其代码

```java
public static void printPNGAgain(File file) {
    File[] files = file.listFiles(((dir, name) -> {
        //根据dir和name创建一个File对象
        //后面的代码完全同上面一样
        File newFile = new File(dir,name);
        boolean b = newFile.getName().endsWith(".png");
        if (b) {
            return true;
        } else {
            return false;
        }
    }));
    for (File fi : files) {
        System.out.println(fi.getName());
    }
}
```

在 main 方法中调用该方法

```java
printPNGAgain(new File("D:\\images"));
```

输出同上面一模一样

<center>
     <img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java39.png"/>
</center>

## 字节流

下面介绍两个类用来从文件中读取数据和写入数据到文件的类。不管是读取数据还是写入数据，都是以字节为单位的。

### OutputStream

该类可以将内存中的数据写入到文件中。OutputStream 类是一个抽象类，我们可以使用它的子类 FileOutputStram，它的构造方法中传入的参数可以是一个 File 对象，也可以是一个代表路径的字符串。它里面主要有以下方法

- write(byte b)
  - 写入一个字节到文件中
- write(byte[] bytes)
  - 将一个字节数组写入到文件中
  - 如果传入的 byte 是一个负数，那么该数与其后面的那个字节组成一个中文
- write(byte[] bytes, int off, int len)
  - 写入字节数组索引从 off 开始，长度为 len 字节的数据
- close()
  - 关闭流

下面示例其使用

```java
//如果没有a.txt，会创建一个a.txt新文件
OutputStream fos = new FileOutputStream(new File("a.txt")); //可以传入"a.txt"字符串
fos.write(97); //写入a
//\r\n代表 回车换行
fos.write("\r\n".getBytes()); //String类的getBytes()方法可以得到一个byte数组
fos.write("abc".getBytes());
fos.close();
```

上面的程序是会把原来文件里面的内容情空，然后将数据写入了，如果想向文件中追加数据的话，那么就要在构造方法的第二个参数传入 true，代表是追加。如

```java
OutputStream fos = new FileOutputStream(new File("a.txt"), true);
```

### InputStream

与 OutputStream 相对的，该类的作用是读取文件中的内容，一般我们使用的是其子类 FileInputStream，构造方法同 OutputStream，包含的方法有

- read()
  - 读取一个字节并返回，如果读到了文件的末尾，那么返回-1，我们可以通过返回的是否是-1 来判断是否已经读到了文件的末尾
- read(byte[] bytes)
  - 读取 bytes 大小的字节，返回的是读取的有效位数
  - 假如文件有 5 个字节，我用长度为 6 的字节数组去读取，那么返回的就是 5
  - 如果已经读到了文件的末尾，不是返回 0，而是返回-1。
- close()
  - 关闭流

```java
InputStream fis = new FileInputStream(new File("a.txt")); //可以传入"a.txt"字符串
int len = fis.read(); //读取一个字符，读取的虽然是byte，但是返回时会被提升为int，所以用用int接收
System.out.println((char)len);
fis.close();
```

### 文件复制

综合读取数据和写入数据，我们通过这两个流复制一个文件

```java
OutputStream fos = new FileOutputStream(new File("copy.jpg"));
InputStream fis = new FileInputStream(new File("a.jpg"));
byte[] bytes = new byte[1024]; //用来读取数据的数组，也是写数据的数组
int len = 0;
// 判断是否读取完毕
while ((len = fis.read(bytes)) != -1) {
    fos.write(bytes,0, len);
}

//先关闭写的流，因为写完了，说明肯定读完了
fos.close();
fis.close();
```

## 字符流

字节流是一个字节一个字节读取的，但是对于中文，如果使用 GBK 编码的话由 2 个字节组成，如果使用 UTF-8 编码的话由 3 个字节组成。所以如果一个字节进行读取的话就得不到想要的字符。那么这里就需要引入一个新的流来读取字符，以字符为单位进行读取。

### Reader

Reader 就相当于是 InputStream，它的作用就是从文件中读取数据到内存，不过不同的是，Reader 读取的最小单位为字符，它的构造方法与 InputStream 一样，包含的方法也一样，不过参数有所不同，不在是字节，而是字符。Reader 也是一个抽象类，我们在这里常使用它的子类 FileReader。我们在本目录下新建一个文件 b.txt，在里面写入你好，使用 UTF-8 编码(不要使用 GBK)

```java
Reader reader = new FileReader("b.txt"); //也可以传入一个File对象
int c = 0;
while ((c = reader.read()) != -1) {
    System.out.println((char)c);
}
```

输出为

```java
你
好
```

FileReader 其实也有新建一个字节流去读取数据，不过中间有一个将字节转为字符的过程，转换默认是按照 UTF-8 的方式解码的，所以如果你在 Windows 下直接新建文件，默认是 GBK 的，这样读取就会是乱码，可以直接在 IDEA 中新建文件，默认为 UTF-8 编码。

如果我们读取得到了一个字符数组，我们可以使用 String 类的构造方法

- String(char[] value)
- String(char [] value, int off, int len)

将字符数组转化为字符串，方便处理。

### Writer

Writer 就是向文件中写入一个字符，同 OutputStream 不同，Writer 有一个特有的方法

- flush()
  - 该方法的作用是将缓存区里的内容写入到文件中

这是怎么回事，其实 Writer 的底层是创建一个了字节流写数据的，我们直接写入字符不是直接写在文件中，而是写在缓冲区中，然后缓冲区中的内容转化为字节写入文件中。在调用 close()，在关闭流之前，会把缓冲区中的内容写到文件中，如果既没有调用 flush()也没有调用 close()，那么写入的数据不会写入文件中。

还有一个方法是

- write(String str [, int off, int len])
  - 可以直接写入字符串，不用写字符数组那么麻烦了

```java
//如果没有c.txt，那么会新建一个
Writer writer = new FileWriter("c.txt");  //也可以传入一个File对象
//写入的默认是utf-8编码
writer.write("你好啊");
//如果不调用close()，那么c.txt里面什么都没有
writer.close();
```

同 OutputStream 一样，这里的 write 也是覆盖重写，如果想要只是追加数据的话，在构造方法的第二个参数传入 true

```java
Writer writer = new FileWriter("c.txt",true);
```

## Properties 属性集

Properties 是继承至 HashTable 的一个集合。它是与 IO 流关联起来的，通过 load()和 store()方法可以读取特定格式的文件数据，和将 Properties 集合中的数据写入到文件中。

### store

下面介绍将 Properties 中的数据写入到文件中。首先介绍如何向 Properties 集合中添加数据

- setProperty(String key, String value)

Properties 集合中的键和值都是 String 类型的。store()分为两种，一种是字节流，传入的是 OutputStream 对象，一种是字符流，传入的是 Writer 对象

- store(OutputStream in, String comment)
- store(Writer writer, String comment)

第二个参数 String comment 是注释，写入文件会写在第一行，并且以#开头，一般我们传入一个空字符串

```java
Properties properties = new Properties();
properties.setProperty("古力娜扎","18");
properties.setProperty("迪丽热巴","17");
properties.store(new FileWriter("properties.txt"), " ");
```

properties.txt 文件里面的内容为

```java
#
#Fri Aug 09 11:28:20 CST 2019
古力娜扎=18
迪丽热巴=17
```

### load

在介绍 load()之前，说明一下如何获取 Properties 中的元素

- getProperty(String key)
  - 通过键获取值，相当于 Map 中的 get()
- stringPropertiesName()
  - 相当于 Map 中的 keySet()，返回一个 Set 集合，泛型为 String

下面介绍 load()方法，load()就是读取指定格式的文件，比如，指定格式如下两种

```java
古力娜扎=18
```

或者

```java
古力娜扎 18
```

中间的分隔符可以使用=或者空格，如果碰到以#开头的，则不会读取，我们来读取刚刚写的 properties.txt 文件

```java
Properties properties = new Properties();
//读取文件
properties.load(new FileReader("properties.txt"));
Set<String> propertiesName = properties.stringPropertyNames();
//遍历集合
for (String name : propertiesName) {
    System.out.println(name+"="+properties.getProperty(name));
}
```

输出为

```java
古力娜扎=18
迪丽热巴=17
```

## 缓冲流

之前我们使用过字节流 InputStream 和 OutputStream 进行过文件复制的练习，但是其实我们可以发现，使用这两个流的速度很慢，所以这里就引入了缓冲流 BufferedInputStream 和 BufferedOutPutStream，还有 BufferedReader 和 BufferedWriter。为了感受字节流和缓冲流的差异，我们这次来复制一个 1MB 的文件。

### 字节流复制

首先使用字节流，一个字节一个字节的读取

```java
long start = System.currentTimeMillis();
InputStream fis = new FileInputStream("file.pdf");
OutputStream fos = new FileOutputStream("copy.pdf");
int len = 0;
while ((len = fis.read()) != -1) {
    fos.write(len);
}
fos.close();
fis.close();
long end = System.currentTimeMillis();
System.out.println("共耗时" + (end - start) + "毫秒");
```

输出为

```java
共耗时11738毫秒
```

现在还是使用字节流，不过一次读取 1KB

```java
long start = System.currentTimeMillis();
InputStream fis = new FileInputStream("G:\\JavaProject\\ThirdProject\\src\\file.pdf");
OutputStream fos = new FileOutputStream("copy.pdf");

int len = 0; //存储读取的有效位数
byte[] bytes = new byte[1024]; //读取1KB
while ((len = fis.read(bytes)) != -1) {
    fos.write(bytes,0,len);
}
fos.close();
fis.close();
long end = System.currentTimeMillis();
```

输出为

```java
共耗时203毫秒
```

### 缓冲流复制

这次使用缓冲流，先一个字节一个字节的读取

```java
long start = System.currentTimeMillis();
BufferedInputStream buffis = new BufferedInputStream(new FileInputStream("file.pdf"));
BufferedOutputStream buffos = new BufferedOutputStream(new FileOutputStream("copy.pdf"));
int len = 0;
while ((len = buffis.read()) != -1) {
    buffos.write(len);
}
buffos.close();
buffis.close();
long end = System.currentTimeMillis();
System.out.println("共耗时" + (end - start) + "毫秒");
```

输出为

```java
共耗时94毫秒
```

这次一次读取一 1KB

```java
long start = System.currentTimeMillis();
BufferedInputStream buffis = new BufferedInputStream(new FileInputStream("file.pdf"));
BufferedOutputStream buffos = new BufferedOutputStream(new FileOutputStream("copy.pdf"));
int len = 0;
byte[] bytes = new byte[1024];
while ((len = buffis.read(bytes)) != -1) {
    buffos.write(bytes,0,len);
}
buffos.close();
buffis.close();
long end = System.currentTimeMillis();
System.out.println("共耗时" + (end - start) + "毫秒");
```

输出为

```java
共耗时16毫秒
```

下面我来做一个表格总结一下速度

|     | 缓冲流(ms) | 字节流(ms) |
| --- | ---------- | ---------- |
| 1MB | 94         | 11738      |
| 1KB | 16         | 203        |

从这些数据看，就知道缓冲流的速度比字节流的快很多。

### 缓冲流的一些特有方法

缓冲流提供了几个特有的方法，如 BufferedOutputStream 和 BufferedReader 中有一个方法为

- readline()
  - 一次读取一行数据，读到的数据不包括换行符
  - 如果读到了末尾，那么返回 null

BufferedInputStream 和 BufferedWriter 也提供了一个方法叫做

- newline()
  - 作用是换行，可以根据不同的操作系统进行换行
  - 之前我们写的`\r\n`只适用于 Windows 系统

## 转换流

我们在使用字符流时，只能读取 UTF-8 格式的文件，写出的数据的格式也是 UTF-8 的，现在假设我要读取一个 GBK 的文件，里面有中文"你好"，看看会发生什么。

```java
FileReader reader = new FileReader("text.txt");
int len = 0;
while ((len = reader.read()) != -1) {
    System.out.print((char)len);
}
reader.close();
```

发现读取的只是乱码

<center>
<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java40.png"/>
</center>

这是因为 FileReader 是按照 UTF-8 的格式读取的，但是文件的格式却是 GBK，编码不一样，那么解码当然会出问题。所以就有了转换流的出现，有两个类是做这件事情的，分别是 InputStreamReader 和 OutputStreamWriter，它们的构造方法是

- InputStreamReader(InputStream in, String charset)
- OutputStreamWriter(OutputStream out, String charset)

第二个参数就是用来指定字符集的，如果不指定字符集的话，就默认为 UTF-8。

```java
String path = "text.txt";
InputStreamReader reader = new InputStreamReader(new FileInputStream(path), "gbk");
int len = 0;
while ((len = reader.read()) != -1) {
    System.out.print((char) len);
}
reader.close();
```

输出为

```java
你好
```

下面做一个小的练习，将一个 GBK 格式的文件转换为一个 UTF-8 格式的文件

```java
String path = "text.txt";
//也可写成GBK
InputStreamReader reader = new InputStreamReader(new FileInputStream(path), "gbk");
//不指定字符集的话，默认为UTF-8(utf-8)
OutputStreamWriter writer = new OutputStreamWriter(new FileOutputStream("test.txt"));
int len = 0;
while ((len = reader.read()) != -1) {
    writer.write(len);
}
writer.close();
reader.close();
```

## 序列流

如果我们想将对象保存在硬盘中怎么办? 因为一旦断电，内存中的对象就会全部的消失。这个时候就需要序列流将对象保存在文件中，对应也有相应的序列流读取文件得到一个对象。

- ObjectOutputStream
  - 将文件写到文件中的类
  - writeObject(Object o)
    - 该方法将对象写到文件中
- ObjectInputStream
  - 从文件中读取对象的类
  - readObject()
    - 将文件中的对象读取出来，返回一个 Object 对象

下面介绍使用及注意事项。首先创建一个 Person 类

```java
package IOProject

public class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.age = age;
        this.name = name;
    }

    public Person() {
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "Person{" +
                "age=" + age +
                ", name='" + name + '\'' +
                '}';
    }
}
```

然后在测试类中创建一个对象，并使用 ObjectOutputStream 类的对象将这个对象写到文件中

```java
ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("person"));
Person p = new Person("迪丽热巴",18);
oos.writeObject(p);
oos.close();
```

这时抛出了一个异常

<center>
<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java41.png"/>
</center>

这时因为 Person 没有实现 Serializable 接口，这里谈第一个注意事项

- 只有实现了 Serializable 接口才能将其对象序列化和反序列化
- Serilaizable 是一个标志性接口，所谓的标志性指的是只起一个标志的作用，Serializable 接口里面什么都没有，我们不需要实现任何的方法

现在将 Person 实现 Serializable 接口然后执行上面的程序就不会有问题了。

下面使用 ObjectInputStream 读取刚刚序列化的对象，这个过程叫做反序列化

```java
ObjectInputStream ois = new ObjectInputStream(new FileInputStream("person"));
Object o = ois.readObject();
System.out.println(o);
ois.close();
```

输出为

```java
Person{age=18, name='迪丽热巴'}
```

说明我们读取成功了。

下面添加几个注意事项

- static 修饰的静态变量不能进行序列化
- 被 transient 修饰的成员变量也不能进行序列化
- 如果对象序列化后，修改了类文件，那么不能被序列化

现在我们修改 Person 类中的 age 使用 transient 修饰，进行序列化和反序列化操作，得到的结果为

```java
Person{age=0, name='迪丽热巴'}
```

age = 0 并不等于 18，说明 age 没有被序列化到文件中。

现在我们将 Person 类的对象序列化，然后修改 Person 类，接着在反序列化

```java
Exception in thread "main" java.io.InvalidClassException: IOProject.Person; local class incompatible: stream classdesc serialVersionUID = -774581383406272369, local class serialVersionUID = -4004215360553243182
	at java.io.ObjectStreamClass.initNonProxy(ObjectStreamClass.java:699)
	at java.io.ObjectInputStream.readNonProxyDesc(ObjectInputStream.java:1885)
	at java.io.ObjectInputStream.readClassDesc(ObjectInputStream.java:1751)
	at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:2042)
	at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1573)
	at java.io.ObjectInputStream.readObject(ObjectInputStream.java:431)
	at IOProject.TestSerial.main(TestSerial.java:13)
```

抛出了一个异常 InvalidClassException，这是因为它们的 serialVersionUID 对不上。serialVersionUID 是根据类文件自动计算的，当我们修改类文件时，serialVersionUID 发生了改变，当我们反序列化时会比较 serialVersionUID，由于这时它们的 serialVersionUID 不同，所以抛出了这个异常。我们可以在类里面为这个变量赋一个固定的值，这样 serialVersionUID 就不会发送改变，但是赋值也是有要求的

- 必须使用 final static long 修饰

我们在 Person 类中加入

```java
private final static long serialVersionUID = 1L;
```

然后执行上面的操作，发现没有问题。输出为

```java
Person{age=18, name='迪丽热巴'} //这里age没有使用transient修饰
```

## 打印流

PrintStream 是一个有关于打印流的类，我们一直使用的 System.out 其中的 out 就是一个 PrintStream 对象，下面是其在 System 类中的定义

```java
public final static PrintStream out = null;
```

打印流有以下的特点

- 它只负责数据的输出
- 永不抛出 IOExecption
- 它继承了 OutputStream 类
- 它有自己的特有方法，如 print(), println()，这两个方法可以打印出任意的数据类型
- 当它调用 OutputStream 类的方法 write()时，打印数据时会查编码表，如 write(97)会打印出 a，但是 print(),println()方法输入什么，打印出什么，如 print(97)打印出 97。

下面简单介绍使用，首先看 PrintStream 的构造方法

- PrintStream(File file)
- PrintStream(OutputStream out)
- PrintStream(String filename)

```java
PrintStream printStream = new PrintStream("a.txt");
printStream.write(97);
printStream.println(97);
printStream.println(22.2);
printStream.println(true);
printStream.println('a');
printStream.println("abc");
printStream.close();
```

打开 a.txt 文件，里面的内容为

```java
a97
22.2
true
a
abc
```

System 类有一个方法 setOut(PrintStream out)，可以用来改变 System.out 的指向，这样打印出的内容不会在控制台显示，而是会在文件中

```java
PrintStream printStream = new PrintStream("a.txt");
System.setOut(printStream);
System.out.println("怎么了，你累了，说好的幸福呢？");
```

运行程序发现控制台没有任何的输出

<center>
<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java42.png"/>
</center>

而在 a.txt 中出现了打印的语句。


<Disqus />