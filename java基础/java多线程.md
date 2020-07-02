## **线程基础知识**

### 前沿介绍

- **关于线程池关联博客有：**
- [多线程1，线程基础知识](http://www.jcodecraeer.com/plus/view.php?aid=9597)
- [多线程2，线程池深入理解](http://www.jcodecraeer.com/plus/view.php?aid=9604)
- [多线程3，线程池封装库](https://blog.csdn.net/m0_37700275/article/details/79979097)
- 如果觉得前两篇线程知识太基础，可以直接忽略……主要是回顾基础知识点！

### 进程概述及多进程的意义

#### 1.1 线程和进程

- 要想说线程，首先必须得聊聊进程，因为线程是依赖于进程存在的。

#### 1.2 进程概述

- 什么是进程呢?通过任务管理器我们就可以看到进程的存在。
- 概念：进程就是正在运行的程序，是系统进行资源分配和调用的独立单位。每一个进程都有它自己的内存空间和系统资源。

#### 1.3 多进程的意义

- 单进程计算机只能做一件事情。而我们现在的计算机都可以一边玩游戏(游戏进程),一边听音乐(音乐进程)，所以我们常见的操作系统都是多进程操作系统。比如：Windows，Mac和Linux等，能在同一个时间段内执行多个任务。
- 对于单核计算机来讲，游戏进程和音乐进程是同时运行的吗?不是。
- 因为CPU在某个时间点上只能做一件事情，计算机是在游戏进程和音乐进程间做着频繁切换，且切换速度很快，
- 所以，我们感觉游戏和音乐在同时进行，其实并不是同时执行的。多进程的作用不是提高执行速度，而是提高CPU的使用率。

### 线程的概述和多线程的意义

#### 2.1 什么是线程

- 在一个进程内部又可以执行多个任务，而这每一个任务我们就可以看成是一个线程。是程序使用CPU的基本单位。

#### 2.2 多线程有什么意义

- 多线程的作用不是提高执行速度，而是为了提高应用程序的使用率。
- 那么怎么理解这个问题呢?我们程序在运行的使用,都是在抢CPU的时间片(执行权),如果是多线程的程序,那么在抢到CPU的执行权的概率应该比较单线程程序抢到的概率要大.那么也就是说,CPU在多线程程序中执行的时间要比单线程多,所以就提高了程序的使用率.但是即使是多线程程序,那么他们中的哪个线程能抢占到CPU的资源呢,这个是不确定的,所以多线程具有随机性.

#### 2.3 并行和并发

- 前者是逻辑上同时发生，指在某一个时间内同时运行多个程序。

- 后者是物理上同时发生，指在某一个时间点同时运行多个程序。

  

### JVM运行原理以及JVM启动的线程探讨

#### 3.1 Java程序运行原理

- Java命令会启动java虚拟机，启动JVM，等于启动了一个应用程序，也就是启动了一个进程。
- 该进程会自动启动一个 “主线程” ，然后主线程去调用某个类的 main 方法。所以 main方法运行在主线程中。

#### 3.2 JVM的启动是多线程的吗

- JVM启动至少启动了垃圾回收线程和主线程，所以是多线程的。



## **Thread创建详解**





### 继承Thread类创建线程类

- 创建步骤如下

  - 定义Thread类的子类，并重写该类的run方法，该run方法的方法体就代表了线程要完成的任务。因此把run()方法称为执行体。
  - 创建Thread子类的实例，即创建了线程对象。
  - 调用线程对象的start()方法来启动该线程。

- 代码如下所示

  ```
  第一种方式的步骤:
  1: 定义一个类,让该类去继承Thread类
  2: 重写run方法
  3: 创建该类的对象
  4: 启动线程
  
  public class ThreadDemo {
  	public static void main(String[] args) {
  		// 创建对象
  		MyThread t1 = new MyThread() ;
  		MyThread t2 = new MyThread() ;
  		// 启动线程: 需要使用start方法启动线程, 如果我们在这里调用的是run方法,那么我们只是把该方法作为普通方法进行执行
  //		t1.run() ;
  //		t1.run() ;
  		t1.start() ;		// 告诉jvm开启一个线程调用run方法
  		// t1.start() ;		// 一个线程只能被启动一次
  		t2.start() ;
  		
  	}
  }
  
  public class MyThread extends Thread {
  	@Override
  	public void run() {
  		for(int x = 0 ; x < 1000 ; x++) {
  			System.out.println(x);
  		}
  	}
  }
  ```

#### 通过Runnable接口创建线程类

- 创建步骤如下

  - 定义runnable接口的实现类，并重写该接口的run()方法，该run()方法的方法体同样是该线程的线程执行体。
  - 创建 Runnable实现类的实例，并依此实例作为Thread的target来创建Thread对象，该Thread对象才是真正的线程对象。
  - 调用线程对象的start()方法来启动该线程。

- 代码如下所示

  ```
  实现多线程的第二中方式步骤:
  1: 定义一个类,让该类去实现Runnable接口
  2: 重写run方法
  3: 创建定义的类的对象
  4: 创建Thread的对象吧第三步创建的对象作为参数传递进来
  5: 启动线程
  
  public static void main(String[] args) {
  	// 创建定义的类的对象
  	MyThread mt = new MyThread() ;
  	// 创建Thread的对象吧第三步创建的对象作为参数传递进来
  	Thread t1 = new Thread(mt , "张三") ;
  	Thread t2 = new Thread(mt , "李四") ;
  	// 启动线程
  	t1.start() ;
  	t2.start() ;
  }
  
  public class MyThread implements Runnable {
  	@Override
  	public void run() {
  		for(int x = 0 ; x < 1000 ; x++) {
  			System.out.println(Thread.currentThread().getName() + "---" + x);
  		}
  		
  	}
  }
  ```

#### 通过Callable和Future创建线程

- Callable基础介绍

  - Runnable 从 JDK1.0 开始就有了，Callable 是在 JDK1.5 增加的。它们的主要区别是 Callable 的 call() 方法可以返回值和抛出异常，而 Runnable 的 run() 方法没有这些功能。Callable 可以返回装载有计算结果的 Future 对象。

  - 通过对比两个接口得到这样的结论

    - Callable 接口下的方法是 call()，Runnable 接口的方法是 run()；
    - Callable 的任务执行后可返回值，而 Runnable 的任务是不能返回值的；
    - call() 方法可以抛出异常，run()方法不可以的；
    - 运行 Callable 任务可以拿到一个 Future 对象，表示异步计算的结果。它提供了检查计算是否完成的方法，以等待计算的完成，并检索计算的结果。通过 Future 对象可以了解任务执行情况，可取消任务的执行，还可获取执行结果；

    ```
    public interface Runnable {
        public void run();
    }
    
    public interface Callable<V> {
        V call() throws Exception;
    }
    ```

- 创建步骤如下所示

  - 创建Callable接口的实现类，并实现call()方法，该call()方法将作为线程执行体，并且有返回值。
  - 创建Callable实现类的实例，使用FutureTask类来包装Callable对象，该FutureTask对象封装了该Callable对象的call()方法的返回值。
  - 使用FutureTask对象作为Thread对象的target创建并启动新线程。
  - 调用FutureTask对象的get()方法来获得子线程执行结束后的返回值，调用get()方法会阻塞线程。

- 代码如下所示

  ```
  public class CallableThread implements Callable<Integer> {
      @Override
      public Integer call() throws Exception {
          int i = 0;
          for (; i < 100; i++) {
              System.out.println(Thread.currentThread().getName() + " " + i);
          }
          return i;
      }
  }
  
  
  public class ThreadDemo {
      public static void main(String[] args) {
          CallableThread ctt = new CallableThread();
          FutureTask<Integer> ft = new FutureTask<>(ctt);
          for (int i = 0; i < 100; i++) {
              System.out.println(Thread.currentThread().getName() + " 的循环变量i的值" + i);
              if (i == 20) {
                  new Thread(ft, "有返回值的线程").start();
              }
          }
          try {
              System.out.println("子线程的返回值：" + ft.get());
          } catch (InterruptedException e) {
              e.printStackTrace();
          } catch (ExecutionException e) {
              e.printStackTrace();
          }
      }
  }
  ```

### 三种创建线程区别

- 采用实现Runnable、Callable接口的方式创见多线程时
  - 优势是：
    - 线程类只是实现了Runnable接口或Callable接口，还可以继承其他类。
    - 在这种方式下，多个线程可以共享同一个target对象，所以非常适合多个相同线程来处理同一份资源的情况，从而可以将CPU、代码和数据分开，形成清晰的模型，较好地体现了面向对象的思想。
  - 劣势是：
    - 编程稍微复杂，如果要访问当前线程，则必须使用Thread.currentThread()方法。
- 使用继承Thread类的方式创建多线程时
  - 优势是：
    - 编写简单，如果需要访问当前线程，则无需使用Thread.currentThread()方法，直接使用this即可获得当前线程。
  - 劣势是：
    - 线程类已经继承了Thread类，所以不能再继承其他父类。



























