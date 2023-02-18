## 第一个程序: HelloWorld

下面将书写Java的第一个程序，它的作用是在命令行输出Hello World!。新建一个文件，文件名为HelloWorld.java，使用记事本或其他代码编辑工具打开，敲入以下代码

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello World!");
    }
}
```

然后在命令行中输入

```powershell
javac HelloWorld.java
java HelloWorld
```

然后就可以在命令行中看到Hello World!输出了。

**注意事项：**

- class后面的HelloWorld要与文件名一样，包括大小写
- 在命令行使用javac或者java命令时，要保证所处的路径与文件HelloWorld.java在同一个路径，否则会报错，解决办法有两种
  - 第一种，使用cd命令切换到HelloWorld.java文件的目录
  - 第二种，javac或者java命令后跟上HelloWorld.java的完整路径名称
- javac命令后面有后缀.java，java命令后面没有任何的后缀

现在稍微解释上面程序的意思，因为刚刚起步，很多东西现在都解释不了，所以在这里并不要求弄懂。第一行

```java
public class HelloWorld
```

这里，我们只需要记住HelloWorld必须与文件名**相同**。第二行

```java
public static void main(String[] args)
```

这个是固定的写法，几乎每个程序都是这么写的，这个是程序的入口，程序从这里开始执行。第三行

```java
System.out.println("Hello World!");
```

这个语句是关键的程序，它的作用就是向屏幕输出字符，这里我们输入的是Hello World!，注意输出的语句需要被双引号括起来。

## 标识符与命名规范

标识符的命名规范

- 由数字、字母、下划线和美元符组成
- 不能由数字开头
- 不能是关键字

上面是标识符命名的硬性规范，即如果不按照上面的来做，那么程序在编译时不能够通过，下面介绍软性规范，意思就是如果你不按照下面的来，编译器不会报错，但是软性规范是大家约定俗成的，大家都遵守的，所以我们要按照下面的来

- 类名：大驼峰命名法，即首字母**全部大写**，如HelloWorld
- 变量名与方法名：小驼峰命名法，即第一个首字母小写，后面的首字母全部大写，如helloWorld

## Java中的常量

Java中的常量分为：

- 字符串常量：使用双引号括起来的，如"Hello World!"
- 整型常量：其实就是整数，如5
- 浮点数：小数，如2.5
- 字符常量：用单引号括起来的，单引号里面必须有一个字符，不可以没有，也不可以多于一个，如'ab',''都是错误的，另外这个字符可以是中文，如'中'
- 布尔常量：只有两个值true和false
- 空常量：null，这个在这里不多做介绍

在前面我们学习了向屏幕输出Hello World!，类似的，在这里我们可以尝试着把常量输出出来，新建一个文件为Constant.java，注意前面提及的命名规范，Constant是类名，所以首字母需要大写，然后在该文件中敲入

```java
public class Constant {
    public static void main(String[] args) {
        // 下面我将输出一些常量
        System.out.println("Hello"); // 输出字符串常量
        System.out.println(5);       // 输出整型常量
        System.out.println(2.5);     // 输出浮点型常量
        System.out.println('a');     // 输出字符型常量
        System.out.println(true);    // 输出布尔型常量
    }
}
```

输出为：

```java
Hello
5
2.5
a
true
```

**注意：**

- 不能打印出 `null`，如 `System.out.println(null)` 会报错
- 上面 `//` 后面的内容为注释，编译器会自动的忽略，注释是对程序的解释，是为了帮助人看懂程序的

## 基本数据类型

Java中的数据类型分为基本数据类型和引用数据类型，引用数据类型暂且不提，这里的重点是掌握基本数据类型，基本数据类型分为四类：

- 整型
- 浮点型
- 字符型
- 布尔类型

整型可以细分为：

- byte
- short
- int
- long

上面整型数据类型都表示整数，只是表示的范围大小不一样，byte使用**一个字节**即8bit来表示一个整数的大小，表示的范围为-128~127，short使用**两个字节**来表示，范围比byte大一点，大约在几万左右，int使用**四个字节**来表示，是最常用的数据类型，因为它的范围已经大到足够我们日常使用了，long使用**八个字节**来表示，比int的范围还要大，但是由于使用int已经足够了，从节省空间的角度讲，没必要用long。

浮点型可以分为

- float
- double

float使用**四个字节**来表示小数，虽然使用的是四个字节，但是表示的范围比使用八个字节的long更大，double使用**八个字节**来表示小数，所以使用double表示小数更精确，我们在平常使用最多的就是double。

字符型没法细分，只有一个，那就是char类型，使用两个字节表示。

布尔型也没有办法细分，只有一个，那就是boolean，使用一个字节表示，它只有两个值true和false。

**注意：**

- 字符串不是基本数据类型，它是引用类型，这个后面详细阐述。

## 变量

变量的命名需要服从前面所讲的标识符所需遵从的硬性或软性的规定。变量的声明方法为：

```
数据类型 变量名
```

比如：

```java
int a;
float f;
double d;
```

上面是对变量的声明，但是没有对变量进行赋值，没有被赋值的变量是不能被使用的，否则会报错，下面举一些例子：

```java
byte by = 20;
short s = 10;
int a = 2;
long l = 122222222222222L;
float f = 1.2F;
double d = 2.5;
char c = 'a';
boolean b = true;
```

**注意：**

- 对float变量进行赋值时，后面需要加上F
- 对long类型的变量进行赋值时，后面需要加上L(大小写都可以，不过小写的l像1，所以建议使用大写)，当右边常量的值的大小小于int类型的范围时，L可以省略

```java
long l = 2; 
```

- Java中，整型常量的类型默认为int，浮点型常量的类型默认为double
- 在一个花括号内部，变量名不能相同

## 数据类型转换

数据类型转换分为两种：

- 自动类型转换，也称为隐型转换
- 强制类型转换

### 自动类型转换

当将某数据类型的值赋值给某个能够表示更大范围的数据类型的变量时，会发生自动类型转换，比如

```java
byte b = 20;
int a = b;
```

上面的b是byte类型，a是int类型，int类型的范围更加的大，所以将b赋值给a时会自动类型转换，将20转化为int类型赋值给a，但是b还是byte类型。但是不能将大范围的数据类型的值赋值给更小范围数据类型的变量，如

```java
int a = 20;
byte b = a; //不行，编译器会报错
```

范围从小到大的顺序为

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/Java012022-05-18-23-35-02.png" style="zoom:50%"/>

箭头代表可以转换。

在前面我们提及到，当将一个整数赋值给long类型的变量时，如果表示的范围没有超过int，那么可以不写后面的L，这时因为发生了自动类型转换

```java
long l = 12; //后面没有加L，Java默认整数类型为int，这里发生了自动类型转换
```

### 强制类型转换

上面提及到大范围不能转向小范围，但是我们可以强行转换，比如我想将int类型的数值转化为byte类型的数值

```java
int a = 20;
byte b = (byte) a;
```

在要转换的数据前面加上(byte)就可以强行转化为byte类型。

虽然可以将大范围转化为小范围，但是我们在平常时不会这么干，因为往往将大范围的数转化为小范围的数时，会有精度损失，比如：

```java
double d = 3.5;
int i = (int) d; //结果为3，小数点后面的数直接舍去，并不是四舍五入
```

我们知道Java的整数默认为int，那么下面的语句为什么没有报错

```java
byte b = 20; //20是int类型，int不能转byte，为什么没有报错
short s = 30; //同上
```

这是因为，在编译时，编译器会检查右边数值的范围，如果没有超过byte或者short能够表示的大小，那么编译器会自动帮我们强制类型转换为byte或short，相当于

```java
byte b = (byte) 20;
short s = (short) 30;
```

### 几点注意

1. char,short,byte类型在进行计算时，会自动提升为int类型参与计算

```java
byte num1 = 10;
byte num2 = 20;
//下面这行语句会报错
byte num3 = num1 + num2; //在计算时，num1和num2会自动的提升为int类型，而int类型不能转为byte，所以会报错
```

但是，下面的写法是正确的

```java
byte b = 1 + 2;
```

当右边全是常量时，编译器在编译时会直接计算，相当于下面

```java
byte b = 3;
```

由于3没有超出byte能够表示的范围，所以会被强制转换为byte。

```java
char c = 'A';
//下面这行语句会输出一个数字
System.out.println(c + 0); //65
```

我们知道char是一个字符，那么为什么字符可以进行计算呢？这是因为计算机在底层是使用数字表示字符的，比如上面'A'就是使用数字65表示的，上面的字符与数字0进行计算，会自动提升为int类型，所以最后输出的就是数字。

我们需要知道几个特殊字符对应的数字

| 字符  | ASCII码 |
| ----- | ------- |
| '0' | 48      |
| 'A' | 65      |
| 'a' | 97      |

大写字母与小写字母之间差32。

1. boolean类型不能进行数据类型转换

例如，下面将对boolean类型进行转换，是错误的写法：

```java
int i = 1;
boolean b1 = i; //错误
boolean b2 = (boolean) i; //错误
```

```java
boolean b = true;
int i1 = b; //错误
int i2 = (int) b; //错误
```

## 运算符

Java中的运算符分为以下几种：

- 算术运算符
- 赋值运算符
- 比较运算符
- 逻辑运算符
- 三元运算符

### 算术运算符

算术运算符包括以下几种：

- +，加法
- -，减法
- *，乘法
- /，除法
- %，取余
- ++，自增
- --，自减

不同类型的数据之间进行运算，得到的结果的数据类型为范围最大的那个数据类型，如：

```java
double d = 2 + 3.5; //int + double ==> double
int i = 2 + 3.5; //该写法错误
```

加法有两种作用：

- 两数字间进行加法运算
- **字符串的连接**

```java
int c = 3 + 5;  //进行加法运算，结果为8
String s = "Hello" + "World"; //拼接为HelloWorld
```

**注意：任何数据类型与字符串连接时，会自动变成字符串**

```java
String s = 1 + "Hello"; //1Hello
```

除法需要注意的是，**两个整数相除，得到的还是整数**，即使除不尽，如

```java
int a = 10/3; //a = 3
```

下面重点介绍++和--，由于二者十分的相似，这里只介绍++。++的作用是自动加1，如

```java
int a = 3;
a++; //相当于a = a + 1 a变为了4
```

其中++可以在变量的前面，也可以在变量的后面，如下

```java
a++;
++a;
```

二者的作用均是使变量a加1，当二者如上单独使用时，二者没有区别，但是当混合使用时，有区别，具体表现如下

- ++a：先将a进行+1，然后使用a
- a++：先使用a，再将其进行+1

例如：

```java
int a = 3;
b = a++; //先使用a，即先将a的值赋值给b，所以b=3，然后+1，所以最后a=4
```

```java
int a = 3;
b = ++a; //先将a+1，即a=4，然后将a赋值给b，所以b=4
```

--的作用是减1，其用法同++一模一样，这里不多加介绍。

**注意：**

- 常量不能进行++,--运算

```java
30++;  //该写法是错误的
```

### 赋值运算符

赋值运算符分为基本赋值运算符和复合赋值运算符

- 基本赋值运算符
  - =
- 复合赋值运算符
  - +=
  - -=
  - *=
  - /=
  - %=

基本赋值运算符的作用就是把右边的值赋给左边的变量

```java
int a = 3; //把3赋给变量a
```

**注意：**

- 常量是不变的，不能被赋值，所以常量不能出现在赋值运算符的左边

```java
50 = 30; //该写法是错误的
```

复合赋值运算符，这里介绍+=，其他与它类似

```java
int a = 3;
a += 3; //相当于a = a + 3
a *= 3; //相当于a = a * 3
// 其他的同理可以知道... ...
```

**注意：**

- 复合赋值运算符隐含了强制类型转换

```java
short s = 2;
s += 2; //这里暗含了强制类型转换，s + 2得到的是一个int类型，被强制转换为了short类型，所以相当于s = (short) (s + 2);
```

### 比较运算符

比较运算符对操作数进行比较，得到一个boolean数据类型的值，比较运算符包含以下：

- ==：判断两个数是否相等
- \>
- <
- \>=：大于等于
- <=
- !=：判断两个数是否不相等

**注意：**

- 在判断某变量属于某区间，比如是否大于3小于5时，不能写成3 < x < 5，应当使用后面提及的逻辑运算符3 < x && x < 5

### 逻辑运算符

逻辑运算符的对象是boolean数据类型的值，包含下面三个操作

- &&：与操作，当两个操作数同时为true时，结果才为true
- ||：或操作，两个操作数中有一个操作数为true时，结果就为true
- !：非操作，!true = false, !false = true

**注意：**

- 逻辑运算符的操作对象为boolean，所以如果操作对象不为boolean类型时，会报错

```java
boolean b = 5 && 2; //会报错
boolean b = 5 && true; //会报错
```

- **短路**
  - 表达式1 && 表达式2：当表达式1为假时，已经可以判断出结果为假，所以不会对表达式2进行判断，不会执行表达式2
  - 表达式1 || 表达式2：当表达式1为真时，已经可以判断出结果为真，同上。

```java
int a = 3;
System,out.println(false && a++ < 100); //此时进行了短路运算，a++ < 100没有得到执行，所以a = 3
```

```java
int a = 3;
System.out.println(true || a++ < 100); //同上
```

### 三元运算符

这里的三元指的是有三个操作数，上面我们介绍的运算符的操作数要么是一个的，如++,--,!，要么是两个的，如+,-,...，三元运算符的格式是

```java
变量 = 条件判断 ? 表达式1 : 表达式2;
```

首先会进行条件判断，如果条件判断得到的结果为true，那么会将表达式1的结果赋值给变量，此时表达式2不会得到执行；否则将表达式2的结果赋值为变量。

```java
int a= 3;
int b = 4;
int c = 4 > 3 ? a : b++; //c = 3，表达式2没有执行，b = 4
System.out.println(b);   //输出为4
```

**注意：**

- 变量的类型必须与表达式的结果的类型一致，否则会报错

```java
int c = 4 > 3 ? 2 : 2.5; //该语句是错的，2.5是double类型，不能转化为int
```

可能有的人会说返回的2，为什么会报错，因为在实际中，判断语句的结果不是确定的，否则直接将2赋值给c好了，根本不需要判断，因此两个表达式的结果都有可能赋值给变量，编译器会检查表达式得到的数据类型。

- 单独写三元表达式是错误的

```java
int a = 4; b = 3;
a > b ? a : b; //这么单独写是错误的
```

但是可以这么写

```java
System.out.println(a > b ? a : b); //这么写是对的
```

## 流程控制

流程指的就是程序执行的顺序，写过汇编的同学都知道，用汇编写程序的流程控制都是jump的，各种跳来跳去，这会导致程序的流程极其的不清楚，当出现bug时，极其的难以调试，我们称这种程序为“意大利面条”，意思就是像意大利面条一样交缠在一起，极其混乱。

后来人们发现，只需要三种结构就可以写出任何的程序，这三种结构就是**顺序结构，选择结构，循环结构**，使用这三种结构使得程序条理清晰，结构清晰。下面就详细的介绍这三种结构。

### 顺序结构

顺序结构指的就是程序从上到下，按照顺序执行，例如

```java
System.out.println("我是第一条语句");
System.out.println("我是第二条语句");
```

输出为

```java
我是第一条语句
我是第二条语句
```

### 选择结构

有时候我们需要根据某种状态来判断哪段程序应该执行，比如用户输入用户名和密码，根据是否输入正确来决定程序的行为。

选择结构分为两大类，一个与关键字if,else有关，一个与关键字switch,case有关，下面详细进行介绍。

####  if-else语句

##### 单独一个if

使用方法为

```java
if (判断语句) {
    //代码
}
```

对判断语句进行判断，如果结果为真，则执行if里面的代码，如果为假，则跳过整个语句。

##### if-else语句

上面if语句是当结果为真时执行语句块，但是当结果为假却什么都不干，我们希望当结果为假时，也能有相应的操作，那么就要用到if-else语句

```java
if (判断语句) {
    //if语句块
} else {
    //else语句块
}
```

当结果为真时，执行if语句块，结果为假时，执行else语句块。

##### if-else-if else语句级联

有时候，我们不仅需要的是一次判断，而是多个判断，比如有一个分段函数，根据函数的不同取值，选择不同的函数，比如
$$
y = 
\begin{cases}
2x - 1, &x < 1 \\
2x, &1 \leq x < 2 \\
2x + 1, &x \geq 2
\end{cases}
$$

```java
if (x < 1) {
    y = 2 * x - 1;
} else if (1 <= x && x < 2) {
    y = 2 * x;
} else { //x >= 2
    y = 2 * x + 1
}
```

#### switch-case语句

switch-case语句的格式为

```java
switch (表达式) {
    case 常量1:
        语句1;
        break;
    case 常量2:
        语句2;
        break;
    ...
    case 常量n:
        语句n;
        break;
    default:
        语句n+1;
        break;
}
```

该段程序的执行过程为，首先计算switch表达式的结果，然后将表达式的结果与常量1,常量2,...比较，看是否相等，如果与其中某个常量相等，则会执行相应的语句块。如果与所有的case常量都不相等，那么就会执行default中的语句。

**注意：**

- 多个case的常量不能相同
- switch表达式的结果只能是下面的几种类型
  - 基本数据类型：byte,short,char,int
  - 引用类型：String，枚举类型
- case的顺序可以发生改变

另外，比较重要的是，其实break不是必须的，可以省略，如果省略的话，由于没有break，程序会继续执行下去，直到遇到break或者程序执行结束。

```java
switch (1) {
    case 1:
        System.out.println(1); //不会退出程序，继续执行
    case 2:
        System.out.println(2); 
        break;                 //在这里遇到break，退出
    default:
        System.out.println(3);
        break;
}
```

程序的输出为

```java
1
2
```

### 循环结构

循环指的就是重复执行某段程序，一般循环由下面四部分组成

- 初始化
- 条件判断
- 循环体
- 循环控制语句

循环有三种，分别是

- for循环
- while循环
- do-while循环

下面详细介绍。

#### for循环

for循环的格式为

```java
for (初始化语句; 条件判断; 循环控制) {
    循环体;
}
```

执行的顺序为，先执行初始化语句，然后进行条件判断，如果结果为真，那么进入循环体，如果结果为假，那么退出循环，执行完循环体后，接着执行循环控制语句，然后进行条件判断，循环往复。如下面的例子打印出1-100的和

```java
int sum = 0;
for (int i = 1; i <= 100; i++) { //注意，这个i只能在for循环里面用，在外面不能用
    sum = sum + i;
}
```

#### while循环

while循环的格式为

```java
while (判断语句) {
    循环体
}
```

首先会对判断语句进行判断，真就执行循环体，假就退出循环。执行完循环体，接着进行判断，循环往复。下面的例子计算1-100的和

```java
int sum = 0;
int i = 1; //初始化语句
while (i <= 100) { //判断语句
    sum = sum + i; //循环体
    i++; //循环控制语句
}
```

#### do-while循环

do-while循环的格式为

```java
do {
    循环体
} while (条件判断);
```

首先会执行一次循环体，然后进行条件判断，真就继续执行循环体，否则退出循环。



do-while循环与while循环的区别是，do-while一定会执行一次循环体，例如

```java
int i = -1;
do {
    System.out.println(1);
} while (i > 0);
```

输出为

```java
1
```

#### 循环控制break和continue

当在循环体中执行到break时，会直接退出循环，如

```java
for (int i = 1; i <= 10; i++) {
    System.out.println(i);
    if (i == 2) {
        break;      //当i = 2时退出循环
    }
}
```

程序输出为

```java
1
2
```

当在循环体中执行到continue时，退出此次循环，直接进行下一次循环，如

```java
for (int i = 1; i <= 5; i++) {
    if (i == 3) {
        continue; //退出此次循环，后面的代码不执行了，直接执行循环控制语句i++，进入下一次循环
    }
    System.out.println(i);
}
```

输出为

```java
1
2
4
5
```

#### 死循环

当循环一直进行下去，不能退出，那么就成为了死循环，例如下面就是一个死循环

```java
while (true) {
    System.out.println("Hello");
}
for (int i = 0; ; i++) { //省略了条件判断语句，默认为真，是死循环
    System.out.println(i);
}
for (int i = 0; i < 10; ) { //省略了循环控制语句，i一直为0，条件判断一直为真，为死循环
    System.out.println(i);
}
```

#### 循环嵌套

循环里面可以嵌套着一个循环，例如输出九九乘法表

```java
for (int i = 1; i < 10; i++) {
    for (int j = 1; j <= i; j++) {
        System.out.printf("%d*%d=%2d  ",j,i,j*i); //类似于C语言，格式化输出，以便对齐
    }
    System.out.println();
}
```

输出为

```java
1*1= 1  
1*2= 2  2*2= 4  
1*3= 3  2*3= 6  3*3= 9  
1*4= 4  2*4= 8  3*4=12  4*4=16  
1*5= 5  2*5=10  3*5=15  4*5=20  5*5=25  
1*6= 6  2*6=12  3*6=18  4*6=24  5*6=30  6*6=36  
1*7= 7  2*7=14  3*7=21  4*7=28  5*7=35  6*7=42  7*7=49  
1*8= 8  2*8=16  3*8=24  4*8=32  5*8=40  6*8=48  7*8=56  8*8=64  
1*9= 9  2*9=18  3*9=27  4*9=36  5*9=45  6*9=54  7*9=63  8*9=72  9*9=81
```


## 方法入门

### 方法定义

方法定义的格式为

```java
修饰符 返回类型 方法名(参数类型 参数) {
    代码 ...;
    return ...;
}
```

其中修饰符目前固定为public static，方法要符合之前在标识符里面的规定。

注意：

- 方法不能定义在方法里面
- 方法定义的前后顺序无所谓
- 方法应在class和main方法之间定义
- return的作用有两个，第一个是终止程序退出方法，第二个是返回值
- 如果返回值的类型时void，可以只写return;，代表退出方法

```java
public class Hello {
    //定义了一个方法，该方法的作用是计算两个int整数的和，并返回一个int类型的值
    //该方法接收两个int类型的参数，返回一个int类型的值
    public static int add (int x, int y) {
        return x + y;
    }
    
    //main方法的顺序可以和上面add方法的顺序颠倒
    public static void main(String[] args) {
        //... ...
        //不能在里面定义方法
    }
}
```

### 方法调用

方法调用的格式为

```java
方法名(参数);
```

例如，在main方法中调用上面定义的add方法

```java
public class Hello {
    public static int add (int x, int y) {
        return x + y;
    }
    
    public static void main(String[] args) {
        //调用add方法，其中2 和 3是参数，必须是int类型，否则会报错
        int c = add(2, 3); //使用变量c来接受方法返回的结果，此时c = 5
    }
}
```

### 方法重载

方法重载指的是两个方法的方法名相同，但是参数列表不同，参数列表不同包括三个方面

- 参数个数不同
- 参数类型不同
- 参数顺序不同

例如，下面的方法是重载

```java
public static void add (int i, intj);
static int add (double i, double j);
public void add (double i, intj);
```

**注意：**

- 判断方法是否是重载，关键是看方法名是否相同，参数列表是否不同，与修饰符，返回值没有任何的关系

例如，下面的方法不是重载

```java
public static void add (int i, int j);
static void add (int i, int j); //与修饰符无关，参数列表相同，所以不是重载，编译会报错
public static int (int i, int j); //与返回值无关，参数列表相同，所以也不是重载，编译会报错
```

下面举一个注意事项，假设有下面这么一个方法

```java
public static void add (int i, double j);
```

在main方法中，我们可以这么调用

```java
add(2, 2); //我们可以传入两个整数，因为会进行自动类型转换为double
```

然后我们对该方法重载了

```java
public static void add (double i, int j); //参数列表的顺序不同，是重载
```

如果我们还在main方法中传入两个整数

```java
add (2, 2); //这时会报错
```

因为在调用这个方法时，发现两个方法都可以，所以编译器就会感到模糊，不知道调用哪一个方法，就会报错。

## 数组

假设你要保存一组学生的成绩，我们会考虑每个学生用不同的变量保存，如

```java
int score1 = 100;
int score2 = 80;
... ...
```

这样写的话代码很长，并且不利于批量操作，比如如果我要给每个学生加上十分，那我们只能这么写

```java
score1 = score1 + 10;
... ...
```

这只是体力活。

Java中专门有一类数据类型用来保存这样的数据，它叫做数组，下面我们将详细介绍。

### 数组的初始化

数组的初始化分为两组，分别为动态初始化（指定长度）和静态初始化（指定内容）。

#### 动态初始化

动态初始化的格式为

```java
数据类型[] 数组名 = new 数据类型[数组长度];
```

例如，下面声明了一个长度为3的int类型的数组

```java
int[] array = new array[3];
```

动态初始化其实可以分为两部分

```java
int[] array;
array = new array[3];
```

#### 静态初始化

静态初始化的格式为

```java
数据类型[] 数组名 = new 数据类型[] {内容};
```

例如，下面创建了一个String类型的数组，里面有3个字符串，内容之间使用逗号隔开

```java
String[] str = new String[]{ "Hello", "World", "!!!" };
```

编译器会自动计算内容的个数来确定数组的长度。静态初始化还有省略格式

```java
数据类型[] 数组名 = {内容};
```

例如上面的例子用省略格式重写为

```java
String[] str = { "Hello", "World", "!!!" }
```

静态初始化也可以看做是由两部分组成

```java
String[] str;
str = new String[] {  "Hello", "World", "!!!"  };
```

但是省略格式不能这样看，例如下面的写法是错误的

```java
String[] str;
str = { "Hello", "World", "!!!" };
```

**注意：**

- 数组的长度在程序运行期间是固定的，不能够发生改变
- 可以通过数组名.length来得到数组的长度
- 静态初始化会根据内容来自动推算出数组的长度

### 访问数组

我们通过数组名[索引]的方式来访问数组里面的内容，这里需要注意的是索引值是从0开始的，所以索引的范围为**0~数组长度-1**

```java
int[] array = { 1, 2, 3 };
System.out.println(arrays[0]);  //访问数组中的第一个元素，输出1
System.out.println(arrays[1]);  //访问数组中的第二个元素，输出2
```

**注意：**

- 索引的范围不能超过数组的长度，否则会报错

例如对于上面定义的数组，下面的代码会报错

```java
array[3]; //会报错 
```

- 动态初始化有默认值，不同类型的默认值不同，如下

| 数据类型                      | 默认值     |
| ----------------------------- | ---------- |
| 整型(byte,short,int,long) | 0       |
| 浮点型(float和double)   | 0.0      |
| 字符(char)              | '\\u0000' |
| 布尔型(boolean)         | false    |
| 引用内容                      | null     |

- 静态初始化其实也有默认值，不过马上又被赋值被覆盖了

### Java的内存模型

Java的内容模型分为五部分，分别是

- 栈(Stack)
  - 存放的都是局部变量，变量一旦超出作用域，立刻从栈内存中消失
  - 当调用方法运行时，栈会为方法开辟一块空间
- 堆(Heap)
  - 凡是new出来的，都在堆中
  - 堆内存里面的东西都有一个地址值
  - 堆内存里的数据都有默认值，同数组的默认值
- 方法区(Method Area)
  - 存储class相关的信息，包括方法的信息
- 本地方法栈(Native Method Stack)
  - 与操作系统相关
- 寄存器(PC Register)
  - 与CPU相关，速度非常的快

**目前我们需要关注的前三个**。

### 数组的内存图

下面我们来看一段代码，然后从内存的角度看看发生了什么

```java
public static void main(String[] args) {
    int[] array1 = new int[3];
    System.out.println(array1); // 猜猜打印出什么
    
    array1[0] = 10;
    System.out.println(array1[0]);
    
    int[] array2 = array1;
    System.out.println(array2); 
    array2[1] = 200;
    System.out.println(array1[1]);
}
```

上面代码的输出为

```java
[I@15db9742
10
[I@15db9742
200
```

下面将从内存的角度解释，首先第一行

```java
public static void main(String[] args)
```

该方法的信息会保存在方法区中，如果方法区中没有该方法，那么程序会报错。然后找到`main`方法后，这时会把main推入栈中，栈内存会为main方法开辟一块空间

<center>
    <img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java02.png"/>
</center>

```java
int[] array1 = new int[3];
```

这一行是动态初始化数组，首先会在main方法中分配一块空间保存变量array1的值，在堆中会开辟一块空间，空间的大小为3个int的大小，里面的默认值为0，在堆中存放的东西都有一个地址，array1保存的就是这个地址。访问数组就是通过这个地址去访问在堆中保存的数据的。

<center>
    <img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java03.png"/>
</center>

```java
System.out.println(array1);
```

这一行打印变量array1的值，我们知道array1保存的是数组在堆中的地址，所以输出的是[I@15db9742，其中[代表类型为数组，I代表是int类型，@符号后面的十六进制数字是地址的哈希值。

```java
array1[0] = 10;
System.out.println(array1[0]);
```

第一行是对数组中的第一个元素赋值为10，原来数组中的元素默认为0，现在改变为了10。在内存中的过程为，首先根据array1保存的地址去寻找在堆中的数组，然后根据索引值去寻找对应的位置，然后对数据进行修改

<center>
    <img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java04.png"/>
</center>

```java
int[] array2 = array1;
System.out.pritnln(array2);
```

这行语句声明了一个数组变量array2，它的值与array1的值相同，所以输出结果为[I@15db9742。这表示两个数组变量指向在堆中的同一个数组

<center>
    <img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java05.png"/>
</center>

```java
array2[1] = 200;
System.out.println(array1[1]);
```

上面的语句表示通过array2修改数组中的元素array2[1]为200，由于array1和array2指向的是同一个数组，所以array2对数组进行修改也会影响到array1。所以输出的结果是200而不是0。

<center>
    <img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java06.png"/>
</center>

### 数组练习

学习了数组的内容之后，我们做几个练习巩固一下知识。

#### 遍历数组

遍历数组指的就是访问数组中的每一个元素。我们使用for循环，将数组中的每一个元素打印出来。

```java
int[] array = { 1, 4, 7, 9, 5};
for (int i = 0; i < array.length; i++) {
    System.out.println(array[i]);
}
```

输出为

```java
1
4
7
9
5
```

#### 找到数组中的最大值

思路为，想象为这是一个打擂台，数组中的元素就是挑战者，首先让数组中的第一个元素为擂主，后面的挑战者（数组元素）去挑战，比擂主强的话就成为新的擂主，比擂主弱那么擂主就继续在台上，那么最后所有人都挑战完了，那么此刻的擂主就是最强的男人。

```java
int[] array = { 1, 4, 7, 9, 5};
int max = array[0]; //擂主为第一个元素
for (int i = 1; i < array.length; i++) { //挑战者不断挑战
    if (array[i] > max) {
        max = array[i];      //如果挑战者比擂主强，那么换擂主，否则擂主不变
    }
}
System.out.println(max); //此刻的擂主就是最强的
```

输出为

```java
9
```

#### 数组反转

所谓的数组反转就是相对应的位置调换顺序，比如索引为0与索引为array.length-1（最后一个元素）调换位置，一般的索引为i的元素与索引为array.length-1-i的元素交换位置，那么交换的条件就是i < array.length -1 - i得到i < (array.length -1) / 2

```java
int[] array = { 1, 4, 7, 9, 5};
int len = array.length;
for (int i = 0; i < (len - 1) / 2; i++) {
    int temp = array[i];
    array[i] = array[len -1 -i];
    array[len -1 -i] = temp;
}
for (int i = 0; i < len; i++) {
    System.out.println(array[i]);
}
```

输出为

```java
5
9
7
4
1
```

### 数组与方法

数组可以作为函数的参数，也可以作为函数的返回值返回。

#### 方法作为函数参数

下面定义一个方法，该方法的作用是打印出数组里面的所有的元素，该方法接收一个整型的数组参数。

```java
public static void printArray (int[] array) {
    for (int i = 0; i < array.length; i++) {
        System.out.println(array[i]);
    }
}
```

下面在main方法中调用该方法

```java
int[] array = { 1, 2, 3 };
printArray(array);
```

输出为

```java
1
2
3
```

**注意：**

- 数组传递的是地址值，所以printArray方法中的array与main方法中的array指向的是同一个数组，所以如果在printArray方法中对数组元素进行修改的话，会影响到main方法中的array。

  <center>
      <img src="https://gitee.com/lastknightcoder/blogimage/raw/master/img/Java07.png"/>
  </center>

- 随着printArray方法的执行完成，在栈内存中会将为printArray开辟的空间出栈，此时printArray方法中的array变量会立即消失。
- 不仅是数组，引用类型作为方法的参数，传递的都是地址值

#### 数组作为方法返回值

下面有一个方法，该方法接收一个数组，返回该数组的所有数字之和及平均数，由于return语句只能返回0或1个值，要返回两个值的话，我们可以返回一个数组，数组的第一个值表示和，第二个值表示平均数。

```java
public static double[] getSumAndAvg (int[] array) {
    double sum = 0;
    for (int i = 0; i < array.length; i++) { //求和
        sum = sum + array[i];
    }
    double avg = sum / array.length; //得到平均数
    double[] result = { sum, avg };  //创建一个数组，第一个值为sum 第二个值为avg
    return result;
}
```

在main方法中调用该方法

```java
int[] array = { 1, 4, 7, 9, 5};
double[] resArr = getSumAndAvg(array);    //得到数组
System.out.println("Sum is " + resArr[0]); //打印输出和
System.out.println("Average is " + resArr[1]); //打印输出平均数
```

输出为

```java
Sum is 26.0
Average is 5.2
```

注意：

- 方法返回时数组的地址值，内存分析过程同上类似
- 返回引用数据类型，返回的也是地址值



<Disqus />