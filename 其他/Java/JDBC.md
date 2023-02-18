---
title: JDBC
author: 熊滔
commentid: javase:jdbc
---

## 初识JDBC

如果我们要使用`Java`来操作数据库，由于数据库的种类繁多，并且`sql`语句并不完全相同，总而言之不同的数据库千差万别，这是不是意味着我们要对不同的数据库写不同的程序，这样的话对程序员的要求有点高，并且学习的成本也很大，所以我们要定义一套规范，要求对所有的数据库程序员只要写相同的程序就可以了。就相当于插座，我们定义好插座的标准，那么所有的商家必须按我的标准来，否则就不能使用插座。同理，在Java中定义好数据库的接口，定义接口中的方法有什么作用，而具体的实现细节则有数据库的厂商进行编写，程序员只要调用接口里面的方法就可以了。这里给出一个摘至维基百科的定义

```info
Java Database Connectivity (JDBC) is an application programming interface (API) for the programming language Java, which defines how a client may access a database. It is a Java-based data access technology used for Java database connectivity.
```

所以使用`JDBC`的好处便显而易见

- 程序员如果要开发访问数据库的程序，只需要会调用`JDBC`接口中的方法即可，不用关注类是如何实现的
- 使用同一套`Java`代码，进行少量的修改就可以访问其他`JDBC`支持的数据库 

我们使用`Java`操作数据库，会使用到数据库的驱动，由各大数据库厂商提供，需要额外去下载，里面是对`JDBC`接口实现的类，导入驱动jar包的具体的步骤如下

- 在项目中新建一个`libs`文件夹
- 将`jar`包复制到这个文件夹中
- 选中这个`jar`包，右击找到`"Add as Library"`

上面是导入`jar`包的通用步骤，后面会经常导入`jar`包，所以需要熟记。

### 第一个JDBC程序

使用`Java`操作数据库一般包括下面几步

- 注册驱动
- 获得连接数据库的对象
- 获得执行`sql`语句的对象
- 执行`sql`语句
- 释放资源(断开连接)

程序如下(具体类后面解释)

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.Statement;

public class Demo01 {
    public static void main(String[] args) throws Exception{
        //注册驱动
        Class.forName("com.mysql.jdbc.Driver");
        
        //获取连接
        Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/db1", "root", "root");
        
        //创建sql语句
        String sql = "update account set balance = 2000 where id = 1";

        //创建执行sql语句的对象
        Statement statement = conn.createStatement();
        //执行sql语句
        statement.executeUpdate(sql);

        //释放资源
        statement.close();
        conn.close();
    }
}
```

### 核心类

下面就详细介绍`JDBC`的核心类(接口)以及它们的功能

- `DriverManager`：驱动管理对象，功能如下
  - 注册驱动：告诉程序该使用哪一个数据库驱动`jar`包
    - `mysql5`之后可以省略注册驱动的步骤，即不需要写`Class.forName()`
  - 获取数据库连接
    - `static Connection getConnection(String url, String user, String password)`
      - `url`：连接的路径
        - 格式：`jdbc:mysql://ip地址(域名):端口号/数据库名称?参数=参数值 `
          - 例子：`jdbc:mysql://localhost:3306/db1`
          - 如果`ip`地址为本机地址且端口号为`3306`，则上面可省略`ip`地址和端口号，简写为：`jdbc:mysql:///db1`
        - 如果出现乱码问题，可以指定参数`?characterEncoding=utf8`
      - `user`：用户名
      - `password`：密码
- `Connection`：数据库连接对象，相当于是在Java程序与数据库之间建立了一条通道，功能如下
  - 获取执行`sql`的对象
    - `Statement createStatement()`
    - `PreparedStatement prepareStatement(String sql)`
  - 管理事务
    - 开启事务：`setAutoCommit(boolean autoCommit)`，传入`false`即开启事务
    - 提交事务：`commit()`
    - 回滚事务：`rollback()`
- `Statement`：执行`sql`的对象，含有如下方法
  - `boolean execute(String sql)`：可以执行任意的`sql`语句，不常用，了解即可
  - `int executeUpdate(String sql)`
    - 执行`DML(insert、update、delete)`语句、`DDL(create，alter、drop)`语句
    - 返回值：影响的行数，可以通过这个影响的行数判断`DML`语句是否执行成功 返回值`>0`的则执行成功，反之，则失败。
  - `ResultSet executeQuery(String sql)`：执行`DQL(select)`语句
- `ResultSet`：结果集对象，封装查询结果，数据库通过通道返回的结果
  - `boolean next()`
    - 判断游标所指向的行是否为空
  - `getXxx()`
    - `getXxx(int columnIndex)`
      - 根据列数来获得数据
    - `getXxx(int columnLabel)`
      - 根据列名称获得数据

这里给出一个使用`ResultSet`的例子，比如我们要去查询`account`表

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;

public class Demo02 {
    public static void main(String[] args) throws Exception {
        Class.forName("com.mysql.jdbc.Driver");
        Connection conn = DriverManager.getConnection("jdbc:mysql:///db1", "user", "root");
        Statement statement = conn.createStatement();
        
        String sql = "select * from account";
        ResultSet resultSet = statement.executeQuery(sql);
        
        //将查询到的数据打印出来
        while (resultSet.next()) {
            //根据列名查询数据
            int id = resultSet.getInt("id");
            String name = resultSet.getString("name");
            double balance = resultSet.getDouble("balance");

            System.out.println(id + "---" + name + "---" + balance);
        }
        
        resultSet.close();
        statement.close();
        conn.close();
    }
}
```

### JDBC工具类

其实上面的代码并不是十分的规范，因为资源的释放可能不能释放成功，因为前面发生异常就可以导致后面的程序执行不到，这样资源无法释放，就会导致内存越用越少(内存泄漏)，所以我们应当将资源释放的代码写到`finally`代码块中，如下

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;

public class Demo01 {
    public static void main(String[] args) {
        //定义在try代码块外是为了在finally中能够访问
        Connection conn = null;
        Statement statement = null;
        try {
            conn = DriverManager.getConnection("jdbc:mysql:///db1", "root", "root");
            statement = conn.createStatement();
            String sql = "update account set balance = 2000 where id = 1";
            statement.executeUpdate(sql);
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            if (statement != null) {
                try {
                    statement.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            
            if (conn != null) {
                try {
                    conn.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

这时我们发现代码十分的冗长(特别是资源释放的部分)，并且每次写代码都会重复这些，再者程序所连接的数据库是写死的，如果需要改变的话还需要改动程序，我们可以将这些信息写在一个`jdbc.properties`的配置文件中，如果有改动直接改动配置文件即可

```properties
url=jdbc:mysql:///db1
user=root
password=root
driver=com.mysql.jdbc.Driver
```

将上面的这些操作抽离出来，写一个`JDBC`的工具类，在工具类中读取配置文件，以及抽离出一些方法，这样不用每次都写这么长的代码

```java
import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.IOException;
import java.net.URL;
import java.sql.*;
import java.util.Properties;

public class JDBCUtils {
    private static String url;
    private static String user;
    private static String password;
    private static String driver;
	
    // 读取jdbc.properties中的内容 获得url user password driver等信息
    // 因为只需要读取一次，所以写在static代码块中
    static {
        try {
            Properties properties = new Properties();
            ClassLoader classLoader = JDBCUtils.class.getClassLoader();
            URL res = classLoader.getResource("jdbc.properties");
            String path = res.getPath();
            properties.load(new FileReader(path));

            url = properties.getProperty("url");
            user = properties.getProperty("user");
            password = properties.getProperty("password");
            driver = properties.getProperty("driver");

            Class.forName(driver);
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
	
    // 返回连接对象
    public static Connection getConnection() throws SQLException{
        return DriverManager.getConnection(url, user, password);
    }
	
    // 释放资源
    public static void close(Connection conn, Statement stam) {
        if (stam != null) {
            try {
                stam.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        if (conn != null) {
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
	
    // 释放资源
    public static void close(Connection conn, Statement stam, ResultSet resultSet) {
        if (resultSet != null) {
            try {
                resultSet.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        // 复用上面的代码
        close(conn, stam);
    }
}

```

### 用户登录练习

我们来写一个方法来判断用户输入的用户名和密码是否正确，思路就是根据用户输入的用户名和密码去数据库中查询，如果返回的`ResultSet`有内容，那么就确定输入正确，否则失败。假设有下面这么一个表(`user`)

```sql
+------+----------+----------+
| id   | user     | password |
+------+----------+----------+
|    1 | zhangsan | 123      |
|    2 | lisi     | 345      |
+------+----------+----------+
```

下面是检查用户名和密码的方法

```java
public static boolean login(String user, String password) {
    Connection conn = null;
    Statement statement = null;
    ResultSet resultSet = null;
    try {
        conn = JDBCUtils.getConnection();
        statement = conn.createStatement();
        
        //拼接user和password未一个sql语句
        String sql = "select * from user where user = '" + user + "' and password = '" +  password + "'";
        resultSet = statement.executeQuery(sql);
        
        //如果返回有数据则返回true 否则返回false
        return resultSet.next();
    } catch (SQLException e) {
        e.printStackTrace();
    } finally {
        JDBCUtils.close(conn, statement, resultSet);
    }
    return false;
}
```

下面在`main`方法中写测试代码

```java
public static void main(String[] args) {
    //用户输入用户名和密码
    Scanner scanner = new Scanner(System.in);
    System.out.println("请输入用户名：");
    String user = scanner.nextLine();
    System.out.println("请输入密码：");
    String password = scanner.nextLine();
    
    boolean flag = login(user, password);
    if (flag) {
        System.out.println("登录成功");
    } else {
        System.out.println("用户名或密码错误");
    }
}
```

我们来试验一下

```java
请输入用户名：
lisi
请输入密码：
234
用户名或密码错误
```

```java
请输入用户名：
zhangsan
请输入密码：
123
登录成功
```

### PreparedStatement

`PreparedStatement`的作用同`Statement`，也是用来执行`sql`命令的，但是已经有了`Statement`，为什么还需要`PreparedStatement`呢? 那就要知道`Statement`存在什么问题，我们来看上面的用户登录程序，如果我们登录时这么写

```java
请输入用户名：
daad
请输入密码：
a' or 'a' = 'a
登录成功
```

得到的结果居然登录成功了，因为`sql`语句被拼接成了

```sql
select * from user where user = 'daad' and password = 'a' or 'a' = 'a';
```

最后的`or ‘a’ = ‘a’`得到的永远是true，所以总是可以得到返回结果，所以自然会显示登录成功，这种情况叫做`SQL`注入。`PreparedStatement`正是为了解决这一个问题的，上面出现问题是因为我们的`sql`语句是拼接而成的，所以才会出现问题，`PreparedStatement`采取的办法是首先使用`?`占据位置(占位符)，然后对`?`所占据的位置进行赋值，赋值的方法为

- `setXxx()`：接收两个参数
  - 第一个参数表示为第几个占位符赋值，从`1`开始
  - 第二个参数是值

所以我们将上面的登录代码修改如下

```java
public static boolean login(String user, String password) {
    Connection conn = null;
    PreparedStatement preparedStatement = null;
    ResultSet resultSet = null;
    try {
        conn = JDBCUtils.getConnection();
        //使用?作为占位符
        String sql = "select * from user where user = ? and password = ?";
        //创建PreparedStatement对象
        preparedStatement = conn.prepareStatement(sql);
        //为占位符赋值
        preparedStatement.setString(1, user);
        preparedStatement.setString(2, password);
        //不需要传入sql
        resultSet = preparedStatement.executeQuery();
        //如果返回有数据则返回true 否则返回false
        return resultSet.next();
    } catch (SQLException e) {
        e.printStackTrace();
    } finally {
        JDBCUtils.close(conn, preparedStatement, resultSet);
    }
    return false;
}
```

这时还是使用与上面相同的操作

```java
请输入用户名：
dadd
请输入密码：
a or 'a' = 'a'
用户名或密码错误
```

在实际中我们一般使用`PreparedStatement`，因为它不仅可以防止`SQL`注入，并且效率更高。

## JDBC事务

事务就是一组`sql`操作，这一组`sql`操作要么同时成功，要么同时失败。比如转账，一方钱的支出与另一方钱的到账必须同时成功或者同时失败，没有这里钱转出去了，另一方却没有到账的情况。有关事务一般包含下面三个操作

- 开启事务
  - `setAutoCommit(false)`
  - 开启事务后，直至提交，执行的`sql`语句不会在数据库中生效，而是会写在日志中，只有提交后才会将根据日志修改数据库中的数据
  - 默认是自动提交的，即每次执行一次`sql`命令都会更改数据库中的内容，我们将自动提交关闭就相当于是开启了事务
- 提交事务
  - `commit()`
- 回滚事务
  - `rollback()`
  - 事务执行失败，这时我们就要回到执行事务前的状态，这时会把日志中的内容清空
  - 一般在`catch`代码块中进行回滚操作

假设有下面这么一个表

```sql
+----+------+---------+
| id | NAME | balance |
+----+------+---------+
|  1 | 张三 |    2000 |
|  2 | 李四 |    1000 |
+----+------+---------+
```

现在张三要给李四转500块钱

```java
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class Demo04 {
    public static void main(String[] args) {
        //转账的金额
        double money = 500;

        Connection connection = null;
        PreparedStatement preparedStatement1 = null;
        PreparedStatement preparedStatement2 = null;

        try {
            connection = JDBCUtils.getConnection();
            String sql1 = "update account set balance = balance - ? where id = ?";
            String sql2 = "update account set balance = balance + ? where id = ?";

            preparedStatement1 = connection.prepareStatement(sql1);
            preparedStatement1.setDouble(1, money);
            preparedStatement1.setInt(2, 1);

            preparedStatement2 = connection.prepareStatement(sql2);
            preparedStatement2.setDouble(1, money);
            preparedStatement2.setInt(2, 2);

            preparedStatement1.executeUpdate();
            preparedStatement2.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            JDBCUtils.close(connection, preparedStatement1);
            //因为connection已经关闭了，所以传入null
            JDBCUtils.close(null, preparedStatement2);
        }
    }
}
```

执行上面的程序然后再次查表得

```java
+----+------+---------+
| id | NAME | balance |
+----+------+---------+
|  1 | 张三 |    1500 |
|  2 | 李四 |    1500 |
+----+------+---------+
```

但是如果我们在执行张三支出500块后手动添加一个异常，这时李四则不会收到500块

```java
preparedStatement1.executeUpdate();
int i = 3 / 0;
preparedStatement2.executeUpdate();
```

```sql
+----+------+---------+
| id | NAME | balance |
+----+------+---------+
|  1 | 张三 |    1000 |
|  2 | 李四 |    1500 |
+----+------+---------+
```

张三的钱减少了500块，但是李四却没有收到。这就是问题，所以我们要开启事务，并且在`catch`代码块中进行回滚

```java
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class Demo04 {
    public static void main(String[] args) {
        double money = 500;

        Connection connection = null;
        PreparedStatement preparedStatement1 = null;
        PreparedStatement preparedStatement2 = null;

        try {
            connection = JDBCUtils.getConnection();
            
            //开启事务
            connection.setAutoCommit(false);
            
            String sql1 = "update account set balance = balance - ? where id = ?";
            String sql2 = "update account set balance = balance + ? where id = ?";

            preparedStatement1 = connection.prepareStatement(sql1);
            preparedStatement1.setDouble(1, money);
            preparedStatement1.setInt(2, 1);

            preparedStatement2 = connection.prepareStatement(sql2);
            preparedStatement2.setDouble(1, money);
            preparedStatement2.setInt(2, 2);

            preparedStatement1.executeUpdate();
            int i = 3 / 0;
            preparedStatement2.executeUpdate();

            //提交事务
            connection.commit();
        } catch (Exception e) {
            try {
                if (connection != null) {
                    //回滚事务
                    connection.rollback();
                }
            } catch (SQLException e1) {
                e1.printStackTrace();
            }
            e.printStackTrace();
        } finally {
            JDBCUtils.close(connection, preparedStatement1);
            JDBCUtils.close(null, preparedStatement2);
        }
    }
}
```

这次再次执行

```sql
+----+------+---------+
| id | NAME | balance |
+----+------+---------+
|  1 | 张三 |    1000 |
|  2 | 李四 |    1500 |
+----+------+---------+
```

虽然还是发生了异常，但是张三和李四并没有发生上面一方转出一方没有到账的情况，而是转账失败。

## 数据库连接池

每次我们使用完连接对象后都会将该对象销毁，然后下次需要连接对象时又需要重新创建。如果考虑到有频繁的操作数据库的操作，每次创建销毁的开销是很大的，所以就有了数据库连接池，里面有很多的连接对象，当我们需要时就从里面拿，用完之后不是销毁，而是将对象归还给连接池，这样做能够有效的提升程序的性能。Java定义了一个`DataSource`接口，我们可以通过该接口的`getConnection()`方法获取一个连接，并且可以通过`close()`方法归还这个连接(前提是这个连接是从连接池中获得的)。`DataSource`接口的实现我们不需要关心，我们只要知道如何使用即可，我们学习如何使用两种数据库连接池技术

- `C3P0`
- `Druid`：阿里巴巴提供，目前最好的数据库连接池之一

### C3P0

`C3P0`的使用步骤

1. 导入`jar`包
   - `c3p0-0.9.5.2.jar`
   - `mchange-commons-java-0.2.12.jar`
2. 定义配置文件
   - 只能为`c3p0-config.xml`或`c3p0.properties`，配置文件需放在`src`目录下
3. 使用实现类`ComboPooledDataSource`获得连接池对象

下面给出配置文件`c3p0-config.xml`的内容

```xml
<c3p0-config>
  <!-- 使用默认的配置读取连接池对象 -->
  <default-config>
  	<!--  连接参数 -->
    <property name="driverClass">com.mysql.jdbc.Driver</property>
    <property name="jdbcUrl">jdbc:mysql:///db1</property>
    <property name="user">root</property>
    <property name="password">root</property>
    
    <!-- 连接池参数 -->
      <!--初始连接对象数-->
    <property name="initialPoolSize">5</property>
      <!--最大连接对象数-->
    <property name="maxPoolSize">10</property>
      <!--最大等待时间 3s-->
    <property name="checkoutTimeout">3000</property>
  </default-config>
</c3p0-config>
```

下面给出一个使用的示例

```java
import com.mchange.v2.c3p0.ComboPooledDataSource;

import javax.sql.DataSource;
import java.sql.Connection;
import java.sql.SQLException;

public class Demo05 {
    public static void main(String[] args) throws SQLException {
        //获得连接池对象
        DataSource ds = new ComboPooledDataSource();
        //获取连接
        Connection conn = ds.getConnection();
    }
}
```

### Druid

`Druid`的使用步骤

1. 导入`jar`包
   - `druid-1.0.9.jar`
2. 定义配置文件
   - `.properties`文件
   - 可以放置在任何的地方
3. 使用工厂方法`DruidDataSourceFactory.createDataSource()`获得连接池
   - 需要传入一个`Properties`对象(配置文件)

下面给出`druid.properties`的内容

```properties
driverClassName=com.mysql.jdbc.Driver
url=jdbc:mysql:///db1
username=root
password=root
initialSize=5
maxActive=10
maxWait=3000
```

想必上面的各参数的含义不必解释，那么就给出一个使用`Druid`的例子

```java
import com.alibaba.druid.pool.DruidDataSourceFactory;

import javax.sql.DataSource;
import java.io.InputStream;
import java.sql.Connection;
import java.util.Properties;

public class Demo06 {
    public static void main(String[] args) throws Exception {
        Properties pro = new Properties();
        //得到配置文件的输入流对象
        InputStream is = Demo06.class.getClassLoader().getResourceAsStream("druid.properties");
        pro.load(is);

        DataSource ds = DruidDataSourceFactory.createDataSource(pro);

        Connection conn = ds.getConnection();
    }
}
```

### 工具类

我们发现每次使用连接池时会有很多重复的操作，同上面我们应该写一个工具类`JDBCUtils`将一些操作抽离出来，`JDBCUtils`类如下

```java
import com.alibaba.druid.pool.DruidDataSourceFactory;

import javax.sql.DataSource;
import java.io.IOException;
import java.sql.*;
import java.util.Properties;

public class JDBCUtils {
    private static DataSource ds;

    //读取配置文件 初始化连接池对象
    static {
        Properties pro = new Properties();
        try {
            pro.load(JDBCUtils.class.getClassLoader().getResourceAsStream("druid.properties"));
            ds = DruidDataSourceFactory.createDataSource(pro);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
	
    //返回连接对象
    public static Connection getConnection() throws SQLException{
        return ds.getConnection();
    }
	
    //关闭资源
    public static void close(Connection conn, Statement stam) {
        if (stam != null) {
            try {
                stam.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        if (conn != null) {
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }

    public static void close(Connection conn, Statement stam, ResultSet resultSet) {
        if (resultSet != null) {
            try {
                resultSet.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
		
        //复用上面的代码
        close(conn, stam);
    }
    
    //返回连接池对象
    public static DataSource getDataSource() {
        return ds;
    }
}
```

## Spring JDBC

`Spring`对`JDBC`进行了简单的封装，提供了一个`JdbcTemplate`对象来简化`JDBC`的开发，使用步骤为

1. 导入`jar`包
   - `spring-beans-5.0.0.RELEASE.jar`
   - `spring-core-5.0.0.RELEASE.jar`
   - `spring-jdbc-5.0.0.RELEASE.jar`
   - `spring-tx-5.0.0.RELEASE.jar`
   - `commons-logging-1.2.jar`
2. 创建`JdbcTemplate`对象
   - `new JdbcTemplate(ds)`：`ds`为数据库连接池对象
3. 使用`JdbcTemplate`对象的方法对数据库进行操作
   - `update()`：执行`DML`语句
     - 第一个参数为`sql`语句
     - 第二个参数为可变参数，是占位符`?`所对应的值
   - `queryForMap()`：将结果封装为`Map`对象
     - 只能查询一行数据，将结果封装为`Map`对象
     - 将列名最为`Key`，将字段值作为`Value`
   - `queryForList()`：将结果封装为`List`对象
     - 查询多行数据，每一行数据封装为一个`Map`对象，这些`Map`对象会被添加到一个`List`集合中返回
   - `queryForObject()`：将结果封装为对象
     - 用以查询一些聚合函数
   - `query()`：将结果封装为`JavaBean`对象

下面就来做一个简单的演示，假设`db1`数据库中有一张表`account`其中内容如下

```sql
+----+------+---------+
| id | NAME | balance |
+----+------+---------+
|  1 | 张三 |    1000 |
|  2 | 李四 |    1000 |
+----+------+---------+
```

### update

现在我们使用`update`命令修改张三的`balance`为2000

```java
import org.junit.Test;
import org.springframework.jdbc.core.JdbcTemplate;

public class Demo07 {
    private JdbcTemplate template = new JdbcTemplate(JDBCUtils.getDataSource());
    
    //Junit单元测试 方法可以单独执行 不需要写在main方法中
    @Test
    public void testUpdate() {
        String sql = "update account set balance = 2000 where id = ?";
        template.update(sql, 1);
    }
}
```

我们再次查询`account`表

```sql
+----+------+---------+
| id | NAME | balance |
+----+------+---------+
|  1 | 张三 |    2000 |
|  2 | 李四 |    1000 |
+----+------+---------+
```

### queryForMap

查询张三所在行`(id = 1)`，得到一个`Map`对象，我们将其打印出来

```java
@Test
public void testQueryForMap() {
    String sql = "select * from account where id = ?";
    Map map = template.queryForMap(sql, 1);
    System.out.println(map);
}
```

结果为

```java
{id=1, NAME=张三, balance=2000.0}
```

### queryForList

如果我们需要查询多条数据怎么办，这个时候就使用`queryForList`，它会将每条数据封装为`Map`集合，然后将这些`Map`集合添加到`List`集合中

```java
@Test
public void testQueryForList() {
    String sql = "select * from account";
    List<Map<String, Object>> mapList = template.queryForList(sql);
    for (Map<String, Object> map: mapList) {
        System.out.println(map);
    }
}
```

得到的结果为

```java
{id=1, NAME=张三, balance=2000.0}
{id=2, NAME=李四, balance=1000.0}
```

### query

更多的时候我们希望将得到的结果封装为一个对象，现在我们新建一个类`Account`如下

```java
public class Account {
    private Integer id;
    private String name;
    private Double balance;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Double getBalance() {
        return balance;
    }

    public void setBalance(Double balance) {
        this.balance = balance;
    }

    @Override
    public String toString() {
        return "Account{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", balance=" + balance +
                '}';
    }
}
```

现在我们希望将获得结果封装为一个个`Account`对象，这时我们就需要使用`query()`方法了，`query`方法的第一个参数是`sql`语句，第二个参数是`RowMapper<>`的实现类，这个类我们可以自己实现，也可以使用`Spring`提供好的实现类，我们就使用`Spring`提供好的实现类`BeanPropertyRowMapper<>`，我们只要将`Account`的`class`属性传递进去即可

```java
@Test
public void testQuery() {
    String sql = "select * from account";
    List<Account> list = template.query(sql, new BeanPropertyRowMapper<Account>(Account.class));
    for (Account account : list) {
        System.out.println(account);
    }
}
```

结果为

```java
Account{id=1, name='张三', balance=2000.0}
Account{id=2, name='李四', balance=1000.0}
```

### queryForObject

现在我们来查询`account`表中右多少条数据，我们使用聚合函数`count()`，得到的结果是一个`long`类型的数字，我们使用`queryForObject()`查询，接收两个参数，第一个参数是`sql`命令，第二个参数是返回类型的`class`属性

```java
@Test
public void testQueryForObject() {
    String sql = "select count(id) from account";
    Long num = template.queryForObject(sql, Long.class);
    System.out.println(num);
}
```

结果为

```java
2
```



<Disqus />