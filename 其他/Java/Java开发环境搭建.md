---
title: Java开发环境搭建
author: 熊滔
commentid: javase:develop-env
---

Java环境搭建分为三步：

- 下载JDK
- 安装JDK
- 环境变量配置

下面将详细介绍安装的步骤。

## 下载JDK

首先进入[Oracle官网](www.oracle.com)进行下载

<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/20201122095147.png"/>

在搜索框输入 Java，弹出的第一条就是 Java Download，进入页面，选择 JDK 8 进行下载

<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/20201122095334.png"/>

选择符合自己操作系统版本的 JDK 下载

<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/20201122095812.png"/>

勾选同意，并点击下载

<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/20201122095944.png"/>

这时需要你进行登录

<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/20201122100950.png"/>

有账号的登录下载，没账号的注册一个即可。登录成功后就开始下载了

<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/20201122100630.png" width="70%"/>


## 安装JDK

安装JDK就是一直点next即可，因为JDK是包含JRE的，所以JRE可以不必安装。

<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/20201122101330.png" width="60%" />

需要注意的是，不要将安装目录安装在中文目录下，因为可能会碰到各种各样的问题，我们将它扼杀在摇篮里即可。这里需要记住安装的路径，比如我安装的路径是

- D:\Java\jdk1.8.0_271

进入你的安装目录，内容如下

<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/20201122101813.png"/>


## 环境变量配置

现在要进行环境变量的配置，可能你不知道为什么需要进行配置。我们看这么一个情况，你在命令行输入一个命令，比如 notepad(它会打开一个记事本)，但是当你输入一串乱七八糟的字符时，它会提示你

<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/20201122093846.png" width="50%"/>


那命令行怎么知道 notepad 是一个命令，而你乱输入的字符不是命令呢? 因为当你输入字符后，命令行会去一个路径找是否有这个命令，如果有那么执行该命令，没有就会报出上面那样的错误。为了我们能够在命令行中使用有关于 Java 的命令，我们就要将有关 Java 的命令添加到路径中，这个路径就是我们要去配置的东西。那么与Java 有关的命令放在哪里，放在安装目录下的 bin 目录下，然后进入 bin 目录，这个文件夹里面就是有关 Java 的各种命令，我们复制 bin 的路径，如

- D:\Java\jdk1.8.0_271\bin

现在来到桌面，右键我的电脑，点击属性

<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/20201122102238.png" width="35%"/>


然后按照以下步骤

<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/20201122102642.png"/>


会进入下面这个界面

<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/20201122102857.png" width="70%"/>


点击新建，将 bin 目录所在的路径写上去

<img src="https://gitee.com/lastknightcoder/blogimage/raw/master/20201122103214.png" width="50%"/>

接着一路点确定返回。为了检验是否配置成功，我们打开命令行，输入java和javac命令，如果出现下面的命令行输出就说明成功了

```bash
C:\Users\22231
λ java
用法: java [-options] class [args...]
           (执行类)
   或  java [-options] -jar jarfile [args...]
           (执行 jar 文件)
其中选项包括:
    -d32          使用 32 位数据模型 (如果可用)
    -d64          使用 64 位数据模型 (如果可用)
    -server       选择 "server" VM
                  默认 VM 是 server.

    -cp <目录和 zip/jar 文件的类搜索路径>
    -classpath <目录和 zip/jar 文件的类搜索路径>
                  用 ; 分隔的目录, JAR 档案
                  和 ZIP 档案列表, 用于搜索类文件。
    -D<名称>=<值>
                  设置系统属性
    -verbose:[class|gc|jni]
                  启用详细输出
    -version      输出产品版本并退出
    -version:<值>
                  警告: 此功能已过时, 将在
                  未来发行版中删除。
                  需要指定的版本才能运行
    -showversion  输出产品版本并继续
    -jre-restrict-search | -no-jre-restrict-search
                  警告: 此功能已过时, 将在
                  未来发行版中删除。
                  在版本搜索中包括/排除用户专用 JRE
    -? -help      输出此帮助消息
    -X            输出非标准选项的帮助
    -ea[:<packagename>...|:<classname>]
    -enableassertions[:<packagename>...|:<classname>]
                  按指定的粒度启用断言
    -da[:<packagename>...|:<classname>]
    -disableassertions[:<packagename>...|:<classname>]
                  禁用具有指定粒度的断言
    -esa | -enablesystemassertions
                  启用系统断言
    -dsa | -disablesystemassertions
                  禁用系统断言
    -agentlib:<libname>[=<选项>]
                  加载本机代理库 <libname>, 例如 -agentlib:hprof
                  另请参阅 -agentlib:jdwp=help 和 -agentlib:hprof=help
    -agentpath:<pathname>[=<选项>]
                  按完整路径名加载本机代理库
    -javaagent:<jarpath>[=<选项>]
                  加载 Java 编程语言代理, 请参阅 java.lang.instrument
    -splash:<imagepath>
                  使用指定的图像显示启动屏幕
有关详细信息, 请参阅 http://www.oracle.com/technetwork/java/javase/documentation/index.html。

C:\Users\22231
λ javac
用法: javac <options> <source files>
其中, 可能的选项包括:
  -g                         生成所有调试信息
  -g:none                    不生成任何调试信息
  -g:{lines,vars,source}     只生成某些调试信息
  -nowarn                    不生成任何警告
  -verbose                   输出有关编译器正在执行的操作的消息
  -deprecation               输出使用已过时的 API 的源位置
  -classpath <路径>            指定查找用户类文件和注释处理程序的位置
  -cp <路径>                   指定查找用户类文件和注释处理程序的位置
  -sourcepath <路径>           指定查找输入源文件的位置
  -bootclasspath <路径>        覆盖引导类文件的位置
  -extdirs <目录>              覆盖所安装扩展的位置
  -endorseddirs <目录>         覆盖签名的标准路径的位置
  -proc:{none,only}          控制是否执行注释处理和/或编译。
  -processor <class1>[,<class2>,<class3>...] 要运行的注释处理程序的名称; 绕过默认的搜索进程
  -processorpath <路径>        指定查找注释处理程序的位置
  -parameters                生成元数据以用于方法参数的反射
  -d <目录>                    指定放置生成的类文件的位置
  -s <目录>                    指定放置生成的源文件的位置
  -h <目录>                    指定放置生成的本机标头文件的位置
  -implicit:{none,class}     指定是否为隐式引用文件生成类文件
  -encoding <编码>             指定源文件使用的字符编码
  -source <发行版>              提供与指定发行版的源兼容性
  -target <发行版>              生成特定 VM 版本的类文件
  -profile <配置文件>            请确保使用的 API 在指定的配置文件中可用
  -version                   版本信息
  -help                      输出标准选项的提要
  -A关键字[=值]                  传递给注释处理程序的选项
  -X                         输出非标准选项的提要
  -J<标记>                     直接将 <标记> 传递给运行时系统
  -Werror                    出现警告时终止编译
  @<文件名>                     从文件读取选项和文件名
```


如果提示命令找不到之类的，说明配置失败了，就要回头仔细看看是不是漏掉了什么。

<Disqus />