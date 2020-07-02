## **IO流基础介绍**

### IO的基本概述

- IO流概述及其前奏
  - A:IO流概述
    - IO流用来处理设备之间的数据传输，比如常见存储文件
    - 上传文件和下载文件
  - B:IO流前奏
    - 讲解IO流之前为什么先讲解异常和File类呢?
    - 因为File表示的是IO流将来要操作的文件，所以我们需要学习File类。
    - 而常见操作文件无非就是上传文件和下载文件，在这个操作的过程中可能出现问题，
    - 出现问题后，我们需要对对应的代码进行处理。所以我们需要学习异常。
- IO流整体学习知识体系
  - A:IO流概述
    - IO流用来处理设备之间的数据传输
    - Java对数据的操作是通过流的方式
    - Java用于操作流的对象都在IO包中
  - B:IO流分类
    - a:按照数据流向
      - 输入流 读入数据
      - 输出流 写出数据
    - b:按照数据类型
      - 字节流
      - 字符流
      - 什么情况下使用哪种流呢?
      - 如果数据所在的文件通过windows自带的记事本打开并能读懂里面的内容，就用字符流。其他用字节流。
      - 如果你什么都不知道，就用字节流
    - c:字节流和字符流
      - 字节流
        - 字节输入流 InputStream 读
        - 字节输出流 OutputStream 写
      - 字符流
        - 字符输入流 Reader 读
        - 字符输出流 Writer 写
      - 上面的4个流对象都是抽象类,我们不能直接使用,我们需要使用子类

### IO流的概念

- Java的IO流是实现输入/输出的基础
  - 它可以方便地实现数据的输入/输出操作，在Java中把不同的输入/输出源抽象表述为"流"。
- 流是一组有顺序的
  - 有起点和终点的字节集合，是对数据传输的总称或抽象。即数据在两设备间的传输称为流，流的本质是数据传输，根据数据传输特性将流抽象为各种类，方便更直观的进行数据操作。
- **流有输入和输出，输入时是流从数据源流向程序。输出时是流从程序传向数据源，而数据源可以是内存，文件，网络或程序等。**

#### 输入流和输出流

- 根据数据流向不同分为：输入流和输出流。

  > 输入流:只能从中读取数据，而不能向其写入数据。
  > 输出流：只能向其写入数据，而不能从中读取数据。

#### 节点流和处理流

- 按照流的角色来分，可以分为节点流和处理流。

  - 可以从/向一个特定的IO设备（如磁盘、网络）读/写数据的流，称为节点流，节点流也被成为低级流。处理流是对一个已存在的流进行连接或封装，通过封装后的流来实现数据读/写功能，处理流也被称为高级流。

  ```
  //节点流，直接传入的参数是IO设备
  FileInputStream fis = new FileInputStream("test.txt");
  //处理流，直接传入的参数是流对象
  BufferedInputStream bis = new BufferedInputStream(fis);
  ```



![](http://javacode.blogwiki.top/687474703a2f2f75706c6f61642d696d616765732e6a69616e7368752e696f2f75706c6f61645f696d616765732f333938353536332d306636346133666531613262663062392e706e673f696d6167654d6f6772322f6175746f2d6f7269656e742f737472697025.png)



- 当使用处理流进行输入/输出时，程序并不会直接连接到实际的数据源，没有和实际的输入/输出节点连接。使用处理流的一个明显好处是，只要使用相同的处理流，程序就可以采用完全相同的输入/输出代码来访问不同的数据源，随着处理流所包装节点流的变化，程序实际所访问的数据源也相应地发生变化。
- 实际上，Java使用处理流来包装节点流是一种典型的装饰器设计模式，通过使用处理流来包装不同的节点流，既可以消除不同节点流的实现差异，也可以提供更方便的方法来完成输入/输出功能。







## **IO流字符与字节**



### 字节和字符区别

- 字节流和字符流和用法几乎完全一样，区别在于字节流和字符流所操作的数据单元不同。
- 字节流和字符流的区别：
  - 读写单位不同：字节流以字节（8bit）为单位，字符流以字符为单位，根据码表映射字符，一次可能读多个字节。
  - 处理对象不同：字节流能处理所有类型的数据（如图片、avi等），而字符流只能处理字符类型的数据，比如文本内容。

### 什么是字节

- 关于字节，我们在学习8大基本数据类型中都有了解，每个字节(byte)有8bit组成，每种数据类型又几个字节组成等。

### 什么是字符

- 字符流出现的原因
  - 字符流出现的原因:由于字节流操作中文不是特别方便，所以，java就提供了字符流。
  - 字符流: 字符流 = 字节流 + 编码表
- 字符流的由来：
  - 因为数据编码的不同，而有了对字符进行高效操作的流对象。本质其实就是基于字节流读取时，去查了指定的码表。
- 关于字符
  - 可能代表一个汉字或者英文字母。

### unicode编码

- Java采用unicode编码，2个字节来表示一个字符。
  - Java采用unicode来表示字符，一个中文或英文字符的unicode编码都占2个字节。
  - 但如果采用其他编码方式，一个字符占用的字节数则各不相同。
- 这点与C语言中不同，C语言中采用ASCII。在大多数系统中，一个字符通常占1个字节，但是在0~127整数之间的字符映射，unicode向下兼容ASCII。
- 可能有点晕，举个例子解释下。
  - 例如：Java中的String类是按照unicode进行编码的，当使用String(byte[] bytes, String encoding)构造字符串时，encoding所指的是bytes中的数据是按照那种方式编码的，而不是最后产生的String是什么编码方式，换句话说，是让系统把bytes中的数据由encoding编码方式转换成unicode编码。
  - 如果不指明，bytes的编码方式将由jdk根据操作系统决定。

### 如何获取字节

- ```
  getBytes(String charsetName)
  ```

  使用指定的编码方式将此String编码为 byte 序列，并将结果存储到一个新的 byte 数组中。如果不指定将使用操作系统默认的编码方式，我的电脑默认的是GBK编码。

  ```
  public class YC {  
      public static void main(String[] args){  
          String str = "你好hello";  
          int byte_len = str.getBytes().length;  
          int len = str.length();  
          System.out.println("字节长度为：" + byte_len);  
          System.out.println("字符长度为：" + len);  
          System.out.println("系统默认编码方式：" + System.getProperty("file.encoding"));  
     }  
  }
  ```

- 输出结果

  > 字节长度为：9
  > 字符长度为：7
  > 系统默认编码方式：GBK

- 为什么字节和字符长度不同

  - 在 GB 2312 编码或 GBK 编码中，一个英文字母字符存储需要1个字节，一个汉字字符存储需要2个字节。
  - 在UTF-8编码中，一个英文字母字符存储需要1个字节，一个汉字字符储存需要3到4个字节。
  - 在UTF-16编码中，一个英文字母字符存储需要2个字节，一个汉字字符储存需要3到4个字节（Unicode扩展区的一些汉字存储需要4个字节）。
  - 在UTF-32编码中，世界上任何字符的存储都需要4个字节。

- **简单来讲，一个字符表示一个汉字或英文字母，具体字符与字节之间的大小比例视编码情况而定。有时候读取的数据是乱码，就是因为编码方式不一致，需要进行转换，然后再按照unicode进行编码。**

### 如何选择流

- 只要是处理纯文本数据，就优先考虑使用字符流。除此之外都使用字节流。

### String中编解码问题

- String(byte[] bytes, String charsetName):通过指定的字符集解码字节数组
- byte[] getBytes(String charsetName):使用指定的字符集合把字符串编码为字节数组
- 编码:把看得懂的变成看不懂的:	String -- byte[]
- 解码:把看不懂的变成看得懂的:	byte[] -- String



## **IO流之File类**





### File类介绍

- File类是java.io包下代表与平台无关的文件和目录，也就是说，如果希望在程序中操作**文件和目录**，都可以通过File类来完成。

### File类构造函数

- 构造函数

  ```
  //构造函数File(String pathname)
  File f1 =new File("c:\\abc\\1.txt");
  //File(String parent,String child)
  File f2 =new File("c:\\abc","2.txt");
  //File(File parent,String child)
  File f3 =new File("c:"+File.separator+"abc");//separator 跨平台分隔符
  File f4 =new File(f3,"3.txt");
  System.out.println(f1);//c:\abc\1.txt
  ```

- 路径分隔符：

  - windows： "/" "" 都可以
  - linux/unix： "/"
  - 注意:如果windows选择用""做分割符的话,那么请记得替换成"\",因为Java中""代表转义字符
  - 所以推荐都使用"/"，也可以直接使用代码`File.separator`，表示跨平台分隔符。

- 路径：

  - 相对路径：
    - ./表示当前路径
    - ../表示上一级路径
    - 其中当前路径：默认情况下，java.io 包中的类总是根据当前用户目录来分析相对路径名。此目录由系统属性 user.dir 指定，通常是 Java 虚拟机的调用目录。”
  - 绝对路径：
    - 绝对路径名是完整的路径名，不需要任何其他信息就可以定位自身表示的文件。

### 创建与删除方法

- 如下所示

  ```
  //如果文件存在返回false，否则返回true并且创建文件 
  boolean createNewFile();
  //创建一个File对象所对应的目录，成功返回true，否则false。且File对象必须为路径而不是文件。只会创建最后一级目录，如果上级目录不存在就抛异常。
  boolean mkdir();
  //创建一个File对象所对应的目录，成功返回true，否则false。且File对象必须为路径而不是文件。创建多级目录，创建路径中所有不存在的目录
  boolean mkdirs()    ;
  //如果文件存在返回true并且删除文件，否则返回false
  boolean delete();
  //在虚拟机终止时，删除File对象所表示的文件或目录。
  void deleteOnExit();
  ```

### File判断方法

- 如下所示

  ```
  boolean canExecute()    ;//判断文件是否可执行
  boolean canRead();//判断文件是否可读
  boolean canWrite();//判断文件是否可写
  boolean exists();//判断文件是否存在
  boolean isDirectory();//判断是否是目录
  boolean isFile();//判断是否是文件
  boolean isHidden();//判断是否是隐藏文件或隐藏目录
  boolean isAbsolute();//判断是否是绝对路径 文件不存在也能判断
  ```

### File获取方法

- 如下所示

  ```
  String getName();//返回文件或者是目录的名称
  String getPath();//返回路径
  String getAbsolutePath();//返回绝对路径
  String getParent();//返回父目录，如果没有父目录则返回null
  long lastModified();//返回最后一次修改的时间
  long length();//返回文件的长度
  File[] listRoots();// 列出所有的根目录（Window中就是所有系统的盘符）
  String[] list() ;//返回一个字符串数组，给定路径下的文件或目录名称字符串
  String[] list(FilenameFilter filter);//返回满足过滤器要求的一个字符串数组
  File[]  listFiles();//返回一个文件对象数组，给定路径下文件或目录
  File[] listFiles(FilenameFilter filter);//返回满足过滤器要求的一个文件对象数组
  ```

- 其中包含了一个重要的接口FileNameFilter

  - 该接口是个文件过滤器，包含了一个`accept(File dir,String name)`方法，该方法依次对指定File的所有子目录或者文件进行迭代，按照指定条件，进行过滤，过滤出满足条件的所有文件。

  ```
  // 文件过滤
  File[] files = file.listFiles(new FilenameFilter() {
      @Override
      public boolean accept(File file, String filename) {
          return filename.endsWith(".mp3");
      }
  });
  ```

  - file目录下的所有子文件如果满足后缀是.mp3的条件的文件都会被过滤出来。





## **IO流体系图**

![](http://javacode.blogwiki.top/141330564425447.jpg)



#### 按照流向进行划分

- 根据流的流向以及操作的数据单元不同，将流分为了四种类型，每种类型对应一种抽象基类。

  - 这四种抽象基类分别为：InputStream,Reader,OutputStream以及Writer。四种基类下，对应不同的实现类，具有不同的特性。在这些实现类中，又可以分为节点流和处理流。下面就是整个由着四大基类支撑下，整个IO流的框架图。

    o

    ![](http://javacode.blogwiki.top/687474703a2f2f75706c6f61642d696d616765732e6a69616e7368752e696f2f75706c6f61645f696d616765732f333938353536332d333863336561343536326436646265332e706e673f696d6167654d6f677232.png)



#### 按操作方式分类结构图

- 关于IO流总结如下图所示
- ![](http://javacode.blogwiki.top/68747470733a2f2f75706c6f61642d696d616765732e6a69616e7368752e696f2f75706c6f61645f696d616765732f343433323334372d646462633162393666356363623638382e706e673f696d6167654d6f6772322f6175746f2d6f7269656e742f737472697025.png)





#### 按操作对象分类结构图

- 关于IO流总结如下图所示

![](http://javacode.blogwiki.top/68747470733a2f2f75706c6f61642d696d616765732e6a69616e7368752e696f2f75706c6f61645f696d616765732f343433323334372d316365373638623934353034316365382e706e673f696d6167654d6f6772322f6175746f2d6f7269656e742f737472697025.png)



#### 基本概念文字描述

- IO流基类概述
  - a:字节流的抽象基类：
    - InputStream ，OutputStream。
  - b:字符流的抽象基类：
    - Reader ， Writer。
    - 注：由这四个类派生出来的子类名称都是以其父类名作为子类名的后缀。
    - 如：InputStream的子类FileInputStream。
    - 如：Reader的子类FileReader。
- 字节流和字符流：
  - 字节流：以字节为单位，每次次读入或读出是8位数据。可以读任何类型数据。
  - 字符流：以字符为单位，每次次读入或读出是16位数据。其只能读取字符类型数据。
- 输出流和输入流：
  - 输出流：从内存读出到文件。只能进行写操作。
  - 输入流：从文件读入到内存。只能进行读操作。
  - 注意：这里的出和入，都是相对于系统内存而言的。
- 节点流和处理流：
  - 节点流：直接与数据源相连，读入或读出。
  - 处理流：与节点流一块使用，在节点流的基础上，再套接一层，套接在节点流上的就是处理流。
  - 为什么要有处理流？直接使用节点流，读写不方便，为了更快的读写文件，才有了处理流。

### 02.InputStream字节输入流

- InputStream 是所有的输入字节流的父类，它是一个抽象类，主要包含三个方法：

  ```
  //读取一个字节并以整数的形式返回(0~255),如果返回-1已到输入流的末尾。 
  int read() ； 
  //读取一系列字节并存储到一个数组buffer，返回实际读取的字节数，如果读取前已到输入流的末尾返回-1。 
  int read(byte[] buffer) ； 
  //读取length个字节并存储到一个字节数组buffer，从off位置开始存,最多len， 返回实际读取的字节数，如果读取前以到输入流的末尾返回-1。 
  int read(byte[] buffer, int off, int len) ；
  ```

### 03.OutputStream字节输出流

- OutputStream 是所有的输出字节流的父类，它是一个抽象类，主要包含如下四个方法：

  ```
  //向输出流中写入一个字节数据,该字节数据为参数b的低8位。 
  void write(int b) ; 
  //将一个字节类型的数组中的数据写入输出流。 
  void write(byte[] b); 
  //将一个字节类型的数组中的从指定位置（off）开始的,len个字节写入到输出流。 
  void write(byte[] b, int off, int len); 
  //将输出流中缓冲的数据全部写出到目的地。 
  void flush();
  ```

### 04.Reader字符输入流

- Reader 是所有的输入字符流的父类，它是一个抽象类，主要包含三个方法：

  ```
  //读取一个字符并以整数的形式返回(0~255),如果返回-1已到输入流的末尾。 
  int read() ； 
  //读取一系列字符并存储到一个数组buffer，返回实际读取的字符数，如果读取前已到输入流的末尾返回-1。 
  int read(char[] cbuf) ； 
  //读取length个字符,并存储到一个数组buffer，从off位置开始存,最多读取len，返回实际读取的字符数，如果读取前以到输入流的末尾返回-1。 
  int read(char[] cbuf, int off, int len)
  ```

- 对比InputStream和Reader所提供的方法，就不难发现两个基类的功能基本一样的，只不过读取的数据单元不同。

  - **在执行完流操作后，要调用**`close()`**方法来关系输入流，因为程序里打开的IO资源不属于内存资源，垃圾回收机制无法回收该资源，所以应该显式关闭文件IO资源。**

- 除此之外，InputStream和Reader还支持如下方法来移动流中的指针位置：

  博客

  ```
  //在此输入流中标记当前的位置
  //readlimit - 在标记位置失效前可以读取字节的最大限制。
  void mark(int readlimit)
  // 测试此输入流是否支持 mark 方法
  boolean markSupported()
  // 跳过和丢弃此输入流中数据的 n 个字节/字符
  long skip(long n)
  //将此流重新定位到最后一次对此输入流调用 mark 方法时的位置
  void reset()
  ```

### 05.Writer字符输出流

- Writer 是所有的输出字符流的父类，它是一个抽象类,主要包含如下六个方法：

  ```
  //向输出流中写入一个字符数据,该字节数据为参数b的低16位。 
  void write(int c); 
  //将一个字符类型的数组中的数据写入输出流， 
  void write(char[] cbuf) 
  //将一个字符类型的数组中的从指定位置（offset）开始的,length个字符写入到输出流。 
  void write(char[] cbuf, int offset, int length); 
  //将一个字符串中的字符写入到输出流。 
  void write(String string); 
  //将一个字符串从offset开始的length个字符写入到输出流。 
  void write(String string, int offset, int length); 
  //将输出流中缓冲的数据全部写出到目的地。 
  void flush()
  ```

- 可以看出，Writer比OutputStream多出两个方法，主要是支持写入字符和字符串类型的数据。





## **IO字节流案例**



### FileOutputStream

- A: 构造方法

  - FileOutputStream(File file)
  - FileOutputStream(String name)
  - 注意事项：创建字节输出流对象了做了几件事情?为什么一定要close()?

  ```
  public static void main(String[] args) throws IOException {
      FileOutputStream fos = new FileOutputStream("a.txt") ;
      /**
       * 上面的代码做了什么事情?
       *         1: 调用系统资源,创建了一个a.txt文件
       *         2: 创建一个fos对象
       *         3: 把fos指向a.txt
       */
      String s = "hello, IO , 我来了" ;
      // 把字符串转换成字节数组
      byte[] bytes = s.getBytes() ;
      // 调用方法写数据
      fos.write(bytes) ;
      /**
       * 关闭流对象
       * 作用: 通知系统释放关于该文件的资源
       */
      fos.close() ;
  }
  ```

- B:FileOutputStream的三个write()方法

  - public void write(int b):写一个字节
  - public void write(byte[] b):写一个字节数组
  - public void write(byte[] b,int off,int len):写一个字节数组的一部分
  - FileOutputStream写出数据实现换行和追加写入
    - 实现数据的换行
    - 实现数据的追加写入

  ```
  public static void main(String[] args) throws IOException {
      // public FileOutputStream(String name,  boolean append)
      // 表示追加写入
      FileOutputStream fos = new FileOutputStream("c.txt" , true) ;       
      // 写数据
      for(int x = 0 ; x < 5 ; x++) {
          byte[] bytes = ("呵呵" + x + "\r\n").getBytes() ;
          fos.write(bytes) ;
      }
      // 是否资源
      fos.close() ;
  }
  ```

### 03.FileInputStream

- FileInputStream读取数据一次一个字节
  - int read():一次读取一个字节

### 04.字节流复制文本文件

#### 4.1 字节流复制文本文件【字节流一次读写一个字节复制文本文件】

- 一次读取一个字节

  ```
  public static void main(String[] args) throws IOException {
      /**
       * 复制文本文件:
       *         读和写
       *     分析:
       *         1: 创建两个对象一个是字节输入流对象,一个是字节输出流对象
       *         2: 一次读取一个字节,一次写一个字节
       *         3: 释放资源
       */
      // 创建两个对象一个是字节输入流对象,一个是字节输出流对象
      FileInputStream fis = new FileInputStream("FileOutputStreamDemo.java") ;
      FileOutputStream fos = new FileOutputStream("copyFile.java") ;
      // 一次读取一个字节,一次写一个字节
      int by = 0 ;
      while((by = fis.read()) != -1){
          fos.write(by) ;
      }
      // 释放资源
      fos.close() ;
      fis.close() ;
  }
  ```

#### 4.2 字节流复制文本文件【字节流一次读写一个字节数组复制文本文件】

- 字节流一次读写一个字节数组

  ```
  public static void main(String[] args) throws IOException {
      /**
       * 需求: 使用字节流复制文本文件,采用字节输入流的第二种读取数据的方式
       */
      // 创建字节输入流对象和字节输出流对象
      FileInputStream fis = new FileInputStream("FileOutputStreamDemo.java") ;
      FileOutputStream fos = new FileOutputStream("copyFile2.java") ;
      // 一次读取一个字节数组复制文件
      byte[] bytes = new byte[1024] ;
      int len = 0 ;        // 作用: 记录读取到的有效的字节个数
      while((len = fis.read(bytes)) != -1){
          fos.write(bytes, 0, len) ;
      }
      // 释放资源
      fos.close() ;
      fis.close() ;
  }
  ```

### 05.字节流复制MP3

#### 5.1 字节流复制MP3【字节流一次读写一个字节复制MP3】

- 一次读取一个字节

  ```
  public static void main(String[] args) throws IOException {
      // 需求: 使用字节流复制mp3文件
      // 创建字节输入流和字节输出流对象
      FileInputStream fis = new FileInputStream("C:\\a.mp3") ;
      FileOutputStream fos = new FileOutputStream("D:\\a.mp3") ;
      // 复制文件
      int by = 0 ;
      while((by = fis.read()) != -1){
          fos.write(by) ;
      }
      // 释放资源
      fos.close() ;
      fis.close() ;
  }
  ```

#### 5.2 字节流复制MP3【字节流一次读写一个字节数组复制MP3】

- 一次读取一个字节数组

  ```
  public static void main(String[] args) throws IOException {
      /**
       * 需求: 使用字节流复制mp3文件一次读取一个字节数组
       */
      // 创建对象
      FileInputStream fis = new FileInputStream("C:\\a.mp3") ;
      FileOutputStream fos = new FileOutputStream("D:\\a.mp3") ;
      // 一次读取一个字节数组复制文件
      byte[] bytes = new byte[1024] ;
      int len = 0 ;
      while((len = fis.read(bytes)) != -1){
          fos.write(bytes, 0, len) ;
      }
      // 释放资源
      fos.close() ;
      fis.close() ;
  }
  ```

### 06.字节流四种方式复制MP3效率

- 四种方式如下所示

  - 基本字节流一次读写一个字节
  - 基本字节流一次读写一个字节数组
  - 高效字节流一次读写一个字节
  - 高效字节流一次读写一个字节数组

- 代码如下所示

  ```
  public class CopyFileDemo {
  
      public static void main(String[] args) throws IOException {
          // 获取开始时间
          long startTime = System.currentTimeMillis() ;
          // 复制文件
          copyFile_4() ;
          // 获取结束时间
          long endTime = System.currentTimeMillis() ;
          // 输出
          System.out.println("复制文件使用的时间是:" + (endTime - startTime) + "毫秒");
      }
  
      /**
       * 高效流一次读取一个字节数组赋值文件
       * @throws IOException
       */
      public static void copyFile_4() throws IOException {       
          // 复制文件使用的时间是:50毫秒
          // 创建高效流对象
          BufferedInputStream bis = new BufferedInputStream(new FileInputStream("C:\\a.avi")) ;
          BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("D:\\a.avi")) ;
          // 一次读取一个字节数组
          byte[] bytes = new byte[1024] ;
          int len = 0 ;
          while((len = bis.read(bytes)) != -1){
              bos.write(bytes, 0, len) ;
          }
          // 释放资源
          bos.close() ;
          bis.close() ;
      }
  
      /**
       * 高效流一次读取一个字节复制文件
       * @throws IOException
       */
      public static void copyFile_3() throws IOException {       
          // 复制文件使用的时间是:990毫秒
          // 创建高效流对象
          BufferedInputStream bis = new BufferedInputStream(new FileInputStream("C:\\a.avi")) ;
          BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("D:\\a.avi")) ;
          // 一次读取一个字节
          int by = 0 ;
          while((by = bis.read()) != -1){
              bos.write(by) ;
          }
          // 释放资源
          bos.close() ;
          bis.close() ;
      }
  
      /**
       * 基本流一次读取一个字节数组复制文件
       * @throws IOException
       */
      private static void copyFile_2() throws IOException {       
          // 复制文件使用的时间是:130毫秒
          // 创建对象
          FileInputStream fis = new FileInputStream("C:\\a.avi") ;
          FileOutputStream fos = new FileOutputStream("D:\\a.avi") ;
  
          // 一次读取一个字节数组
          byte[] bytes = new byte[1024] ;
          int len = 0 ;
          while((len = fis.read(bytes)) != -1){
              fos.write(bytes, 0, len) ;
          }
          // 释放资源
          fos.close() ;
          fis.close() ;
      }
  
      /**
       * 基本流一次读取一个字节复制文件
       * @throws IOException
       */
      private static void copyFile_1() throws IOException {   
          // 复制文件使用的时间是:88670毫秒
          // 创建对象
          FileInputStream fis = new FileInputStream("C:\\a.avi") ;
          FileOutputStream fos = new FileOutputStream("D:\\a.avi") ;
          // 一次读取一个字节
          int by = 0 ;
          while((by = fis.read()) != -1){
              fos.write(by) ;
          }
          // 释放资源
          fos.close() ;
          fis.close() ;
      }
  }
  ```



## **IO字符流案例**

### 字符流OutputStreamWriter

- 转换流OutputStreamWriter的使用

  ```
  /**
   * 字符输出流:		OutputStreamWriter (转换输出流)		字符流通向字节流的桥梁
   * 字符输入流:		InputStreamReader  (转换输入流)		字节流通向字符流的桥梁
   */
  public class OutputStreamWriterDemo {
  	public static void main(String[] args) throws IOException {
  		// 创建: OutputStreamWriter
  		// 创建: OutputStream的对象
  //		FileOutputStream fos = new FileOutputStream("a.txt") ;
  //		OutputStreamWriter osw = new OutputStreamWriter(fos) ;
  //		OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream("a.txt")) ;
  		// public InputStreamReader(InputStream in , String charsetName)	使用指定的编码表
  		OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream("a.txt") , "UTF-8") ;
  		// 调用方法
  		osw.write("中国") ;
  		// 释放资源
  		osw.close() ;
  	}
  }
  ```

### 02.字符流InputStreamReader

- 转换流InputStreamReader的使用

  ```
  public class InputStreamReaderDemo {
  	public static void main(String[] args) throws IOException {
  		// 创建对象InputStreamReader的对象
  		// public InputStreamReader(InputStream in): 使用的默认的编码表(GBK)
  //		FileInputStream fis = new FileInputStream("a.txt") ;
  //		InputStreamReader isr = new InputStreamReader(fis) ;
  //		InputStreamReader isr = new InputStreamReader(new FileInputStream("a.txt")) ;	// 默认的字符集就是GBK
  		
  		// public InputStreamReader(InputStream in , String charsetName)	使用指定的编码表
  		InputStreamReader isr = new InputStreamReader(new FileInputStream("a.txt") , "utf-8") ;	
  		// 读取数据
  		int ch = 0 ;
  		while((ch = isr.read()) != -1){
  			System.out.print((char)ch);
  		}
  		// 释放资源
  		isr.close() ;
  	}
  }
  ```

### 03.字符流读写方式

- 字符流的5种写数据的方式

  ```
  public void write(int c)
  public void write(char[] cbuf)
  public void write(char[] cbuf,int off,int len)
  public void write(String str)
  public void write(String str,int off,int len)
  ```

- 字符流的2种读数据的方式

  ```
  public int read()
  public int read(char[] cbuf)
  ```

### 04.字符流复制文本文件

- 字符流复制文本文件

  ```
  public class CopyFileDemo {
  	public static void main(String[] args) throws IOException {
  		// 创建转换输入流对象
  		InputStreamReader isr = new InputStreamReader(new FileInputStream("OutputStreamWriterDemo.java")) ;
  		// 创建转换输出流对象
  		OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream("copyFile.java")) ;
  		// 复制文件
  		// 一次读取一个字符复制
  //		int ch = 0 ;
  //		while((ch = isr.read()) != -1){
  //			osw.write(ch) ;
  //		}
  		// 一次读取一个字符数组复制文件
  		char[] chs = new char[1024] ;
  		int len = 0 ;
  		while((len = isr.read(chs)) != -1){
  			osw.write(chs, 0, len) ;
  		}
  		// 释放资源
  		osw.close() ;
  		isr.close() ;
  	}
  }
  ```

### 05.复制单级文件夹

- 复制单级文件夹，代码如下所示

  ```
  public static void main(String[] args) throws IOException {
  	/**
  	 * 需求: 把C:\\course这个文件夹复制到D:\\course盘下
  	 * 分析:
  	 * 		1: 把C:\\course这个目录封装成一个File对象
  	 * 		2: 把D:\\course这个目录封装成一个File对象
  	 * 		3: 判断D:\\course是否存在,如果存在就创建一个文件夹
  	 * 		4: 获取C:\\course这个目录下所有的文件对应的File数组
  	 * 		5: 遍历数组,获取元素进行复制
  	 */
  	// 把C:\\course这个目录封装成一个File对象
  	File srcFolder = new File("C:\\course") ;
  	// 把D:\\course这个目录封装成一个File对象
  	File destFolder = new File("D:\\course") ;
  	// 判断D:\\course是否存在,如果存在就创建一个文件夹
  	if(!destFolder.exists()){
  		destFolder.mkdir() ;
  	}
  	// 复制文件夹
  	IOUtils.copyFolder(srcFolder, destFolder, null) ;
  }
  
  public class IOUtils {
  	
  	public static void copyFolder(File srcFolder , File destFolder , FilenameFilter filenameFilter) throws IOException {
  		File[] files = null ;
  		if(filenameFilter == null) {
  			files = srcFolder.listFiles() ;
  		}else {
  			files = srcFolder.listFiles(filenameFilter) ;
  		}
  		// 遍历
  		for(File f : files) {
  			// 创建目标文件
  			String destFileName = f.getName() ;
  			File destFile = new File(destFolder , destFileName) ; 
  			// 复制文件
  			copyFile(f , destFile) ;
  		}
  	} 
  	
  	public static void copyFile(File srcFile , File destFile) throws IOException {
  		// 创建流对象
  		BufferedInputStream bis = new BufferedInputStream(new FileInputStream(srcFile)) ;
  		BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream(destFile)) ;
  		
  		// 一次读取一个字节数组复制文件
  		byte[] bytes = new byte[1024] ;
  		int len = 0 ;
  		while((len = bis.read(bytes)) != -1){
  			bos.write(bytes, 0, len) ;
  		}
  		// 释放资源
  		bos.close() ;
  		bis.close() ;
  	}
  }
  ```

### 06.复制指定目录下指定后缀名的文件并修改名称

- 复制指定目录下指定后缀名的文件并修改名称

  博客

  ```
  public static void main(String[] args) throws IOException {
  	/**
  	 * 把C:\\demo这个目录下所有的以.java结尾的文件复制到D:\\demo中,然后将这个文件的后缀名更改为.jad
  	 */
  	// 把C:\\demo这个目录下所有的以.java结尾的文件复制到D:\\demo中
  	// 1: 把C:\\demo这个目录封装成一个File对象
  	File srcFolder = new File("C:\\demo") ;
  	// 2: 把D:\\demo这么目录封装成一个File对象
  	File destFolder = new File("D:\\demo") ;
  	// 3: 判断D:\\demo这个路径是否存在
  	if(!destFolder.exists()) {
  		destFolder.mkdir() ;
  	}
  	// 调用方法
  	IOUtils.copyFolder(srcFolder, destFolder, new FilenameFilter() {
  		@Override
  		public boolean accept(File dir, String name) {
  			return new File(dir , name).isFile() && name.endsWith(".java") ;
  		}
  	}) ;
  	System.out.println("-----------------------------------------------------");
  	// 获取destFolder下所有的文件对应的File数组
  	File[] files = destFolder.listFiles() ;
  	for(File f : files) {
  		// 创建目标文件名称		
  		String destFileName = f.getName().replace(".java", ".jad") ;
  		// 创建目标文件
  		File destFile = new File(destFolder , destFileName) ;
  		// 调用
  		f.renameTo(destFile) ;
  	}
  }
  
  
  public class IOUtils {
  	
  	public static void copyFolder(String srcPahtName , String destPathName , FilenameFilter filenameFilter) throws IOException {
  		File srcFolder = new File(srcPahtName) ;
  		File destFolder = new File(destPathName) ;
  		if(!destFolder.exists()) {
  			destFolder.mkdir() ;
  		}
  		copyFolder(srcFolder , destFolder , filenameFilter) ;
  	}
  	
  	public static void copyFolder(File srcFolder , File destFolder , FilenameFilter filenameFilter) throws IOException {
  		File[] files = null ;
  		if(filenameFilter == null) {
  			files = srcFolder.listFiles() ;
  		}else {
  			files = srcFolder.listFiles(filenameFilter) ;
  		}
  		// 遍历
  		for(File f : files) {
  			// 创建目标文件
  			String destFileName = f.getName() ;
  			File destFile = new File(destFolder , destFileName) ; 
  			// 复制文件
  			copyFile(f , destFile) ;
  		}
  	} 
  	
  	public static void copyFile(File srcFile , File destFile) throws IOException {
  		// 创建流对象
  		BufferedInputStream bis = new BufferedInputStream(new FileInputStream(srcFile)) ;
  		BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream(destFile)) ;
  		// 一次读取一个字节数组复制文件
  		byte[] bytes = new byte[1024] ;
  		int len = 0 ;
  		while((len = bis.read(bytes)) != -1){
  			bos.write(bytes, 0, len) ;
  		}
  		// 释放资源
  		bos.close() ;
  		bis.close() ;
  	}
  }
  ```





## **Buffer高效字节流**



### BufferedOutputStream写出数据

- 代码如下所示：

  ```
  public static void main(String[] args) throws IOException {
      /**
       * BufferedOutputStream构造方法:
       *         public BufferedOutputStream(OutputStream out)
       */
      // 创建FileOutputStream对象
      //FileOutputStream fos = new FileOutputStream("buf.txt") ;
      //BufferedOutputStream bof = new BufferedOutputStream(fos) ;
      BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("buf.txt")) ;
      // 调用方法
      bos.write("哈哈,我来了".getBytes()) ;
      // 释放资源
      bos.close() ;
  }
  ```

### BufferedInputStream读取数据

- 代码如下所示：

  ```
  public static void main(String[] args) throws IOException {
      /**
       * BufferedInputStream构造方法:
       *         public BufferedInputStream(InputStream in)
       */
      BufferedInputStream bis = new BufferedInputStream(new FileInputStream("e.txt")) ;
      // 一次读取一个字节
      //int by = 0 ;
      //while((by = bis.read()) != -1){
      //    System.out.print((char)by);
      //}
      // 一次读取一个字节数组
      byte[] bytes = new byte[1024] ;
      int len = 0 ;
      while((len = bis.read(bytes)) != -1){
          System.out.print(new String(bytes , 0 , len));
      }
      // 释放资源
      bis.close() ;
  }
  ```



## **Buffer高效字符流**



### 字符缓冲流BufferedReader

- 代码如下所示

  ```
  /**
   * 使用高效的字符输入流进行读取数据
   * @throws IOException
   */
  private static void read() throws IOException{
  	// 创建高效的字符输入流对象
  	// public BufferedReader(Reader in)
  //		BufferedReader br = new BufferedReader(new InputStreamReader(new FileInputStream("c.txt"))) ;
  	BufferedReader br = new BufferedReader(new FileReader("c.txt")) ;
  	// 读取数据
  //		int ch = 0 ;
  //		while((ch = br.read()) != -1){
  //			System.out.print((char)ch);
  //		}
  	char[] chs = new char[1024] ;
  	int len = 0 ;
  	while((len = br.read(chs)) != -1){
  		System.out.print(new String(chs , 0 , len));
  	}
  	// 释放资源
  	br.close() ;
  }
  ```

### 03.字符缓冲流BufferedWriter

- 代码如下所示

  ```
  /**
   * 使用高效的字符输出流进行写数据
   * @throws IOException 
   */
  private static void write() throws IOException {
  	// 创建BufferedWriter对象
  	// public BufferedWriter(Writer out)
  //		OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream("c.txt")) ;
  //		BufferedWriter bw = new BufferedWriter(osw) ;
  //		BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(new FileOutputStream("c.txt"))) ;
  	BufferedWriter bw = new BufferedWriter(new FileWriter("c.txt")) ;
  	// 写数据
  	bw.write("你好") ;
  	// 释放资源
  	bw.close() ;
  }
  ```

### 04.字符缓冲流的特殊功能

- 如下所示

  博客

  ```
  BufferedWriter:	public void newLine():根据系统来决定换行符
  BufferedReader:	public String readLine()：一次读取一行数据
  包含该行内容的字符串，不包含任何行终止符，如果已到达流末尾，则返回 null
  ```

### 05.高效字符流复制文本文件

- FileWriter和FileReader复制文本文件，一次读取一个字符数组复制文件

  ```
  public class CopyFileDemo {
  	public static void main(String[] args) throws IOException {
  		// 创建高效的字符输入流对象
  		BufferedReader br = new BufferedReader(new FileReader("OutputStreamWriterDemo.java")) ;
  		// 创建高效的字符输出流对象
  		BufferedWriter bw = new BufferedWriter(new FileWriter("copyFile3.java")) ;
  		// 一次读取一个字符数组复制文件
  		char[] chs = new char[1024] ;
  		int len = 0;
  		while((len = br.read(chs)) != -1){
  			bw.write(chs, 0, len) ;
  		} 
  		// 释放资源
  		bw.close() ;
  		br.close() ;
  	}
  }
  ```

- 字符缓冲流的特殊功能复制文本文件，一次读取一行复制文件

  ```
  public class CopyFileDemo {
  	public static void main(String[] args) throws IOException {
  		/**
  		 * 需求: 使用高效的字符流中特有的功能复制文本文件
  		 */
  		// 创建高效的字符输入流对象
  		BufferedReader br = new BufferedReader(new FileReader("OutputStreamWriterDemo.java")) ;
  		
  		// 高效的字符输出流对象
  		BufferedWriter bw = new BufferedWriter(new FileWriter("copyFile4.java")) ;
  		
  		// 复制文件
  		// 一次读取一行复制文件
  		String line = null ;
  		while((line = br.readLine()) != null) {
  			bw.write(line) ;
  			bw.newLine() ;
  			bw.flush() ;
  		}
  		// 释放资源
  		bw.close() ;
  		br.close() ;
  	}
  }
  ```

### 06.集合数据存储到文本文件

- 把集合中的数据存储到文本文件

  博客

  ```
  public static void main(String[] args) throws IOException {
  	/**
  	 * 把ArrayList集合中的数据存储到文本文件中
  	 * 分析:
  	 * 		1: 创建ArrayList集合对象
  	 * 		2: 添加数据
  	 * 		3: 创建高效的字符输出流对象
  	 * 		4: 遍历集合,获取每一个元素,然后通过流对象写出去
  	 * 		5: 释放资源
  	 */
  	// 创建ArrayList集合对象
  	ArrayList<String> al = new ArrayList<String>() ;
  	// 添加数据
  	al.add("西施") ;
  	al.add("貂蝉") ;
  	al.add("杨玉环") ;
  	al.add("王昭君") ;
  	// 创建高效的字符输出流对象
  	BufferedWriter bw = new BufferedWriter(new FileWriter("names.txt")) ;
  	// 遍历集合,获取每一个元素,然后通过流对象写出去
  	for(String name : al) {
  		bw.write(name) ;
  		bw.newLine() ;
  		bw.flush() ;
  	}
  	// 释放资源
  	bw.close() ;
  }
  ```

### 07.文本数据存储到集合中

- 把文本文件中的数据存储到集合中

  ```
  public static void main(String[] args) throws IOException {
  	/**
  	 * 从文本文件中读取数据(每一行为一个字符串数据)到集合中，并遍历集合
  	 * 分析:
  	 * 		1: 创建高效的字符输入流对象
  	 * 		2: 创建集合对象
  	 * 		3: 读取文本文件中的数据,将数据存储到集合中
  	 * 		4: 释放资源
  	 * 		5: 遍历集合
  	 */
  	// 1: 创建高效的字符输入流对象
  	BufferedReader br = new BufferedReader(new FileReader("names.txt")) ;
  	// 2: 创建集合对象
  	ArrayList<String> al = new ArrayList<String>() ;
  	// 3: 读取文本文件中的数据,将数据存储到集合中
  	String line = null ;	// 作用: 用来记录读取到的行数据
  	while((line = br.readLine()) != null) {
  		al.add(line) ;
  	}
  	// 4: 释放资源
  	br.close() ;
  	// 5: 遍历集合
  	for(String name : al) {
  		System.out.println(name);
  	}
  }
  ```

### 08.随机获取文本中姓名

- 随机获取文本文件中的姓名

  ```
  public static void main(String[] args) throws IOException {
  	// 1: 创建集合对象
  	ArrayList<String> students = new ArrayList<String> () ;
  	// 2: 创建BufferedReader对象
  	BufferedReader br = new BufferedReader(new FileReader("students.txt")) ;
  	// 3: 读取数据,把数据存储到集合中
  	String line = null ;
  	while((line = br.readLine()) != null) {
  		students.add(line) ;
  	}
  	// 4: 释放资源
  	br.close() ;
  	// 5: 生成一个随机数
  	Random random = new Random() ;
  	int index = random.nextInt(students.size());
  	// 6: 把生成的随机数作为集合元素的索引,来获取一个元素
  	String name = students.get(index) ;
  	// 7: 把获取到的元素打印到控制台
  	System.out.println(name);
  }
  ```

### 09.录入信息并且写入文件

- 键盘录入学生信息按照总分排序并写入文本文件

  ```
  public static void main(String[] args) throws IOException {
  	
  	/**
  	 * 需求：键盘录入3个学生信息(姓名,语文成绩(chineseScore),数学成绩(mathScore),英语成绩(englishScore)),按照总分从高到低存入文本文件
  	 * 分析:
  	 * 		1: 创建一个学生类
  	 * 		2: 创建一个集合对象TreeSet集合
  	 * 		3: 键盘录入学生信息,把学生信息封装到学生对象中,然后把学生对象添加到集合中
  	 * 		4: 创建一个高效的字符输出流对象
  	 * 		5: 遍历集合,获取每一个元素,把其信息写入到文件中
  	 * 		6: 释放资源
  	 */
  	//  创建一个集合对象TreeSet集合
  	TreeSet<Student> ts = new TreeSet<Student>(new Comparator<Student>() {
  		@Override
  		public int compare(Student s1, Student s2) {
  			// 总分
  			int num = s2.getTotalScore() - s1.getTotalScore() ;
  			// 比较姓名
  			int num2 = (num == 0) ? s2.getName().compareTo(s1.getName()) : num ;
  			// 返回
  			return num2;
  		}
  	}) ;
  	
  	// 3: 键盘录入学生信息,把学生信息封装到学生对象中,然后把学生对象添加到集合中
  	for(int x = 1 ; x <= 3 ; x++) {
  		// 创建Scanner对象
  		Scanner sc = new Scanner(System.in) ;
  		System.out.println("请您输入第" + x + "个学生的姓名" );
  		String sutName = sc.nextLine() ;
  		System.out.println("请您输入第" + x + "个学生的语文成绩" );
  		int chineseScore = sc.nextInt() ;
  		System.out.println("请您输入第" + x + "个学生的数学成绩" );
  		int mathScore = sc.nextInt() ;
  		System.out.println("请您输入第" + x + "个学生的英语成绩" );
  		int englishScore = sc.nextInt() ;
  		
  		// 把学生的信封装到一个学生对象中
  		Student s = new Student() ;
  		s.setName(sutName) ;
  		s.setMathScore(mathScore) ;
  		s.setChineseScore(chineseScore) ;
  		s.setEnglishScore(englishScore) ;
  		// 把学生的信息添加到集合中
  		ts.add(s) ;
  	}
  	
  	// 创建一个高效的字符输出流对象
  	BufferedWriter bw = new BufferedWriter(new FileWriter("student.info")) ;
  	bw.write("==========================================学生的信息如下====================================================") ;
  	bw.newLine() ;
  	bw.flush() ;
  	bw.write("姓名\t\t总分\t\t数学成绩\t\t语文成绩\t\t英语成绩\t\t") ;
  	bw.newLine() ;
  	bw.flush() ;
  	for(Student t : ts) {
  		bw.write(t.getName() + "\t\t" + t.getTotalScore() + "\t\t" + t.getMathScore() + "\t\t" + t.getChineseScore() + "\t\t" + t.getEnglishScore()) ;
  		bw.newLine() ;
  		bw.flush() ;
  	}
  	// 释放资源
  	bw.close() ;
  }
  ```

## **RandomAccessFile**

### RandomAccessFile简介

- RandomAccessFile既可以读取文件内容，也可以向文件输出数据。同时，RandomAccessFile支持“随机访问”的方式，程序快可以直接跳转到文件的任意地方来读写数据。
- 由于RandomAccessFile可以自由访问文件的任意位置，**所以如果需要访问文件的部分内容，而不是把文件从头读到尾，使用RandomAccessFile将是更好的选择。**
- 与OutputStream、Writer等输出流不同的是，RandomAccessFile允许自由定义文件记录指针，RandomAccessFile可以不从开始的地方开始输出，因此RandomAccessFile可以向已存在的文件后追加内容。**如果程序需要向已存在的文件后追加内容，则应该使用RandomAccessFile。**
- RandomAccessFile的方法虽然多，但它有一个最大的局限，就是只能读写文件，不能读写其他IO节点。
- **RandomAccessFile的一个重要使用场景就是网络请求中的多线程下载及断点续传。**

### 02.RandomAccessFile构造函数

- RandomAccessFile类有两个构造函数，其实这两个构造函数基本相同，只不过是指定文件的形式不同

  - 一个需要使用String参数来指定文件名，一个使用File参数来指定文件本身。
  - 除此之外，创建RandomAccessFile对象时还需要指定一个mode参数，该参数指定RandomAccessFile的访问模式，一共有4种模式。

- 关于权限说明

  > **"r":** 以只读方式打开。调用结果对象的任何 write 方法都将导致抛出 IOException。
  > **"rw":** 打开以便读取和写入。
  > **"rws":** 打开以便读取和写入。相对于 "rw"，"rws"。还要求对“文件的内容”或“元数据”的每个更新都同步写入到基础存储设备。
  > **"rwd" :** 打开以便读取和写入，相对于 "rw"，"rwd"。还要求对“文件的内容”的每个更新都同步写入到基础存储设备。

### 03.RandomAccessFile重要方法

- RandomAccessFile既可以读文件，也可以写文件，所以类似于InputStream的read()方法，以及类似于OutputStream的write()方法，RandomAccessFile都具备。除此之外，RandomAccessFile具备两个特有的方法，来支持其随机访问的特性。

- RandomAccessFile对象包含了一个记录指针，用以标识当前读写处的位置，当程序新创建一个RandomAccessFile对象时，该对象的文件指针记录位于文件头（也就是0处），当读/写了n个字节后，文件记录指针将会后移n个字节。除此之外，RandomAccessFile还可以自由移动该记录指针。下面就是RandomAccessFile具有的两个特殊方法，来操作记录指针，实现随机访问：

  > long getFilePointer( )：返回文件记录指针的当前位置
  > void seek(long pos )：将文件指针定位到pos位置

### 04.RandomAccessFile使用

- 利用RandomAccessFile实现文件的多线程下载，即多线程下载一个文件时，将文件分成几块，每块用不同的线程进行下载。

- 下面是一个利用多线程在写文件时的例子，其中预先分配文件所需要的空间，然后在所分配的空间中进行分块，然后写入：

  ```
  /** 
   * 测试利用多线程进行文件的写操作 
   */  
  public class Test {  
  
      public static void main(String[] args) throws Exception {  
          // 预分配文件所占的磁盘空间，磁盘中会创建一个指定大小的文件  
          RandomAccessFile raf = new RandomAccessFile("D://abc.txt", "rw");  
          raf.setLength(1024*1024); // 预分配 1M 的文件空间  
          raf.close();  
  
          // 所要写入的文件内容  
          String s1 = "第一个字符串";  
          String s2 = "第二个字符串";  
          String s3 = "第三个字符串";  
          String s4 = "第四个字符串";  
          String s5 = "第五个字符串";  
  
          // 利用多线程同时写入一个文件  
          new FileWriteThread(1024*1,s1.getBytes()).start(); // 从文件的1024字节之后开始写入数据  
          new FileWriteThread(1024*2,s2.getBytes()).start(); // 从文件的2048字节之后开始写入数据  
          new FileWriteThread(1024*3,s3.getBytes()).start(); // 从文件的3072字节之后开始写入数据  
          new FileWriteThread(1024*4,s4.getBytes()).start(); // 从文件的4096字节之后开始写入数据  
          new FileWriteThread(1024*5,s5.getBytes()).start(); // 从文件的5120字节之后开始写入数据  
      }  
  
      // 利用线程在文件的指定位置写入指定数据  
      static class FileWriteThread extends Thread{  
          private int skip;  
          private byte[] content;  
  
          public FileWriteThread(int skip,byte[] content){  
              this.skip = skip;  
              this.content = content;  
          }  
  
          public void run(){  
              RandomAccessFile raf = null;  
              try {  
                  raf = new RandomAccessFile("D://abc.txt", "rw");  
                  raf.seek(skip);  
                  raf.write(content);  
              } catch (FileNotFoundException e) {  
                  e.printStackTrace();  
              } catch (IOException e) {  
                  // TODO Auto-generated catch block  
                  e.printStackTrace();  
              } finally {  
                  try {  
                      raf.close();  
                  } catch (Exception e) {  
                  }  
              }  
          }  
      }  
  
  }
  ```

- **当RandomAccessFile向指定文件中插入内容时，将会覆盖掉原有内容。如果不想覆盖掉，则需要将原有内容先读取出来，然后先把插入内容插入后再把原有内容追加到插入内容后。**

