# 一、Redis

## 缓存雪崩、缓存击穿、缓存穿透是什么，如何解决？

* #### 缓存雪崩：

  热点数据大面积同时失效。

  **解决方案：**

  * 将数据的失效时间加上一个随机值避免同时失效。

* #### 缓存击穿：

  某个超热点数据失效，大量请求直接打入数据库，将数据库打穿了。

  **解决方案：**

  * 设置热点数据永不失效。
  * 使用分布式锁，setnx 和 expire。

* #### 缓存穿透：

  大量无效数据直接打入数据库导致数据库崩了。

  **解决方案：**

  * 将空数据缓存起来，生存时间较短就行。

  * 加入一些校验，比如 id 小于0的直接返回。

  * **布隆过滤器：**

    利用多个 hash 函数进行散列，将对应位置的值置为 1，重叠没关系因为有多个 hash，因此会发生误判，判断出不在就一定不在，判断出在的话有一定几率是错误的。
  

## Redis 为什么是单线程的，为什么那么块？

* #### 为什么那么快：

  * 完全基于内存，绝代部分请求都是纯粹的内存操作
  * 数据结构比较简单，操作简单
  * 采用单线程，避免了不必要的线程切换的开销，不用考虑各种锁的问题，也不会有死锁
  * 使用的底层模型不同，Redis 自己构建了 VM 机制
  * **采用了多路 I/O 复用，非阻塞 I/O**

* #### 为什么是单线程的？

  因为 Redis 是基于内存的，速度很快，用多线程反而会增加开销，以及资源争夺问题。

## Redis 与 Memcached 区别？

* Redis 有持久化，Memcached 不能持久化，断电数据就消失
* Redis 数据类型更丰富一点，Memcached 只有简单的 String 类型
* 底层使用的模型不同，Redis 有自己的 VM 机制，因为调用系统函数的话会浪费一定的时间
* value 的值大小不同，Redis 最大可以达到 512M，Memcached 只有 1M
* Redis 比 Memcached 快

## Reids 的数据类型有哪些？

* ### 普通数据类型：

  * #### String：

    String 是 Redis 中最常用的数据类型，也是最简单的类型，就是普通的 K-V 对。

    ##### 使用场景：

    * 基础的缓存功能。
    * 计数器，通过 incr 和 decr（原子性的 命令进行计数。
    * 共享用户 Session，利用 Redis 将用户的 Session 集中缓存起来，key 为SessionID value 为用户信息。

  * #### Hash：

    Hash 实际上就是 String 类型的 field、value 映射表（Map）。

    ##### 使用场景：

    * 购物车，key 为用户 id，field 为商品 id，value 为数量。

  * #### List：

    List 是一个简单有序列表。

    ##### 使用场景：

    * 实现简单的消息队列，通过 lpush 和 Brpop 实现阻塞队列。
    * 存放一些列表行数据，如粉丝列表、关注列表等。
    * 通过 lrange 实现简单的分页查询。

  * #### Set：

    Set 是一个无序集合，自动去重复。可以实现并集、差集、交集等。

    ##### 使用场景：

    * 通过并集查找共同好友等。

  * ##### Sorted Set

    Sorted Set 就是排序了的 Set，去重排序，写进去一个 score 值进行自动排序。

    ##### 使用场景：

    * 排行榜：有序集合的经典使用场景，可以是多方面的：按时间、按数量、按播放量等等。
    * 可以做带权重的队列，如普通消息的 score 为1，重要消息的 score为2。
    

* ### 高级数据类型：
  * #### Bitmap：

    获得数据的 bit 值，可以用来实现布隆过滤器。

  * #### HyperLogLog：

    提供不精确的去重计数功能，比较适合用于大量数据的去重，例如统计 UV（Unique Visitors,独立访问者）。

  * #### Geospatial：

    可用用于保存地理位置，存放坐标，可以计算距离、经纬度。

## Redis 如何持久化？

Redis 的持久化分两种：

* ### RDB

  RDB 是 Redis 默认的持久化方式，按照一定周期将内存数据以快照的形式保存到硬盘中，用 fork() 一个子进程实现的。

  #### 特点：

  占用空间小（压缩过），恢复时速度快（快照），存储速度慢，数据安全性不好会丢失较多数据，开销大。

  适合于灾难的备份。

* ### AOF

  AOF 是将写操作追加到日志文件中，当 Redis 启动时执行一遍命令。有多种策略，支持每秒同步、修改同步、不同步等。

  #### 特点：

  占用空间大（采用的追加模式，越来越大），存储速度快，恢复速度慢，一般丢失一秒的数据，开销较小。

  AOF 和 RDB 同时开启，优先使用 AOF。

## Redis 的高可用

* ### 主从同步

  slave 先发送 psync2 命令请求同步，master 接收到后生成一个 RDB 快照文件，将 RDB 文件发给 slave，slave 进行一个全量复制，之后的同步通过 AOF（缓冲区和偏移量） 靠一条一条命令同步。

  心跳机制：master 通过 Ping 判断 slave 是否还存活，slave 回复偏移量以获取新的数据。
  
* ### 哨兵模式

  对于主从结构中的每一台机器进行监控，当出现故障时通过选举从新选出新的 master 并将所有 slave 从新连接到 master 上

  #### 作用：

  * 监控：不断地检查各个机器是否正常运行。
  * 通知：当一个哨兵发现某台机器出现异常，通知其他哨兵及客户端。
  * 自动故障转移：断开 master 与 slave 的连接，选举新的 master，将 slave 连接到新的 master 上，并告知客户端新的服务器地址。

## Redis 的过期策略和内存淘汰策略

* ### 过期策略

  Redis 采用的是**定期删除加惰性删除。**

  不使用定时删除的原因是定时删除用定时器负责监视每一个 key，过期则自动删除，虽然及时的清除了内存，但是十分消耗 CPU 资源。

  定期删除是每个一段时间（默认100ms）随机抽取检查看 key 是否过期，过期则删除。惰性删除是每次获取某个 key 时会先检查是否过期，过期则删除。

  还有许多 key 既没有被随机抽查到也没被访问过，如果内存达到一定大小时就会触发淘汰策略。

* ### 内存淘汰策略

  * no-enviction：禁止驱逐，不淘汰，新写入的话就报错。
  * volatile-lru：在已经设置过期时间的 key 中淘汰最近最久未使用的。
  * volatile-ttl：在已经设置过期时间的 key 中淘汰快要过期的。
  * volatile-random：在已经设置过期时间的 key 中随机淘汰。
  * allkeys-lru：从所有数据中淘汰最近最久未使用的。
  * allkeys-random：从所有数据中随机淘汰。

## 缓存与数据库数据一致性问题

如果对一致性要求不是太高的话就每次先删除缓存再更新数据库。但是在并发情况下会出现下面的情况：

A 线程要更新数据库，将缓存删除以后，B 线程来读取发现缓存中没有，再从数据库中读取出来旧数据放到缓存中，然后 A 线程才更新完成，导致数据不一致。

解决方法：

* “串行化”，将所有读写操作放到一个内存队列中，但是导致吞吐量大大降低。
* 根据数据的唯一表示（主键 id），让同一个数据的操作串行化，不同数据之间不需要串行，例如获得连接池时将 id 传过去。

## Hash扩容

即为 rehash 的过程，redis 使用的是渐进式 rehash，即非一次性、集中式完成，而是多次、渐进式完成。一个线程在扩容，其他线程进行并发读写。大致过程为ht[0]非扩容时存放数据的，ht[1]扩容时才会使用，长度为ht[0]的两倍。当扩容时，扩容线程把ht[0]的数据迁移到ht[1]，其他线程进行读操作时先从ht[0]中找，找不到再去ht[1]中找，写操作直接在ht[1]中写

## 跳表

**插入**

**插入 level = 1，key = 66**
当继续插入 level = 1，key = 66 时，结果如下

![image-20200811115355787](/Users/bytedance/笔记/笔记/跳表1.png)

**插入 level = 2，key = 100**
当继续插入 level = 2，key = 100 时，结果如下

![image-20200811115442367](/Users/bytedance/笔记/笔记/跳表2.png)

**查询**

从顶往下找，第一层为null后说明到达尾部则跳到第二层，依次遍历。例如找66，先66和1比较，大于1和5比较，大于5，5后面为null则往下一层。小于100再往下一层找到啦

# 二、Java 并发

## CAS 的 ABA 问题

cas（compare and swap）：仅当内存值 V 等于预期值 A 时，将 B 赋值给 V。

底层汇编中对应的指令（lock cmpxchg）

ABA 问题的解决：在值后面加一个版本号即可，每次更新版本号 + 1

## Object 对象在内存中的大小

* #### 对象在内存中的存储布局

![image-20200425181916836](C:\Users\杰\AppData\Roaming\Typora\typora-user-images\image-20200425181916836.png)

markword：存放关于锁（synchronized）的所有信息(8 字节)

类型指针：代表此对象是什么对象（4 字节）	markword + 类型指针 构成对象头（12 字节，图中错了）

实例数据：代表对象中的成员数据（**Object 没有实例数据**）

对齐：将字节数补成 8 的倍数，加快读取效率

* #### 因此 Object 对象的大小为 markword(8) + class pointer(4) + padding(4) = 16 bytes

![image-20200425181712335](C:\Users\杰\AppData\Roaming\Typora\typora-user-images\image-20200425181712335.png)

## synchronized 锁膨胀过程（全部记录在 markword 中）

**无锁状态、偏向锁状态、轻量级锁状态（CAS、自旋锁）、重量级锁状态**

**偏向锁：**第一个线程获得锁后，如果目前还没有其他线程来抢锁，此线程多次请求时可以直接获得锁。

**轻量级锁：**当有其他线程争抢锁时，升级为轻量级锁，自旋抢锁。

**重量级锁：**自旋一定时间后还是获得不到锁，就升级为重量级锁，向操作系统申请资源（重量），线程阻塞。

补充：

**锁清除：**当某个对象中的方法、变量不会被其他线程访问，JVM 会自动将锁取消掉。如下：

```java
public void add（String str1, String str2){
    StringBuffer sb = new StringBuffer();
    sb.append(str1).append(str2);	//append 方法中是加了锁的
}
```

**锁膨胀：**当连续的多个操作每次都加锁释放加锁释放，此时 JVM 会将锁膨胀到外部的整体只加一次，避免过多的开销。如下：

```java
public void test(String str){
    int i = 0;
    StringBuffer sb = new StringBuffer();
    while(i < 100){
        sb.append(str);		//频繁加锁释放，此时直接碰着到 while 循环，加一次锁即可
        i++;
    }
    return sb.toString();
}
```

## synchronized 的实现过程

**1.Java 代码层面：**synchronized 关键字

**2.字节码层面：** JVM 编译以后的字节码文件（class）中，进入synchronized 代码块时有一条指令 monitorenter，结束时指令为 monitorexit

**3.在执行过程中自动升级**

**4.操作系统中 lock comxchg**

## volatile 原理

![image-20200501161123611](C:\Users\杰\AppData\Roaming\Typora\typora-user-images\image-20200501161123611.png)

**CPU 在读取缓存时的最小单位时缓存行（cache line），相当于是预读一下，大小为64字节**

缓存一致性协议：CPU 会保证不同线程之间的缓存行内容相同，一个线程修改，其他线程就必须从新读取**（可见性）**

内存屏障：JVM 级别是屏障的两侧指令不能重排，通过 JVM 的一些指令来执行，有 LoadLoad 屏障（指的是两个 Load 指令之间的指令顺序不能换，也就是第一个 Load 执行读完了第二个 Load 指令才能读），还有 StoreStore 屏障、LoadStore 屏障、StoreLoad 屏障。**（禁止指令重排）**

## 双重检验加锁(Double Check Locking,DCL)的单例模式为什么要加 volatile？

```java
public class SingleClass {
 	
    private volatile static SingleClass instance;
    
    private SingleClass() {}
    
    public static SingleClass getInstance() {
        if(instance == null) {
            synchronized(SingleClass.class) {
                if(instance == null)
                    instance = new SingleClass();
            }
        }
        return instance;
    }
}
```

第二个 if 是为了防止第一次有多个线程进入第一个 if 语句，每个线程都创建一个

先补充一下对象创建的过程(三步)：instance = new SingleClass()

1. 先分配内存空间
2. 再通过构造方法初始化对象
3. 最后将 instance 指向对象，此时 instance != null

因此，如果不加 volatile 指令23 发生重排的话导致 instance 不为空但是却是半初始化的对象，另一个线程来了以后判断不为空直接返回的话就就会导致出错

## ReentrantLock 中公平与非公平锁的区别？

先看看非公平锁的实现

```java
static final class NonfairSync extends Sync {
    private static final long serialVersionUID = 7316153563782823691L;

    final void lock() {
        //已进入方法就先CAS尝试获得锁，如果能获得直接加锁
        if (compareAndSetState(0, 1))
            setExclusiveOwnerThread(Thread.currentThread());
        else
            acquire(1);	//拿不到锁以后acquire()最后还是会调用下面的tryAcquire()方法
    }

    protected final boolean tryAcquire(int acquires) {
        return nonfairTryAcquire(acquires);
    }
}

final boolean nonfairTryAcquire(int acquires) {
	final Thread current = Thread.currentThread();
	int c = getState();		//state表示锁的状态，为0则锁是空闲的，不为0则是被重入的次数
	if (c == 0) {
		if (compareAndSetState(0, acquires)) {	//再次CAS尝试加锁，获得则直接加锁
            setExclusiveOwnerThread(current);
            return true;
         }
    } 
    //可重入就体现在这里，如果是持锁的线程就是当前线程，则将state+1即可，返回true
    else if (current == getExclusiveOwnerThread()) {	
          int nextc = c + acquires;
          if (nextc < 0) // overflow
              throw new Error("Maximum lock count exceeded");
          setState(nextc);
          return true;
    }
    return false;
}
```

公平锁的实现：

```java
static final class FairSync extends Sync {
    private static final long serialVersionUID = -3000897897090466540L;

    final void lock() {
        acquire(1);
    }

    protected final boolean tryAcquire(int acquires) {
        final Thread current = Thread.currentThread();
        int c = getState();
        if (c == 0) {
            if (!hasQueuedPredecessors() &&		//公平锁会先判断队列是否为空，不为空就去排队
                compareAndSetState(0, acquires)) {
                setExclusiveOwnerThread(current);
                return true;
            }
        }
        else if (current == getExclusiveOwnerThread()) {
            int nextc = c + acquires;
            if (nextc < 0)
                throw new Error("Maximum lock count exceeded");
            setState(nextc);
            return true;
        }
        return false;
    }
}
```

因此公平锁就是严格按照顺序排队获取锁，而非公平锁通过两次CAS抢锁，因为可以抢到所以避免了部分进入队列后的阻塞与唤醒的操作，进而性能更加的好。

## Java 死锁

```java
public class Lock1 implements Runnable(){
    public void run(){
        synchronized(Main.l1){
            System.out.println("Lock1获得了第一个锁");
            try{
            	Thread.sleep(3000);
            }catch(InterruptedException e){
                e.printStackTrace();
            }
            synchronized(Main.l2){
                System.out.println("Lock1获得了第二个锁");
            }
        }
    }
}
public class Lock2 implements Runnable(){
    public void run(){
        synchronized(Main.l2){
            System.out.println("Lock2获得了第一个锁");
            try{
            	Thread.sleep(3000);
            }catch(InterruptedException e){
                e.printStackTrace();
            }
            synchronized(Main.l1){
                System.out.println("Lock2获得了第二个锁");
            }
        }
    }
}
public class Main{
    public static String l1 = "lock1";
    public static String l2 = "lock2";
    
    public static void main(String[] args){
        Thread t1 = new Thread(new Lock1());
        Thread t2 = new Thread(new Lock2());
        t1.start();
        t2.start();
    }
}
```

## 两个线程交替打印

```java
public class Test {
    public static String lock = "123";
    public static boolean flag = false;
    public static int i = 10;
    public static int j = 10;

    public static void main(String[] args) {
        new Thread(() -> {
            while (i > 0) {
                synchronized (lock) {
                    if (flag) {
                        try {
                            lock.wait();
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    } else {
                        System.out.println("thread1:hello  " + i--);
                        flag = !flag;
                        lock.notifyAll();
                    }
                }
            }
        }).start();
        new Thread(() -> {
            while (j > 0) {
                synchronized (lock) {
                    if (!flag) {
                        try {
                            lock.wait();
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    } else {
                        System.out.println("thread2:hello  " + j--);
                        flag = !flag;
                        lock.notifyAll();
                    }
                }
            }
        }).start();
    }
}
```



# 三、Java 基础

## String、StringBuffer、StringBuilder的区别？

String 中是用 final 修饰的 char 数组，因此可以看作是常量不可变的，也是线程安全的，但是拼接效率较低

StringBuffer 和 StringBuilder 都继承自 AbstractStringBuilder char 数组都没有被 final 修饰，因此都是可变的。StringBuffer 中对方法加上了 synchronized 关键字因此是线程安全的，而 StringBuilder 是线程不安全的

## 接口与抽象类的区别？

抽象类不能被实例化只能被继承，可以有非抽象方法

接口的成员必须是 public 的，并且不允许被定义为 private 和 protected，接口的字段默认都是 static 和 final 的

**比较：**

* 一个类只能继承一个抽象类，而可以实现多个接口
* 抽象类中的可以使用 protected 修饰，抽象类中的变量也不一定是 static 和 final 的
* 从设计层面来说抽象类更像是一个模板，而接口是对一个行为的抽象，是一种规范

## final 关键字

* 修饰变量：如果是基础变量则其值不能被更改，如果是引用类型的变量则其引用的值（只能引用一个对象）不能改变，但是引用的对象的内部可以发生改变
* 修饰类：被 final 修饰的类不能被继承
* 修饰方法：被 final 修饰的方法不能被子类重写，private 隐式的被指定为 final，如果子类定义了一个与父类 private 修饰的方法一样，此时不是重写了父类的方法，而是创建了一个新的方法

## try catch finally 的执行顺序？

在编译时就把 finally 加到 try 和 catch 后面，如果 try 和 catch 中有 return 语句，则将 return 语句的表达式执行完(如i++)在把 finally 执行完再 return

# 四、场景题

## 海量数据排序，如何操作（8G 的文件，内存只有 2G）？

* 可通过分治思想，将文件分成很多区间，利用快排将每个区间排好序再利用归并排序将各个区间合并

* 通过堆排序进行

* 利用位图排序，建立一个 比最大值还大一长的数组，将每个数字当作数组下标对应位置置一

  如：{1，2，7，10，15}，则创建一个 16 长的数组 {0，1，1，0，0，0，0，1，0，0，1，0，0，0，0，1}倒序输出数组值为 1 的下标即可

## 如何排查 OOM？

* 首先先查看堆内存是否分配的合理
* 查看是否有大量资源被频繁申请但是没有被释放，导致泄露（如大量连接未关闭）
* 查看某些资源一直被申请导致系统资源被耗尽（如递归中栈内存溢出）
* 通过 jmap、Java VisualVM 等工具进行排查

## 如何防止超卖？

* 利用 CAS 操作加个版本号，缺点就是 CPU 压力太大
* 悲观锁，平均响应时间太长，并发太高导致可用连接用完出现异常
* 利用队列串行化操作，到达阈值就关闭，缺点还是性能不好
* 将数据全部放到 redis 中，利用原子自增自减进行操作

## 死锁怎么排查？

利用 jps、jstack 等工具进行排查



## 内存溢出和内存泄漏的区别？

内存泄漏：Java 中所有对象都是以 GC Root 为起点的引用链，当某个对象脱离时会被回收。内存泄漏就是一个长周期的对象引用了一个短周期的对象，只要长周期的对象不被回收则短周期的对象永远不会被回收，因此这个短周期的对象就泄漏了

内存溢出：内存溢出就是内存越界，数据的大小超过了指定空间的大小，递归过深内存不足会导致内存溢出





# 五、框架

## Spring 中 @Autowired 和 @Resource 的区别？

Autowired 是按照类型匹配，如果想按名字匹配可以结合 @Qualifier 一起使用

Resource 默认是按照名字匹配，也可以按照类型匹配

Autowired 是由 Spring 提供的，而 Resource 是由 J2EE提供的

## Spring 中拦截器和过滤器的区别

* 拦截器不依赖于 servlet，过滤器依赖于 servlet 容器
* 拦截器是基于反射机制的，而过滤器是基于函数回调
* 拦截器可多次调用，而过滤器只在 servlet 初始化时被调一次

## 如何模拟 SpringMVC 框架的执行？

将 http 请求的各部分信息进行拆分封装，进行不同的处理，（如：找到对应的 Controller ）

# 六、中间件

## HTTP和RPC的区别？

- HTTP 基于 HTTP 协议，RPC 基于 TCP协议

- RPC 请求报文更小，HTTP 请求中会包含很多无用的内容，效率没有 RPC 高

- RPC 基于 thrift 实现高效的二进制传输，而 HTTP 大部分是通过 json 来实现，字节大小和序列化都比 thrift 更消耗性能

- RPC 能做到自动通知，不影响上游，HTTP 需要事先通知

    对于大型企业来说，内部子系统较多、接口较多的情况下，RPC 的有点很明显，不需要像 HTTP 每次通信都需要三次握手等，其次RPC 框架一般都会有注册中心，有丰富的监控管理机制，发布、动态扩展等对于调用放来说是无感知的



# 补充

## HTTP 1.0 1.1 2.0 的区别？

1.0 和 1.1 区别主要是 1.1 支持长连接

1.1 与 2.0 区别主要是 2.0 支持了多路复用可以做到同一个连接并发处理多个请求，2.0 还进行了对 header 数据压缩

## 输入域名后长时间未响应的原因有哪些？

* 可能是服务端正在故障维修
* 网络连接不正常
* DNS 解析域名时间较长
* 所访问的页面可能内容太多了

## 进程间的通信方式有哪些？

* 管道：分为匿名管道和命名管道，匿名管道只能父子进程间通过 fork() 来进行通信，而命名管道可以在没有血缘关系的进程之间通信。面向字节流且是半双工的
* 消息队列：在内核中创建一个队列，队列中的每一个元素都是数据报，不同的进程访问队列来取出数据。消息队列可以看作是一个全局的链表，可以实现双向通信。
* 信号量：通过PV操作进行通信
* 共享内存：将同一块物理内存映射到不同进程的虚拟地址空间中，实现不同进程对同一资源的共享，速度最快

## 多线程与多进程

* 进程占用内存多，切换起来比较复杂，而线程切换简单，但是一个线程出错可能会导致整个进程崩溃
* 进程的数据是分开的，进程间的通信较为复杂，多个线程共享进程的数据，但是各个线程又有自己独立的空间
* 进程就好比一条河，而线程就是一条的一条条分支

## 进程调度算法有哪些？

* 先来先服务
* 短作业优先
* 优先权调度（抢占、非抢占）
* 时间片轮转

## 四种引用类型的使用场景？

* 强引用：new 出来的都是就不解释了
* 软引用：一般用于缓存相关的场景，如缓存图片，内存足够则正常使用，不足时回收避免 OOM 发生
* 弱引用：例如一个对象被容器引用，如 HashMap，再把对象赋值为 null 的话因为 HashMap 还引用着所以不会被回收，因此只需要将此对象交给弱引用即可
* 虚引用：一般和引用队列一起使用，用于跟踪对象被回收后的信息

## 线程的状态有哪些？

* 新建状态：刚 new Thread() ,一直保持到 start() 方法
* 就绪状态：调用完 start() 以后进入就绪状态，处于一个就绪队列中等待 JVM 调用
* 运行状态：就绪状态的线程获得 CPU 资源后进入运行状态
* 阻塞状态：
  * 等待阻塞：线程调用 wait() 方法后进入等待阻塞状态
  * 同步阻塞：遇到 synchronized 获得锁失败进入同步阻塞状态
  * 其他阻塞：调用 sleep() 方法或者 join() 发生了 I/O 请求等情况
* 死亡状态：线程的任务执行完或者其他中断条件发生

## Java 字节码中都有哪些信息？

* class 文件信息（版本号等）
* 常量池信息
* 对象信息
* JVM 指令等

## 红黑树

性质：

* 节点不是黑色就是红色
* 根节点一定是黑色
* 叶子节点都是黑的，每个叶子节点都是null
* 红节点的子节点一定是黑的
* 根节点到任意一个叶子节点的路径中黑节点个数相同

插入平衡：

* 令所有插入的节点是红色

* 不需要平衡：
  * 如果是根节点直接插入并染黑
  * 如果其父节点是黑色，则保持不变

* 需要平衡（默认父节点是祖父的左子）：

  * 父节点是红色且叔叔节点是红色

    把祖父节点变成红色，父节点和叔叔节点变成黑色，**并将当前节点指向祖父节点，进行再次判断**

  * 父节点是红色，叔叔节点是黑色，当前节点是父节点的右节点

    **将当前节点变为父节点**，将旧的当前节点进行左旋

  * 父节点是红色，叔叔节点是黑色，当前节点是父节点的左节点

    将父节点染黑，祖父节点染红，将父节点右旋，再将根节点染黑即可

```java
public static int help(String str){
        Stack<String> stack = new Stack<>();
        int result = 0;
        for(int i = 0, cur = 0; cur < str.length(); cur++) {
            if(str.charAt(cur) >= '0' && str.charAt(cur) <= '9'){
                continue;
            }else if(str.charAt(cur) == '+' || str.charAt(cur) == '-'){
                stack.push(str.substring(i, cur));
                i = cur;
                stack.push(str.charAt(cur) + "");
            }else if(str.charAt(cur) == '*'){
                int t = Integer.valueOf(str.substring(i, cur));
                i = cur + 1;
                while(str.charAt(cur) >= '0' && str.charAt(cur) <= '9'){
                    cur++;
                }
                t *= Integer.valueOf(str.substring(i, cur));
                stack.push(t + "");
                i = cur;
            }else if(str.charAt(cur) == '/'){
                int t = Integer.valueOf(str.substring(i, cur));
                i = cur + 1;
                while(str.charAt(cur) >= '0' && str.charAt(cur) <= '9'){
                    cur++;
                }
                t /= Integer.valueOf(str.substring(i, cur));
                stack.push(t + "");
                i = cur;
            }
        }
        while(!stack.isEmpty()){
            int temp = Integer.valueOf(stack.pop());
            String s = stack.pop();
            int temp1 = Integer.valueOf(stack.pop());
            if(s.equals("+")){
                result += temp + temp1;
            }else{
                result += temp1 - temp;
            }
        }
        return result;
    }
```

