## 安装(Windows)

前往 [MySQL官网](https://www.mysql.com/) 进行下载，下面社区版，版本为 8.0.30。

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1660145809564.png" style="zoom: 50%" />

然后点击安装，一直点击下一步，有几个需要注意的事项：

- MySQL 使用的端口号默认为 3306
    
    <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1660146603749.png" style="zoom: 50%" />

- 需要配置 root 的密码，用以连接数据库
    
    <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1660146730691.png" style="zoom: 50%" />

- 安装程序在系统中注册了一个名为 MySQL80 的服务
    
    <img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1660146777468.png" style="zoom: 50%" />


## 启动/停止服务

有两种方法可以启动/停止服务。

第一种办法，按下 <kbd>Win + R</kbd>，然后输入 `services.msc`，打开服务界面，找到 `MySQL80`，右键手动开启与停止。

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/image-2022081109999.png" alt="image-2022081109999" style="zoom:50%;" />

第二种方法，通过命令行（需要管理员权限）：

```shell {3}
# 启动 MySQL80 服务，不区分大小写
net start mysql80
# 停止 MySQL80 服务
net stop mysql80
```

## 连接 MySQL

首先需要将 `mysql` 添加到 `PATH` 环境变量中，例如我的安装地址是 `C:\Program Files\MySQL\MySQL Server 8.0\bin`，然后通过如下命令连接 MySQL

```shell
mysql -h 主机名 -p 端口号 -u 用户名 -p 密码
```

上述命令包含四个选项：

- `-h`：指定 MySQL 服务器所在的主机，不指定时默认为本机
- `-p`：指定 MySQL 服务器所在的端口号，不指定默认为 3306
- `-u`：指定用户名
- `-p`：指定密码

示例写法：

```shell
mysql -h 127.0.0.1 -p 3306 -u root -p # 按下回车后输入密码
# 等价于
mysql -u root -p
```

```note
我的数据库 root 的密码为 `root`，记在这里，以免忘记。
```




