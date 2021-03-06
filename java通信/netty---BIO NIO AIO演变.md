Netty是一个提供异步事件驱动的网络应用框架，用以快速开发高性能、高可靠的网络服务器和客户端程序。Netty简化了网络程序的开发，是很多框架和公司都在使用的技术。

Netty并非横空出世，它是在BIO，NIO，AIO演变中的产物，是一种NIO框架





## 一、BIO的理解

 网络编程的基本模型是Client/Server模型，也就是两个进程之间进行相互通信，其中服务端提供位置信息（绑定的IP地址和监听端口），客户端通过连接操作向服务端监听的

地址发送连接请求，通过三次握手建立连接，如果连接成功，双方就可以通过网络套接字(Socket)进行通信。在基于同步堵塞模式开发中，ServerSocket负责绑定Ip地址，启动监

听端口。Socket负责发起连接操作。连接成功之后，双方通过输入和输出流进行同步堵塞式通信。



![](http://javacode.blogwiki.top/1090617-20181118215610086-2008700581.png)



**BIO** 全称Block-IO 是一种阻塞同步的通信模式。我们常说的StockIO一般指的是BIO。是一个比较传统的通信方式，模式简单，使用方便。但并发处理能力低，通信耗时，依赖网速。

**BIO 设计原理**：

​    服务器通过一个Acceptor线程负责监听客户端请求和为每个客户端创建一个新的线程进行链路处理。典型的一请求一应答模式。若客户端数量增多，频繁地创建和销毁线程会

给服务器打开很大的压力。后改良为用线程池的方式代替新增线程，被称为伪异步IO。服务器提供IP地址和监听的端口，客户端通过TCP的三次握手与服务器连接，连接成功后，双

放才能通过套接字(Stock)通信。

小结：BIO模型中通过Socket和ServerSocket完成套接字通道的实现。阻塞，同步，建立连接耗时。

 

## 二、NIO的理解



  与Socket类和ServerSocket类相对应，NIO也提供了SocketChannel和ServerSocketChannel两种不同的套接字通道实现。这两种新增的通道都支持阻塞和非阻塞两种模

式。阻塞模式使用非常简单，但是性能和可靠性都不好，非阻塞模式则正好相反。幵发人员一般可以根据自己的需要来选择合适的模式，一般来说，低负载、低并发的应用程序可

以选择同步阻塞I/O以降低编程父杂度，但是对于高负载、高并发的网络络应用，需要使用NIO的非堵塞模式迸行幵发。

NIO 全称New IO，也叫Non-Block IO 是一种非阻塞同步的通信模式。



![](http://javacode.blogwiki.top/1090617-20181118215941745-323892415.png)



**NIO 设计原理**

   NIO 相对于BIO来说一大进步。客户端和服务器之间通过Channel通信。NIO可以在Channel进行读写操作。这些Channel都会被注册在Selector多路复用器上。Selector通过

一个线程不停的轮询这些Channel。找出已经准备就绪的Channel执行IO操作。NIO 通过一个线程轮询，实现千万个客户端的请求，这就是非阻塞NIO的特点。

NIO几个新概念

**1）缓冲区Buffer**

   它是NIO与BIO的一个重要区别。BIO是将数据直接写入或读取到Stream对象中。而NIO的数据操作都是在缓冲区中进行的。缓冲区实际上是一个数组。Buffer最常见的类型

是ByteBuffer，另外还有CharBuffer，ShortBuffer，IntBuffer，LongBuffer，FloatBuffer，DoubleBuffer。

**2）通道Channel**

​    和流不同，通道是双向的。NIO可以通过Channel进行数据的读，写和同时读写操作。通道与流之间不同之处是通道是双向的，流只能一个方向移动（一个流必须是

InputStream或者OutputStream的子类）。 通道分为两大类：一类是网络读写（SelectableChannel），一类是用于文件操作（FileChannel），我们使用的SocketChannel

和ServerSocketChannel都是SelectableChannel的子类。

**3）多路复用器Selector**

   NIO编程的基础。多路复用器提供选择已经就绪的任务的能力。就是Selector会不断地轮询注册在其上的通道（Channel），如果某个通道处于就绪状态，会被Selector轮询出

来，然后通过SelectionKey可以取得就绪的Channel集合，从而进行后续的IO操作。服务器端只要提供一个线程负责Selector的轮询，就可以接入成千上万个客户端，这就是

JDK NIO库的巨大进步。



## 三、AIO理解



  异步无非是通知系统做一件事情。然后忘掉它，自己做其他事情去了。很多时候系统做完某一件事情后需要一些后续的操作。

AIO 也叫NIO2.0 是一种非阻塞异步的通信模式。在NIO的基础上引入了新的异步通道的概念，并提供了异步文件通道和异步套接字通道的实现。



![](http://javacode.blogwiki.top/1090617-20181118221221582-1287720922.png)



  AIO 并没有采用NIO的多路复用器，而是使用异步通道的概念。其read，write方法的返回类型都是Future对象。而Future模型是异步的，其核心思想是：去主函数等待时间。

AIO模型中通过AsynchronousSocketChannel和AsynchronousServerSocketChannel完成套接字通道的实现。非阻塞，异步。





## 四、总结





#### **1 IO，NIO，AIO区别**

  IO 阻塞同步通信模式，客户端和服务器连接需要三次握手，使用简单，但吞吐量小。

  NIO 非阻塞同步通信模式，客户端与服务器通过Channel连接，采用多路复用器轮询注册的Channel。提高吞吐量和可靠性。

  AIO 非阻塞异步通信模式，NIO的升级版，采用异步通道实现异步通信，其read和write方法均是异步方法。

#### 2 Stock通信的伪代码实现流程

   服务器绑定端口：server = new ServerSocket(PORT)

   服务器阻塞监听：socket = server.accept()

   服务器开启线程：new Thread(Handle handle)

   服务器读写数据：BufferedReader PrintWriter

   客户端绑定IP和PORT：new Socket(IP_ADDRESS, PORT)

   客户端传输接收数据：BufferedReader PrintWriter

**3 什么是同步阻塞BIO，同步非阻塞NIO，异步非阻塞AIO**

   **同步阻塞IO** : 用户进程发起一个IO操作以后，必须等待IO操作的真正完成后，才能继续运行。

   **同步非阻塞IO**: 用户进程发起一个IO操作以后，可做其它事情，但用户进程需要经常询问IO操作是否完成，这样造成不必要的CPU资源浪费。

   **异步非阻塞IO**: 用户进程发起一个IO操作然后，立即返回，等IO操作真正的完成以后，应用程序会得到IO操作完成的通知。类比Future模式。

**4、小结**

   1）BIO模型中通过Socket和ServerSocket完成套接字通道实现。阻塞，同步，连接耗时。

  2） NIO模型中通过SocketChannel和ServerSocketChannel完成套接字通道实现。非阻塞/阻塞，同步，避免TCP建立连接使用三次握手带来的开销。

  3） AIO模型中通过AsynchronousSocketChannel和AsynchronousServerSocketChannel完成套接字通道实现。非阻塞，异步。



![](..\images\images\通信\1090617-20181118221832172-1747040798.png)





### 五、参考

[NIO基础详解(代码部分解释的也很好很好) ](https://www.jianshu.com/p/c84141e1a678)

[Java I/O模型从BIO到NIO和Reactor模式（图片解释更清晰）](http://www.jasongj.com/java/nio_reactor/#comments)

















