---
title: Java网络编程
author: 熊滔
commentid: javase:network-programming
---

网络编程主要是客户端与服务器之间的交互，与客户端有关的类是Socket，与服务器有关的类是ServerSocket。客户端与服务器之间的通信主要是通过字节流实现的，客户端Socket含有方法

- getInputStream()
- getOutputStream()

来获得流，而服务器通过accept()监听请求的客户端，该方法返回一个Socket类对象，这个对象就是访问的客户端，服务器通过这个Socket获得流，通过该流与客户端通信。

## 构造方法

客户端Socket类的构造方法为

- Socket(String host, int port)
  - 第一个参数为域名地址或者IP地址
  - 第二个参数为端口号

服务器端ServerSocket的构造方法为

- ServerSocket(int port)
  - 这个参数为端口号

## 客户端与服务器之间的简单通信

下面写一个简单的示例来进行客户端与服务器的通信。

客户端代码

```java
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.Socket;

public class Client {
    public static void main(String[] args) throws IOException {
        //创建客户端对象socket 服务器IP地址为127.0.0.1，代表本机 端口号为8888
        Socket socket = new Socket("127.0.0.1",8888);
        //获得输出流以便向服务器发送信息
        OutputStream os = socket.getOutputStream();
        //向服务器发送信息
        os.write("这里是客户端".getBytes());
        
        //获得输入流，以接收服务器返回的信息
        InputStream is = socket.getInputStream();
        //读取服务器返回的信息并打印
        byte[] bytes = new byte[1024];
        int len = is.read(bytes);
        System.out.println(new String(bytes,0, len));
        //释放资源
        socket.close();
    }
}
```

服务器端代码

```java
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.ServerSocket;
import java.net.Socket;

public class Server {
    public static void main(String[] args) throws IOException {
        //创建一个服务器对象
        ServerSocket serverSocket = new ServerSocket(8888);
        //通过accept()方法获得客户端对象
        Socket socket = serverSocket.accept();
        //通过客户端对象获得输入流，已读取客户端发送的消息
        InputStream is = socket.getInputStream();
        //读取客户端发送的消息并打印
        byte[] bytes = new byte[1024];
        int len = is.read(bytes);
        System.out.println(new String(bytes, 0, len));
        
        //通过客户端对象获得输出流，以便发送消息给客户端
        OutputStream os = socket.getOutputStream();
        os.write("收到了，谢谢".getBytes());
        
        //释放资源
        socket.close();
        serverSocket.close();
    }

}
```

先运行服务器端的程序，然后运行客户端的程序，服务器的输出为

```java
这里是客户端
```

客户端的输出为

```java
收到了，谢谢
```

## 文件上传

将本地的文件传输到服务器，其实原理就是文件的复制，之前我们写过，不过现在是使用网络流来实现，下面是代码，说明都在代码的注释中。

客户端代码

```java
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.Socket;

public class ClientUpload  {
    public static void main(String[]args) throws IOException {
        //创建输入流读取要上传的文件
        InputStream fis = new FileInputStream("upload.pdf");
        //创建客户端对象
        Socket client = new Socket("127.0.0.1", 8888);
        
        //读取文件，并将文件通过socket的输出流传给服务器
        byte[] bytes = new byte[1024];
        int len = 0;
        OutputStream os = client.getOutputStream();
        while ((len = fis.read(bytes)) != -1) {
            os.write(bytes, 0, len);
        }
        //由于read不会将最后读取到-1发送，那么服务器就得不到-1，就不会终止
        //就会进入死循环，所以下面的代码是写入终止符，你可以把下面这行代码去掉，看看会发生什么
        client.shutdownOutput();
        
        //获得输入流以便获得服务器的返回信息
        InputStream is = client.getInputStream();
        while ((len = is.read(bytes)) != -1) {
            System.out.println(new String(bytes,0,len));
        }
        
        //释放资源
        fis.close();
        client.close();
    }
}
```

服务器端代码

```java
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.ServerSocket;
import java.net.Socket;

public class ServerUpload {
    public static void main(String[] args) throws IOException {
        //获得输出流，将上传的文件写到服务器的硬盘中
        OutputStream fos = new FileOutputStream("uploadServer.pdf");
        
        //创建服务器对象
        ServerSocket server = new ServerSocket(8888);
        Socket socket = server.accept();
        
        //获得输入流读取客户端发送的数据
        InputStream is = socket.getInputStream();
        byte[] bytes = new byte[1024];
        int len = 0;
        //读到-1才会终止，所以客户端最后要发一个终止符-1，否则会不断循环
        while ((len = is.read(bytes)) != -1) {
            fos.write(bytes);
        }
        
        //向客户端返回数据"已经上传完成"
        OutputStream os = socket.getOutputStream();
        os.write("已经上传完成".getBytes());

        socket.close();
        fos.close();
        server.close();
    }
}
```





<Disqus />