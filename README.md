***记录日常学习打卡，持续更新***


### Java基础
- 线程安全
  - 线程安全的根本 (JVM内存模型) 
  
    线程安全的本质是由于可见行、有序性、原子性。可见性，JVM的内存模型粗略分为工作内存（线程私有）和主内存（线程公有），Java中实例化的变量都存放在堆中（可以对应为主内存），线程中的引用是主内存变量的拷贝，线程处理完逻辑后把变量结果同步回主内存，由此引发出变量同步的问题。有序性，JVM在为字节码的时候，会把没有逻辑关联的语句重新排序（就是跟写代码时候的顺序不同）。原子性，a=a+1，a+1不是原子性操作，a+1，然后赋值给a。
  - Volatile关键字原理
  
    Volatile修饰的变量，执行写操作后立即同步到主内存，同时线程中的缓存失效，线程发现缓存失效重新获取主内存中的值。Volatile通过"内存屏障"防止指令重排。
  - Synchronized关键字原理，如何优化耗时，Synchronized(String.class)会怎么样；

    在实例方法前加Synchronized关键字，锁住的是对象实例；
    在静态方法前加Synchronized关键字，锁住的是这个类；
    在方法内部加Synchronized关键字，锁住的是(xx)内的对象；
    Synchronized是可重入锁，以线程为单位的可重入。
    
  - 线程的状态
  
    [线程状态-博客园](https://www.cnblogs.com/aspirant/p/8876670.html)
    
    wait、notify是Object类拥有的方法，成对出现，wait会让出cpu和锁资源，线程进入阻塞，notify后 cpu唤醒对应锁资源的线程，重新进入可运行状态。
    
    sleep、yield是 Thread拥有的方法，sleep让出cpu资源，间隔时间后重新进入可运行状态。yield让出cpu资源，cpu重新选择线程进入可运行状态，也有可能选到原本yield的线程，yield的作用在于告诉cpu当前线程的工作快完成了。
    
    join，在当前线程中调用其他线程的join，当前线程阻塞，直到被调用join的线程执行完毕。
    
    
  - 线程安全的单例实现
    
    饿汉式（直接成员变量就new）、懒汉式（调用的时候才初始化）、双重锁 (指令重排可能导致不安全，new Object()不是原子操作)、静态内部类（Holder）
    
    JVM保证了类加载的时候是线程安全的，所以用static修饰类成员，达到了单例的线程安全
    
  - 可重入锁、不可重入锁
  
    可重入是指当前线程获得了锁资源，当前线程可以直接再次获取锁资源。ReentrantLock和Synchronized都是可重入锁，可重入锁避免单线程的死锁。
  - 线程池；

    核心线程也可以回收，allowCoreThreadTimeOut。工作原理，线程池接到一个任务，先判断核心线程数是否够，不够就直接新起一个核心线程来执行，否则核心线程都满了，则判断缓存队列是否满，不满就加入缓存队列，满了就判断maxPoolSize是否满，不满就新起线程来执行，满就根据拒绝策略处理。
    
    非核心线程在处理完任务之后，到了超时时间就销毁。AbortPolicy, 丢弃任务并抛出异常（默认）；DiscardPolicy，丢弃任务但不抛异常；DiscardOldestPolicy，丢弃队列最前面的任务，执行现在任务；CallerRunsPolicy，由调用线程池的线程处理任务。
  - 线程安全的集合及其原理；

    HashMap由数组、链表、红黑树三种数据结构组成，把key做哈希值运算，然后得到的数组的index，如果数组对应的位置为空，就插入，否则放到对应数组下标的链表中，链表长度为8，超过就变成红黑树。
    HashMap不安全的原因，在发生hash碰撞的时候，两个线程计算得到的哈希值一样，数值index一样，此时数组为空的话，则会发生两个线程对同一index值做修改。
    
    JDK1.8后ConcurrentHashMap由桶（数组）、链表、红黑树三种数据结构组成，采用 CAS(campare and swipe) 和 synchronize实现线程安全，锁的粒度是每个桶(即每个数组元素为单位)。CAS是乐观锁，默认没有锁，若遇到锁则一直循环等待。synchronize是悲观锁，默认有锁。

  
  - ***协程***

    - C#协程
    
    unity中C#实现的协程，是在游戏主线程中执行的函数，每一帧都判断协程中代码块是否完成，所以在unity的C#协程里没有并发执行的概念。C#的协程通过 IEnumerator 接口实现
    ```
    public interface IEnumerator{   
    object Current { get; } 
    bool MoveNext(); 
    void Reset(); }
    ```
    MoveNext true时协程里的代码继续往下走，false则跳出协程代码块，后面的代码不执行。yield封装了 IEnumerator的实现，内部实现了一个枚举类，通过MoveNext来遍历访问。
    
    - Kotlin协程

      [协程作用域及挂起原理](https://www.jianshu.com/p/a10a6082e91f)
      
      [协程的取消](https://medium.com/androiddevelopers/cancellation-in-coroutines-aa6b90163629)
      
      [协程异常处理](https://medium.com/androiddevelopers/exceptions-in-coroutines-ce8da1ec060c)
      
      
- JVM 原理
   
  Java虚拟机运行时数据区由方法区、堆、虚拟机栈、程序计数器、本地方法栈组成，其中方法区、堆是所有线程共享的，虚拟机栈、程序计数器、本地方法栈是每个线程独有的。
  - 程序计数器
 
    当前线程所执行的字节码的行号指示器，循环、跳转、异常处理、线程恢复等字节码指令都需要依赖程序计数器。
  - Java虚拟机栈

    与线程的生命周期一样，每个方法在线程执行时都会创建一个栈帧，用于存放局部变量表（包括基本数据类型boolean\byte\char\int\short\float\long）、方法出口等信息，一个方法从调用到完成，可以看作是在虚拟机栈中入栈到出栈的过程 (平时说的堆栈信息就是这么个意思)。
  - 本地方法栈

    作用与Java虚拟机栈类似，区别在于本地方法栈作用于Native方法。 
  - 堆

    Java堆是虚拟机所管理的内存中最大的一部分，用于存放所有的对象实例，所有的对象实例都在这里分配内存。
  - 方法区

    用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译后的代码（JIT） 。
    
 类加载的5个时机，1）遇到new\getstatic\putstatic\invokestatic字节指令时。2）使用java.lang.reflect方法进行反射调用时。3）当初始化一个类时，发现父类还没加载，则先加载父类。4）虚拟机启动时，包含在主函数中（main() ）的类。5）JDK1.7动态语言支持，如果一个java.lang.invoke.MethodHandle实例最后的解析结果 REF_getStatic\REF_putStatic\REF_invokeStatic方法句柄。
   
 
 强引用，如new Object()，只要引用还在就不会回收。软引用，SoftReference，用于描述一些还有用但并非必须的对象，在系统将要发生内存溢出之前，将会把这些对象列进回收范围之中进行第二次回收。弱引用，WeakReference，被弱引用关联的对象只能活到下一次垃圾收集之前。虚引用，虚引用关联的对象，完全不会对其生命周期构成影响，无法通过虚引用取得一个对象实例，为对象设置虚引用的唯一作用是能在这个对象被回收时收到一个系统通知。
 
 - 垃圾收集算法
   - 标记-清除  

     分为标记、清除两个阶段，缺点是会产生不连续的内存碎片，且标记和清除都不是高效率。
   - 复制
     
     把内存块分为两块，一块用完后，把存活的对象复制去另外一块，清理对应的内存块。效率高，代价是把内存块一分为二，减少了可分配的内存。
     
   - 标记-整理

     与标记-清楚不同在于，回收对象时，把存活的对象往一端移动，不会产生内存碎片。 
 
 CMS垃圾收集器（Concurrent Mark Sweep）是基于标记-清除算法实现，其分为初始标记、并发标记、重新标记、并发清除，其中初始标记、重新标记是Stop The World的处理，基于标记-清除缺点是会产生内存碎片，且CMS无法处理浮动垃圾（因为是并发清理）。
 
 G1垃圾收集器整体基于标记-整理算法实现，把Java堆按Region划分，先回收价值最大的Region，且其停顿时间可预测（因为按Region划分）。
 
 
- 插件化
  
  app bundle原理：
  
  基于Split Apks加载apk文件，通过AAPT2可以指定Resource的pp字段或固定资源id来解决资源冲突的问题，AssetsManager#addAssetPath把插件的资源路径加入。在8.0及以上版本DexClassLoader和PathClassLoader基本上一样，在8.0以前DexClassLoader的构造方法有一个optimizeDirectiry的参数，用于指定dex2oat的文件。
  
  [Qigsaw原理](https://blog.csdn.net/u014294681/article/details/116783861?spm=1001.2014.3001.5502)
  [Qigsaw自序原理](https://www.bookstack.cn/read/Qigsaw/spilt.3.spilt.2.c47ca9c7359b0d0d.md)
    
 
 Qigsaw本身具备热更新能力，配合Tinker，更新Qigsaw的配置json文件修改其中的插件版本号，达到热更效果。单类加载器、插件资源id的pp位从7f开始递减。

