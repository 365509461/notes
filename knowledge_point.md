# 查漏补缺：

## （一）什么是面向对象

* ### 三大特性：

  * #### 封装

    利用抽象数据类型将数据和基于数据的操作封装在一起，使其构成一个不可分割的实体。数据被隐藏在内部，尽可能的隐藏细节，只保留一些对外的接口与外部发生联系。用户无需关心内部的细节。

    优点：

    * 减少耦合：独立的测试、开发、优化、修改等。
    * 减轻维护负担：调试的时候不影响其他模块。
    * 提高了可重用性。

  * #### 继承

    继承就是使用已存在的类作为基础建立新的类，新类可以定义新的数据和方法，也可以使用父类的。

  * #### 多态

    多态分为编译时多态和运行时多态：

    * 编译时多态一般指方法的重载。
    * 运行时多态在运行期间才能确定。

    运行时多态的三个条件：

    * 继承
    * 重写（覆盖）
    * 向上转型

    

## （二）JavaWeb

* ### 什么是 Servlet？

  Servlet 是一个用 Java 实现的运行在服务器端的程序，用于处理客户端请求和响应给浏览器的动态资源。
  
* ### doGet() 与 doPost() 区别？

  1. get 请求会把参数加到 url 后面，不安全。post 把参数放到请求体中。
  2. get 有大小限制（1k），post 没有限制。

* ### request 的流程

  ![image-20200511155343575](C:\Users\杰\AppData\Roaming\Typora\typora-user-images\image-20200511155343575.png)

* ### JSP 中四大域对象

  * #### page

    仅在一个页面有效，放置一个页面相关的对象和属性。

  * #### request

    在一个 request 请求链中有效，放置请求相关的对象和属性。

  * #### session

    在一次会话中有效，放置用户与服务器建立一次会话需要的对象和属性。

  * #### application

    在整个 web 应用中有效，包括多个页面、请求、会话的一个全局作用于。

* ### 转发（Forward）与重定向（Redirect）的区别

  * 转发是服务器行为，重定向是客户端行为。
  * 转发的 url 不发生变化（一次请求，速度快），重定向发生变化（重定向是两次请求）。
  * 转发的 request 域中的数据还在，重定向不能共享数据。
  * 转发只能在服务器内部，重定向没有限制。

## （三）Java 8 新特性

* **Lambda 表达式**

  * Lambda 是一个匿名函数（方法），简化匿名内部类的书写

    ```java
    public static void main (String[] args){
        new Thread(new Runnable(){
           @Override
            public void run(){
                System.out.println("正在执行");
            }
        });
    }
    Lambda：
    public static void main (String[] args){
        new Thread(() -> {
            System.out.println("正在执行");
        });
    }
    省略：
    public static void main (String[] args){
        new Thread(() -> System.out.println("正在执行")
        );
    }
    常用：
    	Collections.sort(list, (o1, o2) -> (o1 - o2));
    ```

  * 省略规则：

    * 小括号里的参数类型可以省略
    * 如果小括号中有且只有一个参数小括号也可以省
    * 如果大括号中有且只有一个语句，可以同时省略大括号、return、分号

  * Lambda 表达式的使用前提条件：

    * 方法的参数或局部变量类型必须为接口才可以使用
    * 接口中有且只有一个抽象方法（只有一个方法的接口称为函数式接口）

  * Lambda 表达式中的方法引用，如果要实现的接口中的方法已经有线程的实现，则用类名 :: 方法名即可快速实现（相当于把已有的方法赋值给接口中的方法）

    * 实例对象 :: 方法名
    * 类名 :: 静态方法名
    * 类名 :: 普通方法名
    * 类名 :: new （相当于调用构造函数）
    * 注意：已有方法和接口中的方法参数和返回值需相同

  * 与匿名内部类的区别：

    * 匿名内部类可以是类、抽象类、接口，Lambda 必须是接口
    * 匿名内部类中的抽象方法随意，Lambda 只能有一个
    * 匿名内部类在编译后会生成 class 文件，Lambda 在程序运行时动态生成

* **集合的 Stream 流操作**：

  * Stream 是流式思想，相当于工厂的流水线，对数据进行加工处理

  * 代码：

    ```java
    public static void main(String[] args) {
        ArrayList<String> list = new ArrayList<>();
        Collections.addAll(list, "刘德华", "张一山", "成龙", "张国荣", "郭富城", "张杰");
        //拿到所有姓张的
        ArrayList<String> zhang = new ArrayList<>();
        for (String s : list) {
            if (s.startsWith("张"))
                zhang.add(s);
        }
        //拿到名字长度为三的
        ArrayList<String> three = new ArrayList<>();
        for (String s : zhang) {
            if (s.length() == 3)
                three.add(s);
        }
        for (String s : three) {
            System.out.print(s + "  ");
        }
        System.out.println();
        //太繁琐  因此引入 Stream 操作
        System.out.println("-------------------");
        list.stream().filter((s) -> {
            return s.startsWith("张");
        }).filter((s) -> {
            return s.length() == 3;
        }).forEach((s) -> System.out.print(s + "  "));
    }
    ```

  * 获取 Stream 流的两种方法

    ```java
    //第一种
    list.stream();
    set.stream();
    map.keySet().stream();
    //第二种
    Stream.of("abc", "bcd", "qwe");
    String[] strs = {"abc", "bcd", "qwe"};
    Stream.of(strs);
    //注意不要使用基础类型的数组，Stream 流会将整个数组当作一个元素来处理
    ```

  * Stream 中的常用方法

    * 终结方法，即返回值不是 Stream 的方法，如 forEach() count() 等
    * 拼接方法，返回值还是 Stream，filter() limit() 等
    * 如果最后不调终结方法进行输出或者赋值，则拼接方法不会执行
    * Stream 只能操作一次，如果是拼接方法返回的 Stream 是一个新流，因此可以再次操作

  * 常用方法演示

    ```java
    public static void main(String[] args) {
        ArrayList<String> list = new ArrayList<>();
        Collections.addAll(list, "刘德华", "张一山", "成龙", "张国荣", "郭富城", "张杰");
    
        //forEach() 有一个参数（接口） 无返回值 作用是遍历集合
        list.stream().forEach(System.out::println);
        System.out.println("-------------------------");
      //count()  无参数 返回值为long 作用是返回集合中的元素个数
        System.out.println(list.stream().count());
        System.out.println("-------------------------");
        //filter() 有一个参数（接口） 返回值为Stream 接口返回值为boolean 返回值不是true则这个元素就不要
        list.stream().filter((s) -> s.length() == 3).forEach(System.out::println);
        System.out.println("-------------------------");
        //limit() 有一个参数long 返回值是Stream  作用是只返回前几个元素
        list.stream().limit(3).forEach(System.out::println);
        System.out.println("-------------------------");
        //skip() 有一个参数long 返回值是Stream  作用是跳过前几个元素
        list.stream().skip(3).forEach(System.out::println);
        System.out.println("-------------------------");
        //map() 有个参数（接口）返回值为Stream 接受一个参数返回一个参数 作用是将T类型的流变成R类型的流
        Stream<String> stream = list.stream();
        //因为有中文所以会报错
        Stream<Integer> integerStream = stream.map((s) -> Integer.parseInt(s)); 
        System.out.println("-------------------------");
        //distinct() 去重复
        list.stream().distinct().forEach(System.out::println);
        System.out.println("-------------------------");
        //max() min() 找到最大值最小值 需要传入Comparator
        System.out.println(list.stream().max(Comparator.naturalOrder()).get());
        System.out.println("-------------------------");
        //reduce() 第一个参数是初始值 第二个参数是接口 两个参数一个返回值
        //求和
        System.out.println(Stream.of("11", "22", "33", "12").map(Integer::parseInt).reduce(0, Integer::sum));
        //最大值
        System.out.println(Stream.of("11", "22", "33", "12").map(Integer::parseInt).reduce(0, Integer::max));
        System.out.println("-------------------------");
    }
    
    ```
    
    

* **接口类型的改变**

  * 接口中新添加了默认方法：
    * 接口中可以有默认方法，默认方法可以直接实现
    * 可以有静态方法了

## (四) IO

BIO：阻塞IO，客户端和服务端在等待对方数据到来时阻塞，流程：

- 等待数据：当进程A获得CPU资源并执行到socket生成一个socket并进行recv()时，操作系统将线程A移至等待队列，A进程被阻塞不占用CPU资源
- 数据来了：数据经过网卡传进内存，网卡驱动中断信号，CPU作出响应，执行中断程序，socket接收数据
- 唤醒进程：socket收到数据后，操作系统将该socket的等待队列中的进程A唤醒，进程回到工作队列中继续执行代码

select：轮询式(多路复用下的一个模块)，即用一个数组放置所有需要监视的socket，等待数据时进程挂起，任意一个socket等到数据后，进程被唤醒。缺点是需要多次遍历数据（O(n)）

poll：和select基本一致，将数据变为链表，因此没有了连接数量的限制了

**epoll：**

SELECT开销大的原因有两个：

- 维护等待队列和阻塞进程的关系

    解决方式：将维护等待队列和阻塞进程分离开，在epoll方法中会创建一个eventpoll对象，将需要监视的socket放进其中

- 需要遍历才能知道那个socket收到数据

    解决方式：维护一个list，list引用收到数据的socket

epoll是linux系统提供的多路复用模块，提供了三个函数调用，分别是epoll_create创建一个红黑树(eventpoll)和映射表，epoll_ctl将需要监视的socket放入其中，epoll_wait若红黑树中有引用则返回，没有则阻塞



## 一、集合

###  （一）List

* ArrayList：
  * 底层是可变数组，查询快，插入删除慢（扩容1.5倍）
  * add方法完成了三件事，先确保能添加到集合中（是否扩容），添加到指定位置，size+1
  * 扩容扩大1.5倍（oldCapacity + (oldCapacity >> 1)）
  * 线程不安全
* LinkedList：
  * 底层是链表实现，插入删除块，查询慢
  * 线程不安全
* Vector：
  * 底层是数组，查询快，插入删除慢
  * 线程安全

###  （二）Set

* HashSet：
  * 不允许重复元素
  * 基于HashMap实现的，底层哈希表
  * 查询快
* LinkedHashSet：
  * 不允许重复元素
  * 底层是链表加哈希表
  * 由链表保证有序，哈希表保证唯一
* TreeSet：
  * 底层是红黑树（基于 TreeMap）
  * 唯一、有序，不允许放入null
  * 可以通过比较器改变如何排序

###  （三）Map

* TreeMap：
  * 有序
  * **底层是红黑树**
* HashTable：
  * 线程安全（synchronized）

###  （四）HashMap

* get() 方法：
  * 调用get方法时先调用hash函数，此函数会将key的hashCode值返回，返回的hashCode与Entry数组长度-1进行逻辑与（&）运算得到一个index值来确定数据存储在Entry数组当中的位置  
  * 通过循环遍历索引处的链表，初始值为数据存储在Entry数组当中的位置，循环条件为Entry对象不为null 
  * 如果hash函数得到的hash值与Entry对象中的key的hash值相等，并且Entry对象中的key值与get方法传进来的key值equals相同则返回该Entry对象的value值，否则返回null
* put() 方法：
  * 调用hash函数获得hashcode值
  * 和数组长度-1逻辑与得到index值
  * 遍历对应的链表，如果Entry对象的hash与hash函数得到的hash值相等，并且该Entry对象的key值与put传过来的值相等，则将把oldvalue保存返回，新值赋值
  * 添加entry对象到相应索引处
* 底层结构：数组+链表+红黑树
* 初始容量为16（扩容因子0.75），即到12就扩容，扩容两倍（newThr = oldThr << 1）
* 1.7和1.8的区别
  * 1.7扩容时因为是头插法（倒序），多线程情况下容易链表成环造成死锁，1.8采用尾插法不会造成死锁,代码如下

```java
Node<K,V> loHead = null, loTail = null  //低位头尾指针
Node<K,V> hiHead = null, hiTail = null;	//高位头尾指针
//并进行循环e.hash & oldCap操作（要么为0要么为16）
if ((e.hash & oldCap) == 0) { 	//等于0，则放在低位链表上
   	 if (loTail == null)
      		  loHead = e;
  	 else
     	   loTail.next = e;
   	 loTail = e;
}
else {				//其他，则放在高位链表上
    if (hiTail == null)
        hiHead = e;
    else
        hiTail.next = e;
    hiTail = e;
}
//循环结束，高低位链表准备好了，loHead指向低位链表，hiHead指向高位链表 链表没有倒置
if (loTail != null) {		//将低位链表放入扩容后的数组中j位置（原位置）
    loTail.next = null;
    newTab[j] = loHead;
}
if (hiTail != null) {	//将高位链表放入扩容后链表的数组中后一半的j位置
    hiTail.next = null;
    newTab[j + oldCap] = hiHead;

```

* 为什么hashmap的数组长度必须是2的倍数？
  * 因为index=hashcode&(length-1)，只有为二的倍数length-1为全1，下标才符合，**均匀分布，减少hash碰撞，扩容也是两倍**
  * 位运算速度比取模快得多
* 怎么保证容量是2的倍数？

```java
//第一次把左边两位变为1，再把左边四位变为1...最后全为1，最后再加一返回
static final int tableSizeFor(int cap) {
        int n = cap - 1;
        n |= n >>> 1;
        n |= n >>> 2;
        n |= n >>> 4;
        n |= n >>> 8;
        n |= n >>> 16;
        return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
}
```

* 链表转红黑树的阈值为什么是8？
  * 因为hash冲突时，在链表长度为8时的几率为亿分之六，但是自己重写hash函数时容易hash碰撞
* 为什么要使用红黑树？
  * 因为小于8的时候两者性能差不多，转化为红黑树还需要费一些时间与空间，正常hash算法大于8的概率为亿分之六，但是自定义对象的hash算法可能倒置经常冲突。若冲突了1024次，链表平均查询次数为512，而红黑树仅仅为10

###  （五）ConcurrentHashMap

* 1.7中：
  * 有一个一个的Segment组成，分段锁，每次操作锁住每个Segment
  * 先找到对应的Segment锁住，Segment里面是数组加链表
  
* 1.8中：
  * put 为空时则利用 CAS 进行加入进去
  * 不为空时，则用 synchronized 加锁
  
* key 和 value 为什么不能为 null

  因为如果取得值为 null ，不能确定是 put 进去的 null 还是后来置为 null 的，会引起并发问题

## 二、数据库

###  （一）事务

* #### **概念**

  事务是指满足 ACID 特性的一组操作，可以通过 commit 提交一组事务，也可以用 rollback 回滚一个事务。

* #### **ACID**

  * 原子性（Atomicity）：事务被视为不可分割的最小单元，一个事务中的所有操作要么全都执行，要么全部都不执行（回滚）。

    回滚可以使用回滚日志（Undo Log）来实现，回滚日志记录着所有事务所执行的修改操作，在执行回滚时方向执行这些操作即可。

  * 一致性（Consistency）：数据库在事务执行前后都保持一致性状态。在一致性状态下，所有事务对同一个数据读取的结果都是相同的。

  * 隔离性（Isolation）：一个事务在对数据做修改，但在未提交的情况下，对其他事务不可见。

  * 持久性（Durability）：一旦一个事务提交，则其所修改的数据会永远的保存在数据库中。即使系统崩溃，事务执行的结果也不能丢失。

    系统发生崩溃可以使用重做日志（Redo Log）进行恢复，从而实现持久性。与回滚日志的逻辑修改不同，重做日志记录的是数据页的物理修改。

  ---

  事务的ACID特性概念简单，但是不是很好理解，原因是这几个特性不是平级的关系：

  * 只有满足一致性事务执行的结果才是正确的。
  * 在无并发的情况下，隔离性一定满足，在满足原子性的情况下一致性一定满足。
  * 在并发的情况下，事务不仅要满足原子性还要满足隔离性才能满足一致性。
  * 事务满足持久性是为了能应对系统崩溃。

* #### AUTOCOMMIT

  MySQL默认采用自动提交模式。也就是说，如果不显式的使用 **STRAT TRANSACTION** 语句来开始一个事务，那么每个查询操作都会开启一个事务并自动提交。

### （二）并发一致性问题

* #### 丢失修改

  T1 事务和 T2 事务同时对数据 A 进行操作，事务 T1 先提交，事务 T2 后提交，事务 T2 的结果会覆盖事务 T1 的结果。

* #### 脏读

  事务 T1 修改了一个数据，事务 T2 读取到这个数据，事务 T1 回滚了，那么 T2 读取到的就是脏数据。

* #### 不可重复读

  事务 T1 读取数据 A ，事务 T2 对数据 A 进行了修改，事务 T1 再次读取数据 A 发现和第一次不一样。

* #### 幻读

  事务 T1 第一读取一个范围数据，事务 T2 插入了一条数据，事务 T1 再次读取发现数据多了一条。

  **产生并发一致性问题的原因是破坏了事务的隔离性，解决方法是通过并发控制来保证隔离性。并发控制可以通过锁来实现，但是锁需要用户自己来控制，较为复杂。数据库管理系统提供了事物的隔离级别，让用户以一种更轻松的方式处理并发一致性问题。**

### （三）锁

* #### 锁粒度

  MySQL中提供了两种锁粒度，行级锁和表级锁。

  应该尽量只锁定所要修改的那部分数据，而不是所有资源。锁住的数据量越小越好，这样发生锁争用的可能就越小，并发行就越高。

  但是加锁需要消耗资源，锁的各种操作（获取、释放、判断状态）都会增加系统负担，因此锁粒度越小，系统开销就越大。

  **因此在锁粒度选的时候，需要在锁开销和系统并发程度之间做一个平衡。**

* #### 锁类型

  * ##### 读写锁

    互斥锁（Exclusive）：简写为 X 锁，也称为写锁。

    共享锁（Shared）：简写为 S 锁，也称为读锁。

    **有以下两个规定：**

    一个事务对数据对象加了 X 锁，就可以对数据进行读取和更新。加锁期间，其他事务不能对此数据加任何锁。

    一个事务对数据对象加了 S 锁，只能对数据进行读取操作，不能进行更新。加锁期间，其他事务能给此数据加 S 锁，但不能加 X 锁（update、insert等操作会自动加 X 锁）。

* #### 锁协议

  * ##### 一级锁协议：

    事务 T 要修改数据 A 时必须加 X 锁，直接事务结束才释放 X 锁。

    可以解决上面的丢失修改问题，不会发生数据覆盖。一个事务正在修改其他事务就不能修改。

  * ##### 二级锁协议：

    事务 T 要读取数据 A 时必须加 S 锁，读取完后释放 S 锁。

    这样可以解决脏读，因为在其他事务修改数据时要加 X 锁，所以 T 要读取时必须等 X 锁释放了才能加上 S锁。

  * ##### 三级锁协议：

    在二级协议的基础上，要求在读取数据时必须加 S 锁，等事务完成才释放锁。

    可以解决不可重复读问题，因为在 T 读数据的时候，其他事务不能加 X 锁，从而不能修改数据。

  * ##### MySQL隐式锁定与显式锁定：

    MySQL的InnoDB存储引擎会根据事务隔离级别自动加锁，但是所有的锁都是在同一时刻释放，这被称为隐式锁定。

    InnoDB也可以用特定的语句进行显示锁定：

    > SELECT .....  LOCK In SHARE MODE; 		共享锁（读锁）
    >
    > SELECT .....  FOR UPDATE;						  互斥锁（写锁）

### （四）隔离级别

* ##### 读未提交（READ UNCOMMITED）

  事务中的修改，即使没有提交，也能读到。

* ##### 读已提交（READ COMMITED）

  只能读到已提交的数据。

* ##### 可重复读（REPEATABLE READ）

  可以保证在同一事务中多次读取同一数据结果相同。

* ##### 可串行化（SERIALIZABLE）

  强制事务串行处理，不会出现任何并发一致性问题。

  ![CYC大佬的数据隔离级别](C:\Users\杰\AppData\Roaming\Typora\typora-user-images\image-20200316180441978.png)

### （五）多版本并发控制

* #### 基本思想

  多版本并发控制（Multi-Version Concurrency Control,MVCC）是 MySQL 的 InnoDB 存储引擎实现隔离级别的一种具体方式。

  MVCC 利用了多版本的思想，写操作更新最新的版本快照，而读操作读取旧版本的快照，没有互斥关系。

  在 MVCC 中事务的操作（DELETE、INSERT、UPDATE）会为数据行新增一个版本快照。

  脏读和可重复读最根本的原因是事务读取到其他事务未提交的数据。因此 MVCC 规定了只能读取到已提交的快照，当然自己可以读取自己未提交的快照，这不算脏读。

* #### 版本号

  * 系统版本号 SYS_ID：是一个递增的数字，每开始一个新事务就会自动递增。
  * 事务版本号 TRX_ID：事务开始时的版本号。

* #### 快照读和当前读

  * 快照读

    MVCC 中 SELECT 操作是快照读不需要加锁。

  * 当前读

    MVCC 对会对数据库进行修改的操作（DELETE、INSERT、UPDATE）进行加锁，从而读取最新的数据，

    在进行 SELECT 操作时可以显式的加锁。

    ```sql
    SELECT * FROM product lock in share mode; // S锁
    SELECT * FROM product for update;		  // X锁
    ```

* #### Next-Key Locks

  Next-Key Locks 是 InnoDB 的一种锁实现，MVCC 不能解决幻读问题，Next-Key Locks 就是为了这个问题而存在的。在可重复读的隔离级别上，使用 MVCC + Next-Key Locks 可以解决幻读问题。

  Next-Key Locks 是锁住一个记录的索引和锁定索引之间的间隙。他锁定的是一个前开后闭的区间。

  

### （六）关系数据库设计理论

* #### 函数依赖

  记 A -> B表示 A 函数决定 B函数，或者说 B 函数依赖于 A 函数。

  对于 A -> B，如果能找到 A 的真子集（不包含 A 的子集）A‘ ，使得 A’ -> B ，那么 A、B 就是部分函数依赖。

* #### 异常

  数据冗余：如，某个商品出现了两次。

  修改异常：修改了一个记录中的信息，另一个记录中相同的信息未被修改。

  删除异常：删除一个信息，会丢失其他信息。

  插入异常：没确定主键，不能插入。

* #### 范式

  **高级别范式依赖于低级别范式。**

  * ##### 第一范式（1NF）	

    属性不可分，即同一列不能有多个值（列中不能再分列）。

  * ##### 第二范式（2NF）

    包含两部分，一是必须有主键，二是非主键的列必须完全依赖于主键。

  * ##### 第三范式（3NF）

    不能存在传递依赖，非主键列 B 依赖于非主键列 A，A 依赖于主键。

### （七）数据库设计的原则

* #### 与需求一定要相符合，按需求设计

* #### 性能方面要有一定的要求，如大数据量的表之间关联太多导致性能低下，通过索引设计优化

* #### 数据完整性方面，相互关联的表之间设计不当会造成修改操作后数据出错

* #### 不必要的冗余数据不要设计进数据库

* #### 表之间的耦合性不能太高，容易在修改一张表的时候对其他表造成影响

## 三、MySQL

### （一）索引

* #### B+ Tree原理

  * ##### 数据结构

    B Tree 指的是 Balance Tree ，也就是平衡树。平衡树是一个查找树，并且所有叶子节点位于同一层。

    B+ Tree 树是基于 B 树和叶子节点顺序访问指针实现的，它具有 B 树的平衡性，并且能通过顺序访问指针来提高区间查询性能。

    在 B+ 树Tree 中，一个节点的 key 从左到右依次递增排列。

    ![B+ Tree](C:\Users\杰\AppData\Roaming\Typora\typora-user-images\image-20200317175612888.png)

  * ##### 操作

    查找是通过二分查找，依次递归向下查找，直到找到叶子节点找到对应的key，拿出data。

    插入和删除操作会打乱平衡，因此在插入和删除后要进行树的分裂、合并、旋转等操作以恢复平衡性。

  * ##### 与红黑树比较

    红黑树等平衡树也可以用作实现索引，但是文件系统以及数据库普遍采用 B+ 树 作为索引结构，主要有两个原因：

    * 更少的查询次数

      平衡树的查找操作的时间复杂度树的高度有关，O(h)=O(log<sub>d</sub>N), h 为树高，d 为子节点个数（二叉树为2，术语为度），红黑树的 d 为2，而 B+ 树的度很大，所以红黑树的树高比 B+ 树大得多，查找次数就更多。

    * 利用磁盘预读特性

      为了减少磁盘的 I/O 操作，磁盘往往不是严格按需读取，而是每次都会预读。预读过程中，磁盘进行顺序读取，顺序读取不需要磁盘寻道，并且只需要很小的磁盘旋转时间。

      操作系统一般将内存和磁盘分割成固定大小的块，每一块成为一页，内存与磁盘以页为单位交换数据。数据库系统将索引的一个节点大小作为一页，使得一次 I/O 就能完全载入一个索引节点。并且利用磁盘预读，相邻的节点也能被预先载入。

  * ##### 与 B 树比较

    B 树由于每个节点都存放数据，所以距离根节点近的更快。

    B+ 树由于非叶子节点不存放信息所以能存入更多的key，存放的数据更加紧密，有更好的缓存命中率，叶子节点连续，一次遍历即可。

    B+ 树的磁盘读写代价更小：由于非叶子节点不存放信息，同样大小下容纳的节点数量就更多，一次性读入内存就越多，相对 I/O 次数就更少。

    B+ 树查询效率更稳定：因为非叶子节点不存放信息，所以每次查叶子节点都走同样的路径，每次查询次数相当。

* #### MySQL 索引

  * ##### B+ Tree 索引

    是大多数MySQL存储引擎的默认索引类型。

    因为不需要进行全表扫面，只需要对树进行搜索即可，所以速度要快上很多。

    因为 B+ Tree 的有序性，所以除了用于查找，还可以用于排序和分组。

    可以指定多个列作为索引，多个索引列共同组成键。

    适用于全键值、键值范围和键前缀查找，其中键前缀查找只适用于最左前缀查找。如果不是按照索引顺序查找则无法使用索引。

    InnoDB 的 B+ Tree 索引分为主索引和辅助索引。主索引的叶子节点存储着完整的数据记录，这种索引叫做聚集索引。因为无法将数据行存放在两个不同的地方，所以一个表只能有一个聚集索引。

    辅助索引的叶子节点存放的是数据的主键值，因此使用辅助索引进行查找时，需要先查到主键值，再到主索引中进行查找。

  * ##### 哈希索引

    哈希索引能以O(1)时间进行查找，但是失去了有序性：

    * 无法用于排序分组；
    * 只支持精确查找，不支持范围查找。

    InnoDB 存储引擎有一个特殊功能叫“自适应哈希索引”，当某个索引被使用的非常频繁时，会在 B+ Tree 索引之上再创建一个哈希索引，这样就让 B+ Tree 索引具有部分哈希索引的优点，如快速查找。

  * ##### 空间数据索引

    MyISAM 存储引擎支持空间数据索引（R-Tree），可以用于地理数据存储。空间数据索引会从所有维度来索引数据，可以有效地使用任意维度来进行组合查询。

    必须使用 GIS 相关的函数来维护数据。

* #### 索引优化

  * ##### 独立的列

    在进行查询时，索引不能是表达式的一部分，也不能是函数的参数，否则索引无法使用。

    例如，下面的语句就无法使用索引。

    ```sql
    SELECT actor_id FROM sakila.actor WHERE actor_id + 1 = 5;
    ```

  * ##### 多列索引

    在需要使用多个列作为条件进行查询时，使用多列索引比使用单列索引性能更好。

    例如，最好把 actor_id 和 film_id 设置为多列索引。

    ```sql
    SELECT * FROM sakila.actor WHERE actor_id = 1 AND film_id = 1;
    ```

  * ##### 索引列的顺序

    将选择性最强（如性别只有两个值，选择性就差，一个字段的种类越多越离散越好）的索引列放在前面。**最左原则。**

  * ##### 覆盖索引

    索引包含**所有**需要查询的字段的值。

    具有以下优点：

    * 索引通常远远小于数据行的大小，只查询索引会大大减小数据访问量。
    * 一些存储引擎（如 MyISAM）在内存中只缓存索引，而数据依赖操作系统缓存。因此，只访问索引可以不用系统调用。
    * 对于 InnoDB 存储引擎，辅助索引能覆盖查询，则无需访问主索引。

* #### 索引的优点

  * 大大的减少了服务器需要扫描的行数。
  * 帮助服务器避免进行排序和分组，以及避免创建临时表（B+ Tree 索引有序，可以用 ORDER BY 和 GROUP BY 操作。临时表主要在排序和分组中创建，不需要排序分组就不会产生临时表）。
  * 将随机 I/O 变为顺序 I/O。

* #### 索引的使用条件

  * 对于非常小的表来说，直接查询比建立索引更快；
  * 对于中大型表索引就很有效；
  * 但是对于特大型表，建立和维护索引的开销就随之变大。这种情况下，需要进行分区等操作。

### （二）查询优化

* #### 使用 Explain 进行分析

  Explain 用来分析 SELECT 查询语句，开发人员可以用过分析 Explain 结果来优化查询语句。

  计较重要的字段有：

  *  select_type：查询类型，有简单查询、联合查询、子查询等；
  *  key：使用的索引；
  *  row：扫描的行数。

* #### 优化数据访问

  ##### 减少请求的数据量

  * 只返回有必要的列：最好不要使用 SELECT * 语句。
  * 只返回有必要的行：用 LIMIT 语句来限制返回的数据。
  * 缓存重复查询的数据：使用缓存可以避免在数据库中查询，特别是经常重复查询的数据。

### （三）存储引擎

* #### InnoDB

  是 MySQL 默认的存储引擎，只有在它不支持的特性时，才会考虑其他引擎。

  实现了四个标准的隔离级别，默认为可重复读。在此级别下通过多版本并发控制 MVCC+Next-Key Locking 防止幻读。

  主索引是聚集索引，在索引中保存了数据，从而避免了与直接读取磁盘，因此查询性能有很大的提升。

  内部做了很优化，包括从磁盘读取数据时采用的预读、能够加快读操作并且自动创建的自适应哈希索引、能够加速插入的插入缓冲区等。

* #### MyISAM

  设计简单，数据以紧密格式存储。对于只读数据，或者表较小、可以容忍修复操作，则依然可以使用它。

  提供了大量的特性，包括压缩表、空间数据索引等。

  **不支持事务。**

  不支持行级锁，只支持表级锁，读取时会对所查询的表加共享锁（读锁），写入时加排他锁（写锁）。但在读取操作的时候可以进行插入新记录，这被称为并发插入。

  如果指定了 DELAY_KEY_WRITE 选项，在每次修改完成时，不会立即将修改的索引数据写入磁盘，而是写入到内存中的键缓冲区中，只有在清空缓冲区或关闭表时才会写入磁盘。这种方式会极大的提升写入性能，但是在数据库或主机发生崩溃时会造成索引损坏，需要进行修复。

* #### 比较

  * 事务：InnoDB 支持事务，MyISAM 不支持。
  * 并发：InnoDB 支持行锁，MyISAM 只支持表锁。
  * 外键：InnoDB 支持外键，MyISAM 不支持。
  * 主键：InnoDB 必须有主键，若没有会自动生成一个（用户不可见），MyISAM 可以没有主键。
  * 函数：MyISAM 中保存有行数，使用 SELECT count(*) FROM PRODUCT 会直接取出。
  * 索引：InnoDB 的数据和索引存放在一起，MyISAM 是数据索引分离的。
  * 其他：MyISAM 支持全文索引和空间数据索引，InnoDB 不支持。

### （四）Explain 语句

* select_type：表示查询的类型
* type：访问方式，如 全表、索引、范围等
* key：使用的索引
* rows：扫描出的行数

### （五）分表

* 垂直分割：将不常使用的冷数据分割输出（列），减少每次查询的数据量。适用于行数不是太多，而列很多
* 水平分割：将行数太多的话性能比较低，因此将数据分成多个表，例如每个月的账单单独分出去，手机号以183开头的、以153开头的分成不同的表中

### （六）log

- **重做日志（redo log）**

    作用：确保事务的持久性，防止在发生故障时有脏数据未写入磁盘，在重启 mysql 服务的时候，根据 redo log 进行重做，从而达到事物的持久性

    内容：物理日志，可以理解为记录的是物理数据页面修改的信息

- **回滚日志（undo log）**

    作用：保存了事务发生之前的数据的一个版本，可以用于回滚，同时可以提供多版本并发控制下的读(MVCC)，即非锁定读

- **二进制日志（bin log）**

    作用：用于复制，在主从复制中，从库利用主库上的 bin log 进行重播从而实现主从同步。还可以用于数据库基于时间点的还原

    内容：逻辑格式的日志，可以简单认为就是执行过的事务中的 sql 语句。但不仅仅完全是 sql 语句那么简单，而是包括了执行的 sql 的反向信息(insert 对应就是 delete)

- 错误日志（error log）

    作用：记录服务进程启动/关闭或运行过程中遇到的错误信息

- 慢查询日志（slow query log）

    作用：记录执行超出指定值或其他制定条件的 sql 语句



## 四、网络协议

### （一）打开url的过程

1. 在浏览器中输入url
2. （应用层）客户端获取url，由DNS协议解析域名得到ip地址，发送http请求
3. （传输层）建立TCP连接（三次握手）
4. （网络层）根据ip协议（传输数据），把TCP分割好的数据包传送给接收方，而要保证能传送到接收方需要对方的MAC地址（ARP协议，获取MAC地址）。
5. （数据链路层）将数据发送到数据链路层传输，至此报文已发出，客户端发送请求结束下
6. 服务器端收到请求并响应http请求
7. 浏览器进行页面渲染

### （二）TCP/IP协议族

* 应用层：HTTP、FTP、DNS、TELNET、SMTP等
* 传输层：TCP、UDP
* 网络层：IP、ICMP、ARP、RARP等
* 数据链路层

### （三）HTTP

* **HTTP协议：**是客户端和服务器端之间数据传输的规范，简称为超文本传输协议

* **工作过程：**

  * 域名解析
  * TCP连接（三次握手）
  * 发起HTTP请求
  * 服务器应答HTTP

* **HTTP协议组成：**

  * 请求报文由三部分组成：

    * 请求行：包含请求方式、uri、协议/版本。

      例如：POST  /user/success.html  HTTP/1.1

    * 请求头：（常见请求头）

      * Referer：表示这个请求是从哪个url跳转过来的，如从百度进入的淘宝，Referer的值就是			www.baidu.com,如果是直接访问就不会有这个头。常用于防盗链
      * Accept：告诉服务端，该请求所能支持的响应数据类型
      * Accept-Language：告知服务器，可接受的语言
      * Accept-Encoding：
      * Host：请求的服务器主机名
      * Cache-Control：请求遵循的缓存机制
      * Connection：表示客户端与服务器的连接类型，keep-alive表示持久连接
      * Content-Type：实体主类的类型
        * application/x-www-form-urlencoded：数据被编码为名/值对，这是标准的编码格式
        * multipart/form-data：数据被编码为一条消息，用于文件上传
        * text/plain：数据以纯文本的方式进行编码
      * Content-Length：实体主体的长度（字节）
      * User-Agent：HTTP客户端程序信息

    * 请求体：当请求方式是post时，会有请求体

      例如：name=liujie&age=21

    * 具体实例如下

    ```xml
    POST  /user/success.html  HTTP/1.1
    Accept:text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3
    Accept-Encoding: gzip, deflate, br
    Accept-Language: zh-CN,zh;q=0.9,en-US;q=0.8,en;q=0.7
    Cache-Control: max-age=0
    Connection: keep-alive
    Cookie: 太长
    Host: www.baidu.com
    Referer: https://www.baidu.com/link?url=u7GCXsMOiYXgnaI0xOxND0JUpwBrmJewlO8JlUqLQ9K&wd=&eqid=937a161d00012a7b000000025e6a1421
    Sec-Fetch-Mode: navigate
    Sec-Fetch-Site: same-origin
    Sec-Fetch-User: ?1
    Upgrade-Insecure-Requests: 1
    User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.87 Safari/537.36
    ```

  * 响应报文也由三部分组成：

    * 响应行：协议/版本 状态码及状态描述

      例如：HTTP/1.1 200 OK

      状态码：

      1xx：指示信息，表示请求已接收，继续处理

      2xx：成功

      ​		200 OK

      ​		204 No Content：请求成功处理，但是返回的报文没有主体

      ​		206 Partial Content：响应报文由Content-Range指定范围的实体内容

      3xx：重定向

      ​		301 Moved Permanently：永久性重定向

      ​		302 Found：临时性重定向

      ​		303 See Other：与302相同，但必须是GET方法

      ​		304 Not Modified：不满足某些条件

      4xx：客户端错误

      ​	400 Bad Request：客户端请求有错误，服务器无法理解

      ​	401 Unauthorized：请求未授权

      ​	403 Forbidden：收到请求，拒绝提供服务

      ​	404 Not Found：请求资源不存在，如url输错了

      5xx：服务端错误

      ​	500 Internal Server Error：服务器正在执行请求时发生错误

      ​	503 Service Unavailable：服务器处于超负载或正在进行停机维护，现在无法处理请求

    * 响应头：

    * Accept-Ranges：是否支持字节范围请求

      * Location：令客户端从新定向到得url
      * Server：指服务器名称
      * Content-Type：响应正文类型

    * 响应体：write写进去了，返回的html页面等等

* **HTTP中有哪些请求方式：**

  * GET：用于请求访问已经被URI识别的资源，可以通过url传参数
  * POST：用于传输信息给服务器
  * PUT：传输文件，报文主体中的文件内容，保存到url对应的路径
  * HEAD：获取报文首部，和GET类似但是不返回报文主体实体内容，一般用于URI的有效性
  * DELETE：删除文件，与PUT相反
  * OPTIONS：查询响应URI支持的方法

* **HTTP协议中1.0与1.1区别？**

  **当浏览器访问一个包含多张图片的HTML页面时，除了请求HTML页面资源，还要请求图片资源，如果每进行一次HTTP通信就要建立一个TCP连接，开销太大。**

  **长连接只需进行一次TCP连接就可以多次HTTP通信。**

  **1.0时默认为短连接，要使用长连接需要设置，Connection：Keep-Alive；**

  **1.1时默认为长连接，要关闭长连接需要设置，Connection：close。**

  **缓存处理1.1多了几个控制策略。**

* **Cookie：**

  HTTP是无状态协议，主要是让HTTP协议尽可能的简单，使得它能够处理大量事务，HTTP/1.1引入Cookie来保存状态信息。

  Cookie是服务器发送到浏览器并保存在本地的一小块数据，它会在再次访问服务器时带上，用于告知服务器是否来自于同一浏览器，所以每次都会带着，会消耗一定性能。

  * 分类

    会话期：浏览器关闭会自动删除，仅在一次会话中生效；

    持久性：指定过期时间（Expires）或有效期（max-age）之后就成为持久性的Cookie

* **Session：**

  除了可以将用户信息通过Cookie保存在本地浏览器，也可以利用Session存储在服务器端，存储在服务器端更加安全。为了防止服务器端压力过大，服务器会把长时间没有活跃的 Session 删除掉，如果超过超时时间为访问过服务器，Session 会自动失效。

  Session可以存储在服务器上的文件、内存、数据库中。也可以存在Redis中，效率更高。

  **使用Session维护用户登陆状态的过程如下：**

  * 用户进行登录，将用户信息存放在HTTP请求报文中；
  * 服务器验证该账号密码，如果正确则把用户信息存放在Redis中，key为Session ID；
  * 服务器返回的响应报文中Set-Cookie存放Session ID，客户端收到后将Cookie设置好；
  * 客户端之后对同一个服务器访问时会包括该Cookie，服务器收到后取出Session ID，从Redis中取出信息，继续操作。

* **浏览器禁用Cookie：**

  此时浏览器无法使用Cookie保存用户信息，只能使用Session，但是没有Cookie保存Session ID，而是使用URL重写技术，将Session ID当作参数写入URL中。

* **get与post的区别：**

  * get重点是从服务器上获取数据，post重点在于向服务器发送数据；
  * get中的参数是直接附加到URL后面的所以不安全，post是在实体主体中的；
  * 幂等性：同样的请求发送多次得到的结果相同。post不是幂等的，其他都是幂等的；

### （四）HTTPS

* **HTTP的安全性问题：**
  * 使用明文进行通信，可能被窃听；
  * 不验证身份，可能会被伪装；
  * 无法证明报文的完整性，可能会被篡改。
* HTTPS并不是新协议，而是让HTTP和SSL通信，再由SSL和TCP通信，也就是说HTTPS进行了隧道通信。
* 通过SSL，HTTPS具有了加密（防窃听），认证（防伪装），完整性保护（防篡改）。
* **加密**
  * 对称密钥加密：加密和解密使用同一密钥。
    * 优点：运算速度快；
    * 缺点：无法安全的将密钥传送给通信方
  * 非对称密钥加密：又称公开密钥加密，加密和解密使用不同的密钥。
    * 公开的密钥所有人都能获得，通信发送方获得接收方的公开密钥后，就可以使用公开密钥进行加密，接受方使用私有密钥进行解密。
    * 非对称加密也可以用于签名，发送方使用私有密钥加密，接收方使用发送方公开的密钥进行解密，就能判断此签名是否正确。
    * 优点：可以更安全的将公开的密钥给发送方。
    * 缺点：速度较慢。
  * HTTPS使用的加密方式：
    * 使用非对称密钥加密，传输对称密钥加密所需要的Secret Key，从而保证安全性；
    * 服务器用私钥解密后获取到Secret Key，再使用对称密钥加密，以确保效率。
  * **HTTPS的连接过程：**
    * 客户端：发送客户端所支持的加密协议及版本，SSL、TLS；
    * 服务器：服务器端筛选合适的加密协议；
    * 服务器：服务器端返回证书（包含公钥）；
    * 客户端：使用根证书验证证书的合法性；
    * 客户端：使用公钥加密生成对称密钥，发送到服务器；
    * 服务器：用私钥解密，获得对称密钥，使用对称密钥加密数据；
    * 客户端：解密数据，开始通信。
* **HTTPS的缺点：**
  * 因为要加密所以速度会慢一些；
  * 需要支付证书授权的高额费用。
* **HTTP与HTTPS的区别：**
  * HTTPS需要CA（证书），需要一定费用；
  * HTTP是超文本传输，信息是明文传输，HTTPS是具有加密的SSL传输；
  * 端口不同，HTTP是80，HTTPS是443；
  * HTTP较为简单是无状态的，HTTPS是由HTTP加SSL构成的。

### （五）网络层协议（IP、ARP、ICMP、IGMP）

* **IP 协议**

  * IP 数据报格式

    ![IP数据报](C:\Users\杰\AppData\Roaming\Typora\typora-user-images\image-20200315164707931.png)

    * 版本：4位，有两个值，IPv4和IPv6；
    * 首部长度：4位，因此最大值为15。值为1表示的是1个32位字的长度，也就是4字节。因为固定长度为20字节，所以最小值为5.如果可选字段的长度不是4的整数倍，就用尾部的填充部分来填充；
    * 区分服务：用来获得更好的服务，一般不使用；
    * 总长度：包括首部长度和数据长度；
    * 生存时间：TTL，防止无法交付的数据报在互联网中不断的兜圈子，以路由器跳数为单位，当TTL为0就丢弃数据报；
    * 协议：数据传输应该交给哪个协议，TCP、UDP、ICMP等；
    * 首部检验和：数据每经过一个路由器都要计算检验和，不包含数据部分减少工作量；
    * 标识：在数据报长度过长分片时，相同的数据报在不同分片上有相同的标识；
    * 片偏移：和标识符一起，用于发送分片的情况。

* **ARP（地址解析协议）**

  网络层实现主机之间的通信，而链路层实现每段链路之间的通信。因此 IP 数据报中的源地址和目的地址始终不变，而MAC地址随链路的改变而改变。

  ARP实现由 IP 地址得到 MAC 地址。

  ![ARP](C:\Users\杰\AppData\Roaming\Typora\typora-user-images\image-20200315171313115.png)

  每个主机都有一个APR高速缓存，里面存放局域网上的各主机和路由器的 IP 地址到 MAC 地址之间的映射。

  **如果主机 A 知道主机 B 的 IP 地址，但是ARP缓存中没有 MAC 的映射，此时主机 A 通过广播的方式发送 ARP 请求分组，主机 B 收到后会发送 APR 响应分组给主机 A 告知其 MAC 地址，随后主机 A 向高速缓存中加入 主机 B 的 IP 地址到 MAC 地址的映射。**

* **网络控制报文协议ICMP**

  ICMP是为了更有效地转发 IP 数据报以及提高交付成功的机会。

  ![ICMP](C:\Users\杰\AppData\Roaming\Typora\typora-user-images\image-20200315173309135.png)

  ICMP 报文分为差错报告报文和询问报文。

  ![报文种类](C:\Users\杰\AppData\Roaming\Typora\typora-user-images\image-20200315173412197.png)

  * Ping

    Ping 是 ICMP 的一个重要应用，主要用于测试两台主机之间是否连通。

    Ping 的原理是通过向目的主机发送 ICMP Echo 请求报文，目的主机收到会发送 Echo 应答报文。Ping 会根据时间和成功响应的次数估计出数据包往返的时间和丢包率。

### （六）传输层协议（TCP和UDP）

* **TCP和UDP的特点：**

  * 用户数据报协议UDP（User Datagram Protocol）是无连接的，尽最大可能交付，没有拥塞控制，面向报文（对于用户传下来的报文不拆分合并，只是添加UDP首部）。支持一对一、一对多、多对一、多对多的交互通信。
  * 传输控制协议TCP（Transmission Control Protocol）是面向连接的，提供可靠交付，有拥塞控制，流量控制，提供全双工通信，面向字节流（把报文看成字节流，拆分成大小不等的数据块），每一条TCP只能是点对点的（一对一）。

* **UDP首部格式：**

  ![UDP首部](C:\Users\杰\AppData\Roaming\Typora\typora-user-images\image-20200314155543825.png)

  首部只有8字节，包含源端口、目的端口、长度、校验和。12字节的伪首部是为了计算检验和 临时添加的。

* **TCP首部格式：**

  ![TCP首部格式](C:\Users\杰\AppData\Roaming\Typora\typora-user-images\image-20200314160058503.png)

  * 源端口/目的端口：表示数据从哪来到哪去；
  * 序号：用于对字节流编号，例如第一个字节编号为 301，数据长度为 100，那么下一个报文段的序号应该是401。
  * 确认号：期望收到下一个报文段的序号，例如B收到A发来的一个报文段序号为 301，长度为100，因此B期望下一个报文段的序号为 401；
  * 数据偏移（4位）：指的是数据部分距离报文段起始处的偏移量，实际上指的是首部的长度；
  * 保留（6位）
  * **确认ACK：**当 ACK=1时确认号有效，否则无效。TCP 规定，在连接建立后所有传送的报文段都必须把 ACK置为1；
  * **同步SYN：**在连接建立时用来同步序号，当 SYN=1，ACK=0 时表示这是一个连接请求报文段。若对方同意建立连接，则响应报文中的 SYN=1，ACK=1；
  * **终止FIN：**用来释放一个连接，当 FIN=1 时，表示此报文段的发送方已经发送完毕，并要求释放连接；
  * 窗口：窗口值作为接收方让发送方设置其发送窗口的限制。之所以要有这个限制是因为接收方的数据缓存空间是有限的；

* **TCP的三次握手：**

  ![三次握手](C:\Users\杰\AppData\Roaming\Typora\typora-user-images\image-20200314164410128.png)

  * **首先 B 处于 LISTEN（监听）状态，等待客户端的连接请求；**

  * **A 向 B 发送连接请求报文，SYN=1，ACK=0，选择一个初始的序号 x，A 进入同步已发送状态；**

  * **B 收到连接请求报文，如果同意连接，则向 A 发送确认报文 SYN=1，ACK=1，确认号为 x+1，同时也选择一个初始的序号 y，B 进入同步已接受状态；**

  * **A 收到 B 的确认报文后，还要再给 B 发送确认，ACK=1，确认号为 y+1，序号为 x+1，A 进入已建立连接状态；**

  * **B 收到后，连接建立。**

* **三次握手的原因：**

  第三次握手是为了防止失效的连接到达服务器，让服务器错误的打开。

  客户端发送的连接请求如果在网络中滞留，那么就会隔很长时间才能收到服务器发回的连接确认。客户端等待一个超时时间后，就会从新发送一个请求。但是这个滞留的连接最终会到达服务器端，如果不进行第三次握手，服务器就会开启两个连接。如果有第三次握手，客户端会忽略服务器之后发送的对滞留连接请求的连接确认，不进行第三次握手，因此就不会再次打开一个连接。

* **TCP的四次挥手**

  ![四次挥手](C:\Users\杰\AppData\Roaming\Typora\typora-user-images\image-20200314175153127.png)

  * A 发送连接释放报文，FIN=1，A 进入终止状态1；
  * B 收到后发出确认，ACK=1，此时TCP处于半关闭状态，B 能给 A 发送数据，A 不能给 B 发，B 进入关闭等待状态，A 进入终止状态2；
  * 当 B 不需要连接时，发送连接释放报文，FIN=1，B 进入最后确认状态；
  * A 收到后，发出确定，进入**时间等待状态（下面解释）**；
  * B 收到 A 发送的确定后，直接关闭连接。、

* **四次挥手的原因：**

  客户端发送FIN后，服务器收到后进去关闭等待状态，此状态是为了让服务器把未发送完的数据发送完，发送完以后再FIN给客户端。

* **TIME_WAIT:**

  客户端在接收到服务器发送的FIN报文后进入此状态，此时并不是直接进入CLOSED状态，还需要等待一个时间计时器设置的时间2MSL。这么做的的理由有两个：

  * 确保最后一个确认报文能够到达，如果 B 没收到 A 发送的确认报文，那么就会从新发送连接释放报文（FIN），A 等待一段时间就是为了处理这种情况的发生；
  * （2MSL 就可以保证全部消失）等待一段时间是为了让本连接持续时间内所产生的所有报文都从网络中消失，使得下一个连接不会出现旧的连接请求报文。

* **TCP可靠传输：用超时从传实现。**

* **TCP滑动窗口：**

  窗口是缓存的一部分，用来暂时存放字节流。发送方和接收方各有一个窗口，接收方通过TCP报文段中的窗口字段告诉发送方自己的窗口大小，发送方设置为和接收方相同的大小。

  如果发送方的窗口的左边已经发送且被接受方确定，则窗口会向右滑动直至窗口左边第一个字节已经不是发送且被确定的状态，接受方发送确定后就向右滑动。

  接受窗口只会对窗口内最后一个按序到达的字节进行确定，如{31，34，35}，只会对34进行确定。发送方得到确定后就知道这个字节之前的所有字节都已确定。

* **TCP流量控制：通过窗口大小控制，若窗口设置为0，则发送方不能发送数据。**

* **TCP如何保证数据的顺序：**通过报文中的序号。

* **TCP拥塞控制：**

  如果网络出现拥塞，分组会丢失，此时发送方会从新发送，从而导致拥塞更加严重。因此当出现拥塞时，应该控制发送方的速率。这和流量控制比较像，但是流量控制是让接收方来得及接受，拥塞控制是为了降低整个网络的拥塞成都。

  TCP主要通过四个算法来进行拥塞控制：慢开始、拥塞避免、快重传、快恢复。

  * **慢开始与拥塞避免：**

    发送的最初阶段是慢开始，令cwnd=1，发送方只能发送1个报文段，当接收确认后，cwnd翻倍...

    设置一个慢开始门限ssthresh，当cwnd>=ssthresh时，进入拥塞避免，每次cwnd加一，如果出现超时令ssthresh=cwnd/2，从新开始慢开始。

  * **快重传与快恢复：**

    放发送方发现有丢失时，立即从传丢失的，这种情况下只是丢失个别报文段，而不是网络拥塞。因此执行快恢复，令ssthresh=cwnd/2，cwnd=ssthresh，直接进入拥塞避免。

    **慢开始和快恢复的快慢实质是控制cwnd的值而不是cwnd的增长速度，拥塞避免是控制增长速度。**

### （七）应用层协议（DNS、FTP、DHCP）

* **DNS**

  DNS是一个分布式数据库，提供了主机名和 IP 地址之间的转换服务。这里分布式数据库是指，每个站点只保留自己的那部分数据。

  域名有自己的层次结构，从上到下依次为：根域名、顶级域名、二级域名。

  DNS 可以使用UDP进行传输，使用的是端口号都为53。大部分情况都是用 UDP，有两种情况使用 TCP：

  * 如果返回的响应超过512字节（UDP 最大支持512字节）；
  * 区域传输（是指主域名服务器向辅助域名服务器传送变化的那部分数据）。

* **FTP**

  文件传送协议，FTP 使用 TCP 连接，他需要两个连接传送一个文件：

  * 控制连接：客户端将命令传送给服务器，并传回应答（端口21）；
  * 数据连接：用来传送数据。

* **DHCP**

  DHCP（Dynamic Host Configuration Protocol，动态主机配置协议）提供了即插即用的连网方式，用户不在需要手动配置 IP 地址等信息。

* **电子邮件协议：**

  邮件协议包括发送协议和读取协议，发送协议常用 SMTP，读取协议常用 POP3 和 IMAP。

  * SMTP 只能发送 ASCII 码，而互联网扩充 MIME 可以发送二进制文件。MIME 没有改动 SMTP，而是增加了邮件的主体结构，定义了非 ASCII 码的编码规则。
  * POP3 特点是从服务器读取下来邮件后，就把该邮件删除。但新版本的 POP3 可以不删除邮件。
  * IMAP的客户端和服务器保持一致，如果不手动删除邮件，服务器上的邮件也不会删除。

* **ARP 解析 MAC 地址：**

  * DHCP 过程只知道网关路由器的 IP 地址，为了获取 MAC 地址需要用 ARP 协议；
  * 主机生成一个包含目的地址为网关路由器 IP 地址的 ARP 查询报文，将该 ARP 查询报文放入一个具有广播目的地址的以太网帧中，并向交换机发送该以太网帧，交换机将该帧发送给所有连接设备；
  * 网关路由器接收到该帧后，不断向上分解得到 ARP 报文，发现其 IP 地址与其接口的 IP 地址匹配，因此发送一个 ARP 应答报文，包含了 MAC 地址，并发送回主机。

* **DNS域名解析**

  * 浏览器先检查自身缓存中有没有域名对应的此域名被解析的 IP 地址，有的话直接解析结束。
  * 如果浏览器缓存没有则去操作系统中找。
  * 如果还没命中就去请求本地域名服务器去解析这个域名，80%就在这里结束了。
  * 如果还没找到则会去跟服务器里找，找到后返回给本地服务器缓存起来，再返回给用户。

### （八）OSI 七层模型

* #### 七层模型

  * **物理层**

    定义物理设备的标准，如连接方式、电气特性、机械特性等

  * ##### 数据链路层

    对于网络间的链路进行管理，管理数据在物理线路上的传输

  * ##### 网络层

    在计算机网络中两个计算机之间可能会经过多个数据链路。网络层的任务就是选择合适的网间路由和交换节点，确保数据及时传送。

    协议：IP 协议、ARP、ICMP

  * ##### 传输层

    传输层的主要任务是负责向两台主机的进程之间的通信提供通用的数据传输服务，进行流量控制等。

    协议：TCP、UDP

  * ##### 应用层（会话层、表示层、应用层）
  
    通过应用进程间的交互完成特定网络应用。应用层协议定义的是应用进程间的通信和交互规则。
  
    协议：HTTP、DNS、FTP、DHCP

## 五、Java 并发

### （一）使用线程

* #### 三种使用线程的方法

  * 实现 Runnable 接口；
  * 实现 Callable 接口；
  * 继承 Thread 类。

  实现 Runnable 和 Callable 接口的类只能当作一个可以在线程中运行的任务，不是真正意义上的线程，因此最终还是需要 Thread 调用。可以理解为任务通过线程驱动而执行的。

* #### 实现 Runnable 接口

  需要实现 接口中的 run() 方法。

  ```java
  public class MyRunnable implements Runnable{
      @Override
      public void run(){
          //...
      }
  }
  ```

  使用 Runnable 实例再创建一个 Thread 实例，然后再用 start() 方法启动线程。

  ```java
  public static void main(String[] args){
      MyRunnable instance = new MyRunnable();
      Thread thread = new Thread(instance);
      thread.start();
  }
  ```

* #### 实现 Callable 接口

  与 Runnable 相比，Callable 可以有返回值，返回值通过 FutureTask 进行封装。

  ```java
  public class MyCallable implements Callable<Integer>{
      public Integer call(){
          return 123;
      }
  }
  ```

  ```java
  public static void main(String[] args) throws ExecutionException,InterruptedException{
      MyCallable mc = new MyCallable();
      FutureTask<Integer> ft = new FutureTask<>(mc);
      Thread thread = new Thread(ft);
      thread.start();
      System.out.println(ft.get());
  }
  ```

* #### 继承 Thread 类

  同样也是需要实现 run() 方法，因为 Thread 类也实现了 Runnable 接口。

  当调用 start() 方法启动一个线程时，虚拟机会将该线程放入就绪队列中等待被调度，当一个线程被调度时会执行线程的 run() 方法。

  ```java
  public class MyThread extends Thread{
      public void run(){
          //...
      }
  }
  ```

  ```java
  public static void main(String[] args){
      MyThread mt = new MyThread();
      mt.start();
  }
  ```

* #### 实现接口 VS 继承 Thread

  实现接口会更好一些，因为：

  * Java 不支持多重继承，因此继承了 Thread 类就无法继承其他类，但是可以实现多个接口；
  * 类可能只要求可执行就行，继承整个 Thread 类开销过大。

### （二）基础线程机制

* #### sleep()

  Thread.sleep(millisec) 方法会休眠当前正在执行的线程，millisec 单位为毫秒。

  sleep() 可能会抛出 InterruptedException，因为异常不能跨越线程传回 main() 中，因此必须在本地进行处理。线程中抛出的其他异常也需要在本地进行处理。

  ```java
  public void run(){
      try{
          Thread.sleep(5000);
      }catch (InterruptedException e){
          e.printStackTrace();
      }
  }
  ```

* #### yield()

  对静态方法 Thread.yield() 的调用声明了当前线程已经完成了生命周期中最重要的部分，可以切换到其他线程执行了。该方法只是读线程调度器的一个建议，而且也是建议具有相同优先级的线程可以运行。

### （三）互斥同步（锁）

Java 提供了两种锁机制来控制多个线程对共享资源的互斥访问，第一个是 JVM 实现的 synchronized，而另一个是 JDK 实现的 ReentryLock。

* #### **synchronized**

  * ##### 同步一个代码块

  ```java
  public void func(){
      synchronized(this){
          //...
      }
  }
  ```

  它只作用于同一个对象，如果调用两个对象的同步代码块不会进行同步。

  * ##### 同步一个方法

  ```java
  public synchronized void func(){
      //...
  }
  ```

  和同步代码块一样是作用于一个对象。

  * ##### 同步一个类

  ```java
  public void func(){
      synchronized(Main.class){
          //...
      }
  }
  ```

  作用于整个类，就算调用同一个类的不同对象也会进行同步。

  * ##### 同步静态方法

  等同于同步一个类。

* #### ReentrantLock

  ReentrantLock 是 java.util.concurrent(J.U.C) 包中的锁。

  ```java
  public class LockExample{
      
     	private Lock lock = new ReentrantLock();
      
      public void func(){
          lock.lock();
          try{
              for(int i = 0; i < 10; i++){
                  System.out.println(i + "  ");
              }
          }finally{
              lock.unlock();
          }
      }
  }
  ```

  ```java
  public static void main(String[] args){
      LockExample lockExample = new LockExample();
      ExecutorService executorService = Executors.newCachedThreadPool();
      executprService.execute(() -> lockExample.func());
      executprService.execute(() -> lockExample.func());
  }
  ```

  ```java
  0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9
  ```

* #### 比较

  * ##### 锁的实现

    synchronized 是 JVM 实现的，并没有直接暴露给我们。而 ReentryLock 是JDK实现的，可以通过直接查看他的源码，来看他是如何实现的。

  * ##### 性能

    新本版的 Java 对 synchronized 进行了许多优化，例如自旋锁等，synchronized 与 ReentryLock 大致相同。

  * ##### ReentryLock 比 synchronized 增加了几个高级功能

    相比 synchronized ， ReentryLock 增加了一些高级功能。主要来说有三点：①等待可中断；②可以实现公平锁；③可实现选择性通知。

    * 等待可中断：当持有锁的线程长期不释放锁的时候，正在等待的线程可以选择放弃等待，改为处理其他事情，通过 lock.lockInterruptibly() 来实现的。
    * 可以实现公平锁：公平锁是指多个线程等待锁时，需要按照申请的顺序来一次获得锁。ReentryLock 默认是非公平的，但是可以设置为公平的。
    * 选择性通知：synchronized 可以通过wait() 和 notify()/notifyAll() 方法可以实现等待/通知机制，ReentryLock 通过 Condition 的 await()、signal()/signalAll() 进程线程通知，一个 Lock 对象可以创建多个 Condition 实例，线程对象可以注册在指定的 Condition 中，实现选择性通知。

* #### 使用选择

  除非要使用 ReentryLock 的高级功能，否则优先使用 synchronized。这是因为 synchronized 是 JVM 原生的一种锁机制，而 ReentryLock 不是所有的 JDK 版本都支持。并且因为 synchronized 不用担心锁未释放的而导致的死锁问题，JVM 会确保锁的释放。

### （五）线程间的协作

当多个线程可以一起工作去解决某个问题时，如果某些部分必须在其他部分之前完成，那么就需要对线程进行协调。

* #### join()

  在线程中调用另一个线程的 join() 方法，会将当前线程挂机，而不是忙等待，直到目标线程结束。

  对于以下代码，虽然是 a 线程先启动，但是因为在 a 线程中调用了 b 线程的 join() 方法，a 线程会等待 b 线程结束才继续进行，因此可以保证 b 线程在 a 线程之前执行完。

  ```java
  public class JoinTest{
      
      private class A extends Thread{
          private B b;
          public A(B b){
              this.b = b;
          }
          
          @Override
          public void run(){
              try{
                  b.join();
              }catch(Exception e){
                  e.printStackTrace();
              }
              System.out.prinfln("A执行了");
          }
      }
      
      private class B extends Thread{
          @Override
          public void run(){
              System.out.prinfln("B执行了");
          }
      }
      
      public void test(){
          B b = new B();
          A a = new A(b);
          a.start();
          b.start();
      }   
  }
  ```

  ```java
  public static void main(String[] args){
      JoinTest join = new JoinTest();
      join.test();
  }
  ```

  ```java
  B 
  A
  ```

* #### wait() notify() notifyAll()

  调用 wait() 使得线程等待某个条件满足时，线程在等待时挂起，当其他线程的运行使得这个条件被满足时，其他线程会调用 notify() 或 notifyAll() 方法来唤醒挂起的线程。

  wait() notify() notifyAll() 是属于 Object 的一部分，而不属于 Thread。

  只能在同步方法或者同步块中使用，否则在运行时抛出 illegalMonitorStateException。

  使用 wait() 会释放锁，因为如果没释放锁其他线程获得不了锁进入不了同步块中就不能唤醒挂起线程，造成死锁。

* #### await() signal() signalAll()

  java.util.concurrent 类库中提供了 Condition 类来实现线程之间的协调。可以在 Condition ss行调用 上述方法。

  相比于 wait() 这种等待方式，await() 可以指定等待条件，因此更加灵活。

### （六）非阻塞同步

* #### CAS

  乐观锁需要操作和冲突检测这两个步骤需要原子性，这里就不能用互斥同步来保证了。硬件支持原子性的经典操作就是 CAS（Compare And Swap，比较与交换），CAS 指令需要三个操作数，分别是内存地址 V、旧的预期值 A 和新值 B 。当执行操作时，只有当 V 等于 A 时才能把 B 赋给 V。

* #### AtomicInteger

  AtomicInteger 中用的就是 CAS

### （七）锁优化

这里的锁优化主要指的是 JVM 对 synchronized 的优化。

* #### 自旋锁

  进入阻塞状态的开销很大，应该尽量避免，自旋锁的思想是让一个线程在请求共享数据的锁时执行忙循环 （自旋）一段时间，如果这段时间内能获得锁就避免了进入阻塞状态。

  自旋锁虽然能够避免进入阻塞状态，但是占用了 CPU 时间，因此它适用于共享数据的锁定状态很短的场景。

* #### 锁清除

  锁清除是指对于被检测出不可能存在竞争问题的共享数据进行锁清除。

* #### 锁粗化

  锁粗化是指如果一系列操作都需要对同一个对象反复加锁和解锁，频繁的加锁解锁导致性能被损耗。

  如，多次进行多次连续 append() 方法，而 append() 方法内部都有同步块，因此只需要在第一个 append() 方法之前和最后一个 append 之后加锁即可，这样只需加一次锁。

* #### 偏向锁

  偏向锁的思想是偏向于第一个获得锁对象的线程，这个线程在之后获取锁就不需要进行同步操作。当另一个线程尝试获取锁对象时，偏向状态就结束了。

### （八）线程池

* #### 使用线程池的好处

  * 降低资源消耗。通过重复利用已创建的线程降低创建和销毁时的开销。
  * 提高响应速度。当任务到达时不需要等待线程创建，直接可以使用。
  * 提高线程的客观理性。如果无限制的创建线程不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以统一分配、调优和监控。
  
* #### Executor框架

  * #### 简介

    Executor 框架是 JDK1.5 之后引进的，通过 Executor 来启动线程比 Thread.start() 开启更好，除了易于管理外，效率还更好。

  * #### 结构

    * ##### 任务（Runnable/Callable）

      执行任务需要实现 Runnable/Callable 接口，实现类都可以通过**ThreadPoolExecutor 或者ScheduledThreadPoolExecutor执行**

    * ##### 任务的执行（Executor）

      ![Executor](C:\Users\杰\AppData\Roaming\Typora\typora-user-images\image-20200403175902729.png)

      任务执行机制的核心接口为 Executor 以及继承该接口的 **ExecutorService** 接口。**ThreadPoolExecutor 和 ScheduledThreadThreadPoolExecutor 都实现了 ExecutorService接口。**
      
      注：Executor 接口中仅有 execute() 方法，ExecutorService 继承了 Executor ，并且多了 submit(...)、showdown() 等等多个方法。
      
    * ##### 异步计算的结果
    
      **Future** 接口以及它的实现类 **FutureTask** 都可以代表异步计算的结果。
    
      当我们把 Runnable 接口或 Callable 接口的实现类交给 ThreadPoolExecutor 或者 ScheduleThreadPoolExecutor 执行。（调用 submit() 方法时会返回一个 FutureTask 对象）
    
  * #### Executor 框架使用过程

    **1.首先创建一个实现了 Runnable 或 Callable 接口的任务对象。**

    **2.把创建好的对象交给 ExecutorService 执行：ExecutorService.execute(Runnable command) 或者也可直接把 Runnable 接口对象交给 ExecutorService 执行：ExecutorService.submit(Runnable task) 或 ExecutorService.submit(Callable<T> task)。**

    **3.如果执行了 submit(...) 方法，会返回一个 FutureTask 对象，由于 FutureTask 实现了 Runnable ，我们也可以创建一个 FutureTask 对象直接交给 ExecutorService 直接执行。**

    **4.最后，主线程可以通过 FutureTask.get() 来等待任务执行完成，可以通过 FutureTask.cancel(boolean mayInterruptIfRunning) 来取消此任务的执行。**

* #### ThreadPoolExecutor类简单介绍（重要）

  * ##### ThreadPoolExecutor 类分析

    ThreadPoolExecutor 类有四个构造函数，只需要看最长的即可

```java
/**
     * 用给定的初始参数创建一个新的ThreadPoolExecutor。
     */
public ThreadPoolExecutor(int corePoolSize,//线程池的核心线程数量
                       int maximumPoolSize,//线程池的最大线程数
                       long keepAliveTime,//当线程数大于核心线程数时，多余的空闲线程存活的最长时间
                       TimeUnit unit,	  //时间单位
                       BlockingQueue<Runnable> workQueue,//任务队列，用来储存等待执行任务的队列
                       ThreadFactory threadFactory,//线程工厂，用来创建线程，一般默认即可
                       RejectedExecutionHandler handler//拒绝策略，当提交的任务过多而不能及时处														理时，我们可以定制策略来处理任务
                       ) {
    if (corePoolSize < 0 ||
        maximumPoolSize <= 0 ||
        maximumPoolSize < corePoolSize ||
        keepAliveTime < 0)
        throw new IllegalArgumentException();
    if (workQueue == null || threadFactory == null || handler == null)
        throw new NullPointerException();
    this.corePoolSize = corePoolSize;
    this.maximumPoolSize = maximumPoolSize;
    this.workQueue = workQueue;
    this.keepAliveTime = unit.toNanos(keepAliveTime);
    this.threadFactory = threadFactory;
    this.handler = handler;
}
```

* **ThreadPoolExecutor 的3个重要参数：**

  * **corePoolSize：**核心线程数定义了最小可以同时运行的线程数。
  * **maximumPoolSize：**当队列中存放的线程数达到容量时，当前可以同时运行的线程数变为最大线程数。
  * **workQueue：**任务队列，当新任务来临时，先判断是否达到核心线程数，如果达到则放入等待队列中。

  ThreadPoolExecutor 其他常见参数：

  * **keepAliveTime：**当线程池中的线程大于核心线程数时，如果没有新的线程任务提交，核心线程外的线程不会立即销毁，而实等待的时间超过 keepAliveTime 以后销毁。
  * **unit：**keepAliveTime 参数的单位。
  * **threadFactory：**创建新线程时会用到。
  * **handler：**拒绝（饱和）策略，当任务太多时处理不过来时，可以置顶策略。

* #### 处理流程

  ![线程池处理流程](C:\Users\杰\AppData\Roaming\Typora\typora-user-images\image-20200405170027524.png)

* #### ThreadPoolExecutor 的饱和策略

  当同时运行的线程已经达到最大线程数并且队列也已经满了，TheadPoolExecutor 定义了一些饱和策略：

  * **TheadPoolExecutor.AbortPolicy：**抛出 RejectExecutionException 来拒绝新任务的处理。
  * **TheadPoolExecutor.CallerRunsPolicy：**直接在调用 execute 方法的线程中执行被拒绝的任务，如果执行程序已关闭则抛弃任务。一次这种策略会降低提交速度，影响性能。如果能接受此延迟并且不能抛弃任何任务的话可以使用此策略。
  * **TheadPoolExecutor.DiscardPolicy：**直接丢弃。
  * **TheadPoolExecutor.DiscardOldestPolicy：**丢弃最早未处理的任务。

* #### 线程池的创建

  方法一：**通过 ThreadPoolExecutor 的构造函数创建**

  方法二：**通过 Executors 工厂创建**，配置一个太麻烦，因此可以通过工厂创建的常用的线程池：

  **1.Executors.newCachedThreadPool：创建一个可缓存的线程池，如果线程池大小超过了处理任务所需要的线数，可以灵活的回收。当任务数增加时，则智能的添加线程。对于执行很多短期异步任务的程序来说性能较好。最大线程数为 Integer 的最大值。任务过多会 OOM。**

  **2.Executors.newFixedThreadPool(int nTheads)：创建一个固定大小的线程池，但是队列无界（队列大小为 Integer 的最大值），任务过多时会导致 OOM。**

  **3.Executors.newSingleThreadPool：创建一个单线程的线程池，相当于串行执行，以无界队列的当时来执行线程。任务过多会 OOM。**

* #### 示例 Runnable+ThreadPoolExecutor

```java
/** MyRunnable 我的任务线程
 * @author 刘杰
 */
public class MyRunnable implements Runnable{

    private String id;

    public MyRunnable(String id){
        this.id=id;
    }

    @Override
    public void run() {
        System.out.println("正在执行的线程名："+Thread.currentThread().getName()+"任务id："+id);
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("执行结束的线程名："+Thread.currentThread().getName()+"任务id："+id);
    }
}
```

```java
public class ThreadPoolExecutorDemo {

    private static final int CORE_POOL_SIZE = 5;
    private static final int MAX_POOL_SIZE = 5;
    private static final int QUEUE_CAPACITY = 100;
    private static final long KEEP_ALIVE_TIME = 1l;

    public static void main(String[] args) {
        //创建自定义线程池
        ThreadPoolExecutor executor = new ThreadPoolExecutor(CORE_POOL_SIZE,
                MAX_POOL_SIZE, KEEP_ALIVE_TIME,
                TimeUnit.SECONDS,
                new ArrayBlockingQueue<>(QUEUE_CAPACITY),
                new ThreadPoolExecutor.CallerRunsPolicy());

        for (int i=0;i<10;i++){
            //创建工作线程
            Runnable worker=new MyRunnable(i+1+"");
            //执行线程
            executor.execute(worker);
        }
        //关闭线程池
        executor.shutdown();
        while (!executor.isTerminated());
        System.out.println("完成所有工作！");
    }
}
```

```结果
执行结果：
正在执行的线程名：pool-1-thread-3任务id：3
正在执行的线程名：pool-1-thread-4任务id：4
正在执行的线程名：pool-1-thread-1任务id：1
正在执行的线程名：pool-1-thread-2任务id：2
正在执行的线程名：pool-1-thread-5任务id：5
执行结束的线程名：pool-1-thread-3任务id：3
正在执行的线程名：pool-1-thread-3任务id：6
执行结束的线程名：pool-1-thread-5任务id：5
执行结束的线程名：pool-1-thread-1任务id：1
正在执行的线程名：pool-1-thread-1任务id：8
执行结束的线程名：pool-1-thread-4任务id：4
执行结束的线程名：pool-1-thread-2任务id：2
正在执行的线程名：pool-1-thread-2任务id：10
正在执行的线程名：pool-1-thread-4任务id：9
正在执行的线程名：pool-1-thread-5任务id：7
执行结束的线程名：pool-1-thread-3任务id：6
执行结束的线程名：pool-1-thread-4任务id：9
执行结束的线程名：pool-1-thread-5任务id：7
执行结束的线程名：pool-1-thread-1任务id：8
执行结束的线程名：pool-1-thread-2任务id：10
完成所有工作！
```

* #### 几个常见的对比

  * ##### execute() 和 submit() 

    execute() 用于提交不需要返回值的任务，所以无法判断任务是否被执行。

    submit() 用于提交需要返回值的任务。返回一个 Future 对象，可以判断是否执行成功，并且可以使用 Future 的 get() 方法获取返回值。

  * ##### shutdown() 和 shutdownNow()

    shutdown() 关闭线程池，线程池状态变为 SHUTDOWN，队列中的还会执行完。

    shutdownNow() 关闭线程池，状态变为STOP，线程池会终止当前执行的任务，返回正在等待执行的List.

  * ##### isTerminated() 和 isShutdown()

    isShutdown() 是调用完 shutdown() 后为 true。

    isTerminated() 是调用完 shutdown() 后队列中的任务执行完以后为 ture。

### （九）AQS

* #### AQS 原理

  **AQS 的核心思想是，如果共享资源空闲，则直接使用并且加锁，如果共享资源已经被占用了，则当前线程需要加入到一个双向队列中排队。**

* #### 公平锁与非公平锁

  非公平锁调用完 lock 后，会进行 CAS 抢锁，能抢到则直接返回。

  非公平锁在 CAS 失败后和公平锁一样会进行 tryAcquire 方法，如果发现此时锁被释放了，非公平锁会 CAS 再次抢锁，公平锁会先判断队列中是否有等待的，有就不抢直接等待。

* #### CountDownLatch（计数器）

  用来控制一个或多个线程等待多个线程，维护了一个计数器，每次当一个线程执行完毕计数器-1，当减到0时，那些因为调用 await() 方法进入等待的线程就会被唤醒。典型场景就是启动一个服务需要等待多个组件加载完毕再执行。

### （十）常见问题

* ##### 进程与线程的区别：

  进程：进程是程序的一次执行过程，是系统运行程序的基本单位，进程是动态的。

  线程：线程是进程的一个执行单元，是一个更小的执行单位。与进程不同的是同一个进程下的多个线程可以共享进程的堆和方法区资源，每个线程又拥有自己独立的程序计数器、虚拟机栈

* ##### 为什么要引入线程：

  进程之间的切换开销较大，线程开销比较小。

  进程在同一时间只能干一件事，所以用多个线程执行不同的任务，更有利于并发。

* ##### 并发与并行的区别：

  并发：在一段时间内多个任务都在执行（时间片交替执行）。

  并行：单位时间内，多个任务同时执行。
  
* ##### sleep() 与 wait() 方法的区别

  最主要的区别是 sleep 不释放锁，wait 释放锁。

  wait 一般用户线程间的交互/通信，sleep 一般用于暂停线程。

* ##### 为什么我们调用 start() 方法时会调用 run() 方法，为什么不能直接调用 run() 方法？

  new 一个 Thread ，线程进入新建状态，调用 start() 会启动线程并进入就绪状态，分配到时间片后就可以执行了。start() 会准备线程执行的响应准备，然后自动执行 run() 的内容。直接调用 run() 相当于调用一个普通方法，并不会在线程中执行它，还是在主线程（main）中执行。
  
* ##### 死锁的条件

  * 互斥条件：一个资源在同一时间仅可以被一个线程持有。
  * 不可抢占条件：当一个资源被一个线程拥有，其他线程则不能获得该资源。
  * 请求与保持条件：当一个线程申请资源时，因为申请不到而阻塞时不释放已经获得的资源。
  * 循环等待条件：若干个线程形成一个循环请求资源的关系。

* ##### 如何破坏线程死锁？

  * 破坏请求与保持条件：一次性申请完所有资源。
  * 破坏不可剥夺条件：当线程申请不到需要的资源时，主动释放自己占有的资源。
  * 破坏循环等待条件：将系统中的资源进行编号，将稀缺的资源（容易被抢占）编较大的号，申请资源时需要按编号申请，只有获得了较小编号的资源才能申请较大的。

## 六、JVM

### （一）运行时数据区域

![运行时数据区域](C:\Users\杰\AppData\Roaming\Typora\typora-user-images\image-20200326185442423.png)

* #### 程序计数器

  记录正在执行的虚拟机字节码的行号。

* #### 虚拟机栈

  每个方法在执行的同时都会创建一个栈帧用于存储局部变量表、操作数栈、常量池引用等信息，每一个方法从调用到完成的过程，对应一个栈帧在虚拟机栈中的入栈和出栈的过程。

  可以通过 **-Xss 参数**来指定每个线程的虚拟机栈的大小，默认大小为 1M。

  该区域可能的异常：

  * 当线程的栈深度超过最大值时，会抛出 StackOverflowError 异常。
  * 栈进行动态扩展时，无法申请到足够的内存时，会抛出 OutOfMemoryError 异常。
  
* #### 本地方法栈

  本地方法栈与虚拟机栈类似，本地方法栈是为本地方法服务的。本地方法一般是用其他语言实现的（C、C++、汇编等）。

* #### 堆

  所有对象都在这里分配内存，是垃圾收集的主要地方（“GC 堆”）。

  现代的垃圾收集器都采用分代回收，其主要思想是针对不同类型的对象采用不同的垃圾回收算法。可以将堆分为两块，新生代与老年代。

  堆不需要连续内存，可以动态增加，增加失败会抛出 OutOfMemoryError 异常。

  可以通过 **-Xms 和 -Xmx** 这两个虚拟机参数来控制堆内存大小，前者是初始大小，后者是最大大小。

* #### 方法区

  用于存放被加载类的元信息，即类信息、常量、静态变量等。

  和堆一样不需要连续的空间，可以动态扩展，扩展失败一样会抛出 OutOfMemoryError 异常。

  JDK 1.8 以后方法区的实现变为**元空间**，元空间使用的是本地内存，元空间存储类元信息，静态变量和字符串常量池等放入堆中。

  可以通过 **-XX：MetaspaceSize** 初始空间大小，**-XX：MaxMeatspaceSize** 最大空间，默认无限制。

* #### 运行时常量池

  **存在于方法区中。**

  存放 class 文件的元信息，编译后的代码数据等。

### （二）垃圾收集

* #### 收集什么

  垃圾收集主要针对的是堆内存和方法区。虚拟机栈、本地方法栈、程序计数器这三个区域是线程私有的，线程结束后会消失，所以不需要进行垃圾收集。

* #### 判断一个对象是否可回收

  * ##### 引用计数法

    为对象添加一个引用计数器，当对象增加一个引用时计数加一，引用失效时减一。引用为零可被回收。

    当两个对象互相引用时永不为零，所以 JVM 不适用引用计数法了。

  * ##### 可达性分析算法

    以 GC Roots 为起始点向下搜索，能引用（可达）到的对象都是存活的，不可达对象被回收。

    JVM 使用该算法来判断对象是否可回收，GC Roots 一般包括以下内容：

    * 虚拟机栈中局部变量表中引用的对象
    * 本地方法栈中引用的对象
    * 方法区中静态资源引用的对象
    * 方法区常量引用的对象

* #### 引用类型

  * ##### 强引用

    被强引用的对象不会被垃圾回收，被 GC Root 直接引用的对象。

    一般使用 new 来创建强引用对象。

  * ##### 软引用

    一些非必须但是还有用的对象，在内存足够时不会回收软引用对象，在内存不够时会回收掉，只有在回收完软引用对象后内存还是不够才会引发内存溢出。GC Root 引用软引用对象，软引用对象引用的对象。

    ```java
    Object obj = new Object();
    SoftReference<Object> sf = new SoftReference<>(obj);
    obj = null;  //使对象只被软引用关联
    ```

  * ##### 弱引用

    只要发生垃圾回收就会被回收，也就是说他只能存活至下次垃圾回收之前。

    ```java
    Object obj = new Object();
    WeakReference<Object> sf = new WeakReference<>(obj);
    obj = null; 
    ```

  * ##### 虚引用

    顾名思义，就是形同虚设，和其他几类引用不同，虚引用不会决定对象的生命周期，也无法通过虚引用引用到一个对象。

    为一个对象设虚引用的唯一目的是用来**跟踪对象被垃圾回收器回收的活动**，它必须和引用队列联合使用，当垃圾回收器回收一个有虚引用的对象时，会将其加入到引用队列中。

    ```java
    Object obj = new Object();
    PhantomReference<Object> pf = new PhantomReference<Object>(obj, null);
    obj = null;
    ```

    

* #### 垃圾收集算法

  ##### 1. 标记-清除

  ![标记-清除](C:\Users\杰\AppData\Roaming\Typora\typora-user-images\image-20200325145823177.png)

  在标记阶段，程序会检查每个对象是否为活动对象，如果是活动对象则会在对象头上打上标记。

  在清除阶段，会进行对象回收和取消标志位，另外，还会判断回收后的分块与前一个空闲块是否连续，若连续则合并两个分块。回收对象就是把对象当作分块，连接到一个叫做“空闲链表”的单向链表。

  分配时从空闲链表上寻找大小合适的分块。

  主要就是先统一标记一遍再统一回收

  缺点：

  ​		标记和清除的效率都不高。

  ​		会产生大量不连续的内存碎片，导致无法给大对象分配内存。

  ##### 2.标记-整理

  ![标记-整理](C:\Users\杰\AppData\Roaming\Typora\typora-user-images\image-20200325152952369.png)

  让所有存活的对象向一端移动，然后直接清理到边界以外的内存。

  优点：

  ​		不会产生内存碎片。

  缺点：

  ​		需要移动大量的对象，处理效率比较低。

  ##### 3.复制

  ![复制](C:\Users\杰\AppData\Roaming\Typora\typora-user-images\image-20200325153137975.png)

  将内存划分为相同大小的两块，每次只是用其中一块，当这一块使用完了就将还存活着的对象复制到另一块去，然后再把使用过的内存已清理。

  主要不足是只使用了一半内存。

  现在虚拟机用的基本都是这种回收算法，但不是划分为相等的两块，是一块较大的 Eden 区和两块 Survivor 区。HotSpot 虚拟机的 Eden 和 Survivor 大小比例默认为8：2。

  ##### 4.分代收集

  一般将堆分为新生代和老年代。

  ​		新生代：采用复制算法。

  ​		老年代：标记-整理或标记-清除。

* #### 垃圾收集器

  ![垃圾收集器](C:\Users\杰\AppData\Roaming\Typora\typora-user-images\image-20200325161426651.png)

  以上是 HotSpot 虚拟机中的7种垃圾收集器，连线表示可以配合使用。

  #### 1.Serial 收集器

  Serial 翻译为串行，说明它是以串行的方式执行的。串行是指垃圾回收器和用户程序交替执行，这意味着进行垃圾回收的时候需要暂停用户程序。

  他是单线程的收集器，只会使用一个线程进行垃圾收集工作。

  优点是简单高效，在单个 CPU 的情况下，由于没有线程交互的开销，因此拥有最高的单线程收集效率。

  他是 client 场景下的默认新生代收集器，因为在该场景下内存一般不会太大，他收集一两百兆垃圾的时间可以控制在一百毫秒内，这点停顿可以接受。

  #### 2.ParNew 收集器

  他是 Serial 收集器的多线程版本。

  他是在 Server 场景下默认的新生代收集器，除了性能原因外，除了 Serial 只有它能与 CMS 收集器配合使用。

  #### 3.Parallel Scavenge 收集器

  也是多线程收集器。
  
  其他收集器的目标是缩短垃圾回收时用户线程停顿的时间，而它的目标是达到一个可控制的吞吐量，**因此被称为”吞吐量优先“收集器**。这里的吞吐量是指 CPU 用于运行用户程序的时间占总时间的比值。
  
  停顿时间越短越适合需要与用户交互的程序，良好的响应速度能够提高用户体验。而高吞吐量可以高效的利用 CPU 时间，进快的完成任务，适合后台运行的程序不太需要交互。
  
  #### 4.Serial Old 收集器
  
  是 Serial 的老年代版本，也是给 Client 场景下的虚拟机使用的。两大用途：
  
  ​		在 JDK 1.5 以前与 Parallel Scavenge 收集器搭配使用。
  
  ​		作为 CMS 收集器的后备预案。
  
  #### 5.Parallel Old 收集器
  
  是 Parallel Scavenge 收集器的老年代版本，注重吞吐量。
  
  #### 6.CMS 收集器
  
  CMS（Concurrent Mark Sweep），Mark Sweep 指的是标记-清除。
  
  CMS 是一种以获取**最短回收停顿时间为目标**的垃圾回收器，非常符合注重用户体验的应用上使用。
  
  分为以下四个过程：
  
  * 初始标记：仅仅标记一下 GC Roots 能直接关联到的对象，速度很快需要停顿。
  * 并发标记：同时开启 GC 线程和用户线程，进行 GC Roots 跟踪的过程，在整个回收中时间最长，不要停顿。
  * 重新标记：修正因为并发标记用户程序执行时导致的标记发生变动的记录，需要停顿。
  * 并发清除：用户线程和 GC 同时工作，不需要停顿。
  
  在整个过程中耗时最长的并发标记和并发清除过程中，GC 线程和用户线程同时运作。
  
  缺点：
  
  * 吞吐量低，低停顿是以牺牲吞吐量为代价的，CPU 利用率不够高。
  * 无法处理用户线程执行时产生的垃圾（浮动垃圾），只能等下一次。因为有浮动垃圾的存在，所以内存要预留一些空间，意味着 CMS 不能等老年代快满了再回收。如果预留空间不够，会出现 Concurrent Mode Failure（并发模式失败），此时开启 Serial Old 替代以下。
  * 标记-清除算法导致有许多空间碎片，往往出现老年代还有剩余但是大对象存不进来而进行 Full GC。
  
  #### 7.G1 收集器
  
  G1（Garage-First），它是一款面向服务器端的垃圾收集器，在多 CPU 和大内存的场景下有很好的性能。堆被分为新生代和老年代，其他收集器都是分开回收，而 G1 可以直接一起回收。
  
  G1 将堆划分为大小相等的独立区域，新生代和老年带不再隔离。
  
  通过引入 Region（区域），将一块内存分为多个小空间，每个小空间单独回收。使得可预测的停顿时间模型成为可能。通过记录每个 Region 垃圾回收所使用的时间和获得的空间，维护一个队列，每次根据允许的时间回收价值最大的 Region。
  
  步骤：
  
  * 初始标记
  * 并发标记
  * 最终标记：修改在并发标记时的变动。此阶段需要停顿线程，但是可以并行执行。
  * 筛选回收：对价值成本排序，指定回收计划。这里停顿用户线程将大幅度提高收集效率。
  
  特点：
  
  * 空间整合：看似是标记-整理，从两个 Region 之间看是复制算法，意味着不会产生碎片。
  * 可预测的停顿：能让使用者在明确指定的 M 毫秒内，消耗在 GC 上的时间不超过 N 毫秒。
  
  

### （三）内存分配与回收策略

* #### Minor GC 和 Full GC 

  * Minor GC 回收新生代，因为新生代对象存活时间较短，因此 Minor GC 会频繁执行，执行速度较快。
  * Full GC 回收老年代和新生代，老年代对象存活时间较长，因此 Full GC 很少执行，执行速度比 Minor GC慢十倍以上。

* #### 内存分配策略

  #### **1.对象优先在 Eden 分配**

  大多数情况下对象会被分配在新生代的 Eden 区，当 Eden 区内存不够时会触发 Minor GC。

  #### 2.大对象直接进入老年代

  大对象是指那种需要连续内存的对象。最常见的就是很长字符串和数组。

  经常会出现大对象触发垃圾收集以后去足够的连续内存空间分配给大对象。

  -XX:PretenureSizeThreshold 大于此值的对象直接进入老年代，避免在 Eden 和 Survivor 区域之间的大量复制。

  #### 3.长期存活的对象直接进入老年代

  为对象设置一个年龄计数器，对象在 Eden 出生并经过 Minor GC 还存活进入 Survivor 区，年龄就为1岁，增加到一定年龄就进入老年代，默认为15岁。

  -XX:MaxTenuringThreshold 用来定义年龄的阈值。

  #### 4.动态对象年龄判定

  虚拟机不是永远要求对象的年龄必须大于 MaxTuringThreshold 才能晋升老年代，当 Survivor 区中同一年龄的对象占了 Survivor 的一半，则年龄大于此年龄的对象直接进入老年代。

  #### 5.空间分配担保

  在进行 Minor GC 之前虚拟机会先检查老年代的连续空间是否大于新生代所有对象的总空间，如果条件成立，则确保 Minor GC 时安全的。

  如果不成立虚拟机会查看 HandlePromotionFailure 是否允许担保失败，允许的话虚拟机再判断老年代的连续空间大小是否大于历来晋升老年代的平均大小，如果大于则尝试进行 Minor GC，如果小于则进行 Full GC。

* #### Full GC 的触发条件

  对于 Minor GC，其触发条件很简单，当 Eden 区空间满了后就会触发。而 Full GC 触发的条件相对复杂。

  #### 1.调用 System.gc();

  只建议虚拟机进行 Full GC，但不一定真正执行。不建议使用这种方式，而实让虚拟机管理内存。

  #### 2.老年代空间不足

  老年代空间不足常见的场景有大对象直接进入老年代、长期存活的对象进入老年代等。

  为了避免因为以上情况发生 Full GC，应当避免创建过大的对象以及过长的数组。也可以通过 -Xmn 参数调大新生代大小，让对象尽量在新生代被手机掉。还可以通过 -XX:MaxTuringThreshold 调大对象进入老年代的年龄，让对象在新生代存活的时间更长一点。

  #### 3.空间分配担保失败

  见上面的第5条。

  #### 4.Concurrent Mode Failure

  执行 CMS GC 的过程中老年代的空间不足（可能时浮动垃圾过多导致的），便会报 Concurrent Mode Failure 错误并且执行 Full GC。

  

### （四）类加载机制

​	类是在运行期间第一次使用时动态加载的，而不是一次性全部加载。如果一次性全部加载，会占用很多内存。

* #### 类的生命周期

  包括以下七个阶段：

  * **加载（Loading）**
  * **验证（Verification）**
  * **准备（Preparation）**
  * **解析（Resolution）**
  * **初始化（Initialization）**
  * 使用（Using）
  * 卸载（Unloading）

* #### 类加载过程

  包含了加载、验证、准备、解析、初始化五个阶段。

  #### 1.加载

  加载是类加载的一个阶段，加载主要完成三件事：

  * 通过类的完全限定类名获取定义该类的二进制字节流。
  * 将字节流中的静态存储结构转化为方法区的运行时存储结构。
  * 在内存中生成一个代表该类的 Class 的对象，作为方法区中该类中的各种数据的入口。

  #### 2.验证

  确保 Class 文件的字节流中包含的信息符合 JVM 规范，以及安全性检查。

  #### 3.准备

  为静态变量分配空间并设置初始值，使用的是方法区的内存。

  实例对象不会在此阶段分配内存，它会在对象实例化时随着对象一起在堆中分配内存。实例化不是类加载的一个过程，类加载只有一次，而实例化可以进行多次。

  初始值一般为 0 值，例如下面的类变量 value 的值被初始化为 0 而不是 123。

  ```java
  public static int value = 123;
  ```

  如果类变量是常量时，就不是 0 了，而是赋值的值，下面的 value 被赋值为 123。

  ```java
  public static final int value = 123;
  ```

  #### 4.解析

  将常量池的符号引用替换为直接引用的过程。

  #### 5.初始化

  初始化阶段才真正开始执行 Java 代码，在初始化阶段才将上述的 value 赋值为123.

* #### 初始化时机

  #### 1.主动引用

  虚拟机规范中没有强制约束何时进行加载，但是在有且只有以下5种情况必须对类进行初始化（加载、验证、准备都随之发生）：

  * 使用 new 关键字创建对象和使用一个类的静态成员、方法时（被 final 修饰的变量除外）。
  * 使用 java.lang.reflect 包的方法对类进行反射调用的时候，如果类还未被初始化，则需要先初始化。
  * 当初始化一个类时，其父类若未被初始化则先初始化父类。
  * 当虚拟机启动时，用户需要指定一个要执行的主类（包含 main() 方法的那个类），虚拟机会先初始化这个主类。
  * 当使用 JDK 1.7 的动态语言支持时，如果一个 java.lang.invoke.MethodHandle 实例最后的解析结果为 REF_getStatic、REF_putStatic、REF_invokeStatic 的方法句柄，并且对应的类未被初始化，则先进程初始化。

  #### 2.被动引用

  除了以上五点其他情况都不会被初始化，成为被动引用，常见例子为：

  * 通过子类引用父类的静态变量，子类不会被初始化。
  * 通过数组定义来引用类，不会触发初始化。如 Test[] tests = new Test[10]。
  * 引用一个类的常量不会导致那个类初始化。

* #### 类加载器分类

  * 启动类加载器（BootstrapClassLoader）：最顶层的类加载器，由 C++ 实现，负责加载 %JAVA_HOME/lib 目录下的 jar 包和类，无法直接访问。
  * 扩展类加载器（ExtensionClassLoader）：负责加载 %JAVA_HOME/lib/ext 目录下的 jar 包和类，用户可以直接使用，上级为 BootstrapClassLoader。
  * 应用程序类加载器（ApplicationClassLoader）：面向用户的类加载器，加载类路径 classpath 下的 jar 包和类。上级为 ExtensionClassLoader。
  * 自定义类加载器。

* #### 双亲委派模式

  每一个类都对应一个它的类加载器。系统中的 ClassLoader 在协同工作时默认使用双亲委派模式。即在类加载时，先判断是否被加载过，已经被加载的类直接返回，否则才尝试加载。加载的时候首先会把请求委派该父类加载器加载，当父类加载器无法处理时，才由自己处理。**当父类加载器为 null 时会使用 BootstrapClassLoader 作为父加载器**。

  ![双亲委派](C:\Users\杰\AppData\Roaming\Typora\typora-user-images\image-20200328182631287.png)

* #### 双亲委派的好处

  双亲委派模型保证了 Java 程序的稳定运行，可以避免类的重复加载（**JVM 区分不同的类不仅仅只依靠类名，相同的类被不同类加载器加载产生的是两个不同的类**），也保证了 Java 的核心 API 不被篡改。

* #### 自定义类加载器

  继承 java.lang.ClassLoader 重写里面的方法。loadClass() 实现了双亲委派，只需重写findClass() 即可。

## 七、JMM

### （一）三个特性

* #### 可见性

  指当一个线程对共享变量进行修改后，其他线程可以立即看到修改后的新值。

* #### 原子性

  在一个或多个操作中，所有操作要么全部都执行，要么全部都不执行。

* #### 有序性

  指代码执行过程中的先后顺序，由于 Java 在编译器和运行期的优化，可能导致代码的顺序发生变化。

## 八、SSM

### （一）IOC 和 AOP

* #### IOC

  IOC（Inverse Of Control，控制反转）是一种设计思想，**将创建对象的控制权，交给框架处理**。IOC 容器是 Spring 用来实现 IOC 的载体，实际上就是一个 Map 其中存放了各种对象。

  将对象之间的复杂依赖关系交给 IOC 容器来管理，并由 IOC 容器实现注入。这样可以很大程度简化开发，IOC 容器就像一个工厂一样，当我们需要一个对象时只需要配置好配置文件，可以不用考虑对象是如何创建出来的。

* #### AOP

  AOP（Aspect-Oriented Programming，面向切面编程），能够将那些与业务无关却有着共同的逻辑和责任的部分（如，事务管理、日志管理、权限管理）封装起来，**减少了代码的重复，降低了耦合性，有利于维护与扩展。**

  Spring 的 AOP 是基于动态代理的，如果代理对象实现了接口，Spring 就会使用 JDK 的 Proxy，而对于没有实现接口的类就不能使用 JDK 的 Proxy，而是使用 cglib。cglib 实现如下：

  ```java
  public static void main(String[] args){
      CglibTest test = (CglibTest)Enhancer.creat(CglibTest.class,MethodInterceptor(){
          
          /**
           * 执行被代理对象的任何方法都会经过此方法
           * @param proxy     代理对象的引用
           * @param method    当前执行的方法
           * @param args      方法参数
           * @param methodProxy   当前执行方法的代理对象
           * @return
           * @throws Throwable
           */
          public Object intercept(Object proxy, Method method, Object[] args,
                                  MethodProxy methodProxy) throws Throwable {
                  System.out.println("前置增强");
                  Object invoke = method.invoke(new CglibTest(), args);
                  System.out.println("后置增强");
                  return invoke;
              }
  }
  ```

* #### Bean的生命周期

  * Bean 容器找到配置文件的 Bean 的定义，实例化 Bean，进行属性注入
  * 如果实现了一系列的接口，如 BeanNameAware、BeanFactoryAware、BeanClassLoaderAware等，先 set 对应的属性
  * 将 Bean 实例交给前置处理器的 postProcessBeforeInitialization()
  * 调用配置文件中配置的 init-method 方法
  * 然后再调用后置处理器的 postProcessAfterInitialization()
  * 使用完 Bean 进行销毁，执行 destroy() 和用户指定的 destroy-method

* #### Spring 的事务传播行为

  * ##### 支持当前事务的情况

    * propagation_required：支持当前事务，如果当前无事务则**创建一个新事务**。
    * propagation_supports：支持当前事务，如果当前无事务则**以非事务执行**。
    * propagation_mandatory：支持当前事务，如果当前无事务则**抛异常**。

  * ##### 不支持当前事务的情况

    * propagation_required_new：创建一个新事物，如果当前有事务则挂起。
    * propagation_not_support：以非事务执行，当前有则挂起。
    * propagation_never：以非事务运行，当前有则抛异常。

  * ##### 其他

    * propagation_nested：当前有事务，则嵌套起来，没有的话新建一个。

* #### Spring MVC 流程

  ![springmvc](C:\Users\杰\AppData\Roaming\Typora\typora-user-images\image-20200331175131542.png)

* #### Mybatis

  #### 1.#{} 和 ${} 的区别？

  答：Mybatis 在处理 #{} 时会会将参数替换为占位符“？”，执行 sql 时用的时PrepareStatement。

  ​		而在处理 ${} 时是直接替换过去。   **使用#{} 可以有效的防止sql 注入**。
  
  #### 3.Mybatis 的原理
  
  加载配置文件，解析得到 Configuration 和 一个个的 MappedStatement（最终被 Executor解析成jdbc的Statement对象） 通过这些创建一个 SqlSessionFactory，由工厂创建 SqlSession ，通过传入的 class 对象利用动态代理创建 Dao 层对应的对象，最终执行 execute 方法。
  
  #### 2.通常一个 XML 文件都对应一个 Dao 层的接口，Dao接口的工作原理是什么？
  
  每一个接口的全限定类名加方法名都能唯一确定一个 MapperStatement ，XML 文件中每一个标签都会被解析为一个 MapperStatement 对象。
  
  #### 3.Mybatis 的一对一和一对多如何实现？
  
  联合查询（表连接）是只查询一次，通过在 resultMap 中设置 association 标签来配置一对一，用 collection 来配置一对多，嵌套查询（子查询）是通过，也是通过 association  和 collection 配置，配置的是 select 属性。
  
  #### 4.延迟加载
  
  只有在一对一或一对多时才能使用延迟加载，如 a 中有个 b 对象，只有在使用到 b 对象时发现为空才会查询 b 对象。用 lazyLoadingEnabled 开启。
  
  #### 5.一级缓存和二级缓存
  
  一级缓存指存在于 sqlSession 中的，当 sqlSession 对象消失时和数据发生变化时清空缓存。
  
  二级缓存是存在于 sqlSessionFactory 中的，同一个工厂的 sqlSession 都能使用（即同一个 namespace 下的sqlSession 都能使用），需要用 setting 中 cacheEnable 和 cache 标签开启。
  
  



## 九、数据结构与算法

### （一）图

* 定义：图是由一组顶点和顶点之间相连接的边组成的
* 分类：有向图和无向图
* 术语：
  * 相邻顶点：当两个顶点由一条边连接，则成这两个顶点为相邻顶点
  * 度：某个顶点的度就是依附于这个点的边的个数
  * 连通图：如果每个顶点都存在路径通往其他顶点，则成为这个图为连通图
* 图的存储结构：
  * 邻接矩阵：由二位数组构成，若顶点 i 和 j 相连则在矩阵中 arr[i][j] = 1，缺点是费空间，无向图中有一半都是重复。
  * 邻接表：由数组加链表构成，数组的索引代表对应的顶点，数组的值存放一个链表代表与之连接的顶点
* 实现：

```java
public class Graph {
    //顶点数量
    private final int V;
    //边的数目
    private int E;
    //邻接表
    private Queue<Integer>[] adj;

    public Graph(int V) {
        this.V = V;
        this.E = 0;
        for (int i = 0; i < adj.length; i++) {
            adj[i] = new LinkedList<>();
        }
    }

    //获取顶点个数
    public int V() {
        return V;
    }

    //获取边的个数
    public int E() {
        return E;
    }

    //像途中添加一条边
    public void addEdge(int x, int y) {
        adj[x].add(y);
        adj[y].add(x);
        //边的数量+1
        E++;
    }

    //获取顶点v相邻的所有顶点
    public Queue<Integer> adj(int v) {
        return adj[v];
    }
}
```

### （二）并查集

**实现：**

```java
public class UnionCollection {

    //记录有多少个分组
    public int count;
    //下表索引代表元素，索引处的值代表根节点的索引
    public int[] element;
    //代表每个元素所在组的长度
    public int[] size;

    public UnionCollection(int n) {
        count = n;
        element = new int[n];
        size = new int[n];
        for (int i = 0; i < n; i++) {
            element[i] = i;
            size[i] = 1;
        }
    }

    //返回当前的组数
    public int count() {
        return count;
    }

    //找到元素的根节点
    public int find(int p) {
        while (true) {
            if (p == element[p])
                return p;
            p = element[p];
        }
    }

    //判断两个元素是否在一组中
    public boolean connected(int p, int q) {
        return find(p) == find(q);
    }

    //合并两个组元素
    public void union(int p, int q) {
        int proot = find(p);
        int qroot = find(q);

        //让较小的组连接到较大的组上，效率更佳
        if (proot == qroot)
            return;
        if (size[proot] > size[qroot]) {
            element[qroot] = proot;
            size[proot] += size[qroot];
        } else {
            element[proot] = qroot;
            size[qroot] += size[proot];
        }
        count--;
    }
}
```







