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
    
  - 线程安全的单例实现；
  - 可重入锁、不可重入锁；
  - 线程池；
  - 线程安全的集合及其原理；
