在很多开源项目中都有netty的影子，比如阿里系的rocketmq和dubbo,大数据领elasticsearch,hbase,hadoop都是使用了netty作为底层传输的。到底netty是个啥东东呢，其实 Netty是一个NIO client-server(客户端服务器)框架，使用Netty可以快速开发网络应用，例如服务器和客户端协议。Netty是业界最流行的NIO框架之一，它的健壮性、功能、性能、可定制性和可扩展性在同类框架中都是首屈一指的，它已经得到成百上千的商用项目验证，例如Hadoop的RPC框架（Remote Procedure Call Protocol ，远程过程调用协议，它是一种通过网络从远程计算机程序上请求服务，而不需要了解底层网络技术的协议）avro使用Netty作为底层通信框架。很多其它业界主流的RPC框架，也使用Netty来构建高性能的异步通信能力。通过对Netty的分析，我们将它的优点总结如下：

- **API使用简单，开发门槛低；**

- **功能强大，预置了多种编解码功能，支持多种主流协议；**

- **定制能力强，可以通过ChannelHandler对通信框架进行灵活的扩展；**

- **性能高，通过与其它业界主流的NIO框架对比，Netty的综合性能最优**；

- **成熟、稳定，Netty修复了已经发现的所有JDK NIO BUG，业务开发人员不需要再为NIO的BUG而烦恼；**

- **社区活跃，版本迭代周期短，发现的BUG可以被及时修复，同时，更多的新功能会被加入；**

- **经历了大规模的商业应用考验，质量已经得到验证。在互联网、大数据、网络游戏、企业应用、电信软件等众多行业得到成功商用，证明了它可以完全满足不同行业的商业应用。**

- **说到netty不得不说IO,现在通信方式无非就是三种通信模式，BIO,NIO,AIO。**

  

  **BIO**： 同步并阻塞，服务器实现模式为一个连接一个线程，即客户端有连接请求时服务器端就需要启动一个线程进行处理（一客户端一线程）。该模型最大的问题就是缺乏弹性伸缩能力，当客户端并发访问量增加后，服务端的线程数与客户端并发访问数呈1:1的关系，系统性能将急剧下降，随着并发访问量的继续增加，系统会发生线程堆栈溢出、创建新线程失败等问题，并最终导致宕机或僵死。

  **NIO**：同步非阻塞，服务器实现模式为一个请求一个线程，客户端发送的连接请求都会注册到多路复用器上，多路复用器轮询到连接有I/O请求时才启动一个线程进行处理。 对于NIO，有两点需要强调的： （1）关于概念有两种理解，New I/O（相对于之前的I/O库是新增的）和Non-block I/O（非阻塞的）。由于NIO的目标就是让java支持非阻塞I/O，所有更多人喜欢用Non-block I/O。 （2）很多人喜欢将NIO称为异步非阻塞I/O，但是，如果按照严格的NUIX网络编程模型和JDK的实现进行区分，实际上它只是非阻塞I/O，不能称之为异步非阻塞I/O。但由于NIO库支持非阻塞读和写，相对于之前的同步阻塞读和写，它是异步的，因此很多人习惯称NIO为异步非阻塞I/O。

  **AIO**：JDK1.7升级了NIO库，升级后的NIO库被称为NIO2.0，正式引入了异步通道的概念。NIO2.0的异步套接字通道是真正的异步非阻塞I/O，此即AIO。其服务器实现模式为一个有效请求一个线程，客户端的I/O请求都是由OS先完成了再通知服务器应用去启动线程进行处理。

NIO 就是New IO , NIO和IO有相同的作用和目的，但实现方式不同，NIO主要用到的是块，所以NIO的效率要比IO高很多。NIO主要是通过buffer和channel传递数据，而原始的io 主要是通过字节流输入输出数据。

Netty就是采用了NIO模型，说了这么多，还是先来个栗子看看吧：

首先构建服务端，SslContext主要是来构建安全套接字，不过这个不是重点， 

EventLoopGroup bossGroup = new NioEventLoopGroup(1)开始构建boss线程组， ServerBootstrap负责初始化netty服务器，并且开始监听端口的socket请求。ServerBootstrap用一个ServerSocketChannelFactory 来实例化。ServerSocketChannelFactory 有两种选择，一种是NioServerSocketChannelFactory，一种是OioServerSocketChannelFactory。 前者使用NIO，后则使用普通的阻塞式IO。它们都需要两个线程池实例作为参数来初始化，一个是boss线程池，一个是worker线程池。



```
public final class EchoServer {
    static final boolean SSL = System.getProperty("ssl") != null;
    static final int PORT = Integer.parseInt(System.getProperty("port", "8007"));
    public static void main(String[] args) throws Exception {
        // Configure SSL.构建安全套接字
        final SslContext sslCtx;
        if (SSL) {
            SelfSignedCertificate ssc = new SelfSignedCertificate();
            sslCtx = SslContextBuilder.forServer(ssc.certificate(), ssc.privateKey()).build();
        } else {
            sslCtx = null;
        }
        // Configure the server.
        EventLoopGroup bossGroup = new NioEventLoopGroup(1);
        EventLoopGroup workerGroup = new NioEventLoopGroup();
        try {
            ServerBootstrap b = new ServerBootstrap();
            b.group(bossGroup, workerGroup)
             .channel(NioServerSocketChannel.class)
             .option(ChannelOption.SO_BACKLOG, 100)
             .handler(new LoggingHandler(LogLevel.INFO))
             .childHandler(new ChannelInitializer<SocketChannel>() {
                 [@Override](https://my.oschina.net/u/1162528)
                 public void initChannel(SocketChannel ch) throws Exception {
                     ChannelPipeline p = ch.pipeline();
                     if (sslCtx != null) {
                         p.addLast(sslCtx.newHandler(ch.alloc()));
                     }
                     p.addLast(new EchoServerHandler());
                 }
             });
           // Start the server.
        ChannelFuture f = b.bind(PORT).sync();
        // Wait until the server socket is closed.
        f.channel().closeFuture().sync();
    } finally {
        // Shut down all event loops to terminate all threads.
        bossGroup.shutdownGracefully();
        workerGroup.shutdownGracefully();
    }
}
```





ServerBootstrap.bind(int)负责绑定端口，当这个方法执行后，ServerBootstrap就可以接受指定端口上的socket连接了。一个ServerBootstrap可以绑定多个端口。也就是说ServerBootstrap监听的一个端口对应一个boss线程，它们一一对应，在boss线程接收了socket连接请求后，会产生一个channel（一个打开的socket对应一个打开的channel），并把这个channel交给ServerBootstrap初始化时指定的ServerSocketChannelFactory来处理，boss线程则继续处理socket的请求。ServerSocketChannelFactory则会从worker线程池中找出一个worker线程来继续处理这个请求。如果是OioServerSocketChannelFactory的话，那个这个channel上所有的socket消息，从开始到channel（socket）关闭，都只由这个特定的worker来处理，也就是说一个打开的socket对应一个指定的worker线程，这个worker线程在socket没有关闭的情况下，也只能为这个socket处理消息，无法服务其他socket。

如果是NioServerSocketChannelFactory的话则不然，每个worker可以服务不同的socket或者说channel，worker线程和channel不再有一一对应的关系。 显然，NioServerSocketChannelFactory只需要少量活动的worker线程及能很好的处理众多的channel，而OioServerSocketChannelFactory则需要与打开channel等量的worker线程来服务。

线 程是一种资源，所以当netty服务器需要处理长连接的时候，最好选择NioServerSocketChannelFactory，这样可以避免创建大 量的worker线程。在用作http服务器的时候，也最好选择NioServerSocketChannelFactory，因为现代浏览器都会使用 http keepalive功能（可以让浏览器的不同http请求共享一个信道），这也是一种长连接。

worker线程池中的线程生命周期是怎么样的？当某个channel有消息到达或者有消息需要写入socket的时候，worker线程就会从线程池中取出一个。在worker线程中，消息会经过设定好 的ChannelPipeline处理。ChannelPipeline就是一堆有顺序的filter，它分为两部分：UpstreamHandler和 DownStreamHandler， 客户端送入的消息会首先由许多UpstreamHandler依次处理，处理得到的数据送入应用的业务逻辑handler，对 于Nio当messageReceived()方法执行后，如果没有产生异常，worker线程就执行完毕了，它会被线程池回收。业务逻辑hanlder 会通过一些方法，把返回的数据交给指定好顺序的DownStreamHandler处理，处理后的数据如果需要，会被写入channel，进而通过绑定的 socket发送给客户端。这个过程是由另外一个线程池中的worker线程来完成的。 对于Oio来说，从始到终，都是由一个指定的worker来处理。

减少worker线程的处理占用时间

worker 线程是由netty内部管理，统一调配的一种资源，所以最好应该尽快的把让worker线程执行完毕，返回给线程池回收利用。worker线程的大部分时 间消耗在在ChannelPipeline的各种handler中，而在这些handler中，一般是负责应用程序业务逻辑掺入的那个handler最占 时间，它通常是排在最后的UpstreamHandler。所以通过把这部分处理内容交给另外一个线程来处理，可以有效的减少worker线程的周期循环 时间。messageReceived()方法中开启一个新的线程来处理业务逻辑，当然也可以使用利用netty框架自带的ExecutionHandler，这个以后在说。。

下面要说明下载本栗子中用于进行业务逻辑处理的EchoServerHandler，实际的开发中更可以拥有多个handler做消息的处理，下面我们来看下这个handler都做了那些工作。 EchoServerHandler主要是继承ChannelInboundHandlerAdapter，并且复写其中的某几个方法就可以实现自己定义的逻辑，例如我们可以复写channelRead方法可以获取客户端传过来的消息。





```
public class EchoServerHandler extends ChannelInboundHandlerAdapter {
    [@Override]
    public void channelRead(ChannelHandlerContext ctx, Object msg) {
        try {
            ByteBuf buf = (ByteBuf)msg;
            byte[] req = new byte[buf.readableBytes()];
            buf.readBytes(req);
            String response = new String(req,"utf-8");
            System.out.println("收到客户端服务器的请求消息"+response);
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }
        ctx.writeAndFlush(Unpooled.copiedBuffer("服务端返回的消息hello".getBytes()))
                .addListener(ChannelFutureListener.CLOSE);
    }
    

    [@Override]
    public void channelReadComplete(ChannelHandlerContext ctx) {
        ctx.flush();
    }
    [@Override](https://my.oschina.net/u/1162528)
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) {
        // Close the connection when an exception is raised.
        cause.printStackTrace();
        ctx.close();
    }

 } 
```



下面来看看客户端是如何编写的，首先构建客户端线程组，只需要构建一个线程组即可，并且绑定服务器主机的ip和port，并且向pipeline中绑定自己的handler这个handler主要是客户端中需要自定义的业务处理逻辑。



```
 public final class EchoClient {
    static final boolean SSL = System.getProperty("ssl") != null;
    static final String HOST = System.getProperty("host", "127.0.0.1");
    static final int PORT = Integer.parseInt(System.getProperty("port", "8007"));
    static final int SIZE = Integer.parseInt(System.getProperty("size", "256"));
    public static void main(String[] args) throws Exception {
        // Configure SSL.git
        final SslContext sslCtx;
        if (SSL) {
            sslCtx = SslContextBuilder.forClient()
                .trustManager(InsecureTrustManagerFactory.INSTANCE).build();
        } else {
            sslCtx = null;
        }
        // Configure the client.
        EventLoopGroup group = new NioEventLoopGroup();
        try {
            Bootstrap b = new Bootstrap();
            b.group(group)
             .channel(NioSocketChannel.class)
             .option(ChannelOption.TCP_NODELAY, true)
             .handler(new ChannelInitializer<SocketChannel>() {
                 [@Override](https://my.oschina.net/u/1162528)
                 public void initChannel(SocketChannel ch) throws Exception {
                     ChannelPipeline p = ch.pipeline();
                     if (sslCtx != null) {
                         p.addLast(sslCtx.newHandler(ch.alloc(), HOST, PORT));
                     }
                     //p.addLast(new LoggingHandler(LogLevel.INFO));
                     p.addLast(new EchoClientHandler());
                 }
             });
            // Start the client.
        ChannelFuture f = b.connect(HOST, PORT).sync();
        // Wait until the connection is closed.
        f.channel().closeFuture().sync();
    } finally {
        // Shut down the event loop to terminate all threads.
        group.shutdownGracefully();
    }
}       
```



下面看看handler的处理逻辑，只是在连接服务器的时候向服务器注册完毕时候及channelActive发送条消息，并且得到服务器返回消息的时候调用channelRead方法，得到消息并且将消息打印。





    public class EchoClientHandler extends ChannelInboundHandlerAdapter {
       private final ByteBuf firstMessage;
    
        /**
         * Creates a client-side handler.
         */
        public EchoClientHandler() {
            firstMessage = Unpooled.buffer(EchoClient.SIZE);
            for (int i = 0; i < firstMessage.capacity(); i ++) {
                firstMessage.writeByte((byte) i);
            }
        }
        @Override
        public void channelActive(ChannelHandlerContext ctx) {
            ctx.writeAndFlush(firstMessage);
        }
    
        @Override
        public void channelRead(ChannelHandlerContext ctx, Object msg) {
            try {
                ByteBuf buf = (ByteBuf)msg;
                byte[] req = new byte[buf.readableBytes()];
                buf.readBytes(req);
                String response = new String(req,"utf-8");
                System.out.println("收到服务端返回的消息"+response);
            } catch (UnsupportedEncodingException e) {
                e.printStackTrace();
            }
    
        }
    
        @Override
        public void channelReadComplete(ChannelHandlerContext ctx) {
           ctx.flush();
        }
    
        @Override
        public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) {
            // Close the connection when an exception is raised.
            cause.printStackTrace();
            ctx.close();
        }
      }  




总结一下Netty的核心包含以下几个部分:

- channel：通信框架的核心抽象

- ```
  bootstrap：应用启动入口
  ```

- ```
  buffer：数据交换的载体
  ```

- ```
  handler：协议与事件的处理工具
  ```

channel下包含的核心概念有：

![](D:\devPro\javaCode\images\通信\1089449-afd9e14197e1ef11.png)

```
Channel，表示一个连接，可以理解为每一个请求，就是一个Channel。
ChannelHandler，核心处理业务就在这里，用于处理业务请求。
ChannelHandlerContext，用于传输业务数据。
ChannelPipeline，用于保存处理过程需要用到的ChannelHandler和ChannelHandlerContext。

```



ByteBuf

ByteBuf是一个存储字节的容器，最大特点就是**使用方便**，它既有自己的读索引和写索引

详细了解可以这篇文章  [Netty 分享之 ByteBuf 实战](https://www.jianshu.com/p/8e407689c15a)

