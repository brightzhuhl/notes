# Java知识总结

## Java基础

### 1.面向对象的基本特征

​	封装、继承、多态

### 2.多态介绍

​	字面意思是一个对象可以有多种形态。

​	编程中表现为允许将子类对象指针赋值给父类对象引用，调用方法时根据子类对应方法实现的不同而呈现不同效果。

#### 2.1如何实现多态

​	多态的必要特征是：继承、重写方法、父类引用指向子类。

​	Java中的实现方式有：方法重写、接口、抽象类和抽象方法。

#### 2.2重写和重载的区别

​	重写是指子类继承父类时重新编写方法逻辑以实现多态。

​	重载是指同一个类中，可以存在方法名相同但是形参不同、返回值可以相同也可以不同的方法。

### 3.Java基本数据类型

​	byte(1) char(2) short(2) int(4) float(4) long(8) double(8) boolean

#### 3.1.char和short的区别

​	char是16位无符号整型，表示范围为`0-65535`

​	short是16位有符号整型，表示范围为`-32768～32767`

#### 3.2 原码、反码、补码介绍

​	原码：最高位为符号位，0为正，1为负。如-127用8位原码表示为：`1111`  `1111` ，原码的表示范围为`-2^(n-1)` 到 `2^(n-1)`

​	反码：正数的反码和原码一致，负数反码最高位符号位为1，其余各位为原码取反。如-127用8位反码表示为：`1000` `0000` ，反码的表示范围为`-2^(n-1)` 到 `2^(n-1)`

​	补码：正数的补码和原码一致，负数的补码为__反码加1__。如-127用8位补码表示为`1000` `0001`

#### 3.3 IEEE754标准介绍

### Object

#### equals和hashcode

​	equals方法规约：

 - 自反性：对于任何非空引用`x`，`x.equlas(x)`应该返回`true`

 - 对称性：对于任何非空引用`x`、`y`，当且仅当`x.equals(y) == true` 时 `y.equals(x) == true`

 - 传递性：对于任何非空引用`x`、`y`、`z`，若`x.equals(y) == true` 并且 `y.equals(z) == true`，则 `x.equals(z) == true`

 - 一致性：对于任何非空引用`x`、`y`，只要未修改`equal`中用于比较的属性值，那么多次调用`x.equals(y)`应该始终返回相同的结果

 - 对于任何非`null`值`x`，`x.equals(null)`应该始终返回`false`

 - 对于任何非空引用`x`、`y`，当且仅当`x`和`y`引用同一个对象（x == y的值为true）时，此方法才返回true

   __当重写本方法被重写时，通常必须重写hashCode方法，以保证遵守hashCode方法的规约，即相等的对象一定要有相等的hashCode__

   

   hashCode方法规约：

- 一个Java程序的一次执行过程中，多次调用对象的`hashCode()`方法必须一致地返回同一个整数，前提是对象的`equals()`方法中比较用到的信息没有被修改。从这个Java程序的一次执行过程到另一次执行过程，`hashCode()`方法返回的这个整数无需保持一致
- 如果根据`equals()`方法知道两个对象相等，那么分别调用这两个对象的`hashCode()`方法也要一定要产生一样的结果
- 如果根据`equals()`方法两个对象不相等，那么不必保证分别调用两个对象的`hashCode()`方法一定要产生不同的结果。然而，编程人员要注意：不相等的两个对象有不同的`hashCode`可能会提高`hash tables`的性能。

### 字符串

#### String常量池介绍

​	String类内部维护了一个常量池，调用intern方法可以将当前字符串加入常量池。

​	所有的字符串字面量在编译时都会自动调用intern方法加入常量池。



#### StringBuilder和StringBuffer的区别

​	`StringBuffer`是Jdk1.1开始提供的可变字符串操作类，线程安全。

​	`StringBuilder`是Jdk1.5开始提供的一个兼容`StringBuffer`的Api的可变字符串操作类，非线程安全，性能比`StringBuffer`更高

## JDK

### 集合框架

#### 集合框架简介

​	 Jdk的集合框架常用接口：

- List：列表，有序元素序列，常用子类：ArrayList、LinkedList、Vector
- Set：不重复元素集合，常用子类：HashSet、TreeSet
- Map：键值对映射，Map不能包含重复的key，常用子类：HashMap、TreeMap、HashTable
- Queue：队列

#### ArrayList和LinkedList的区别

​	ArrayList采用数组实现，LinkedList采用双向链表实现。因此ArrayList随机访问效率高，时间复杂度为O(1)，而LinkedList随机访问的时间复杂度为O(n)；LinkedList容量限制小，一般不会有扩容的开销；而ArrayList达到容量限制后需要扩容，时间复杂度为O(n)；内存使用上，ArrayList需要预留数组空间以供追加元素，而LinkedList每个节点需要额外的空间存储前驱和后继节点。

​	综上，ArrayList适合已知容量、扩容不频繁、查询频繁的场景；而LinkedList适合未知容量、频繁添加、顺序访问的场景。

#### HashMap和HashTable的区别

​	HashMap是线程安全的，而HashTable不是线程安全的，HashTable的方法有synchronize修饰，效率更低；HashTable支持null作为key或者value

#### HashMap的实现

​	HashMap根据哈希算法的思想来实现，它需要一个hash算法将元素映射到一个指定的位置以对数组进行直接存取。HashMap存放元素的步骤如下：

1. hash：计算key的hashcode，并将hashcode高低位混淆均匀（主要是为了减少某些特殊情况下的hash冲突）
2. mask：将hashcode取余，计算表达式为hashcode & n（n为散列表的大小），该结果作为存放的数组下标
3. putVal：查询该下标是否已有值，如果没有值则新增一个链表节点，并将当前元素设为链表节点值。如果有值且节点为TreeNode类型，则将当前值加入TreeNode中。否则，将当前元素插入为对应链表节点的next节点，插入后如果链表长度超过了8则需要将链表转为树结构。
4. treeifyBin：如果当前散列表的大小小于64，则需要将散列表扩容，并将元素重新散列。否则将列表转为红黑树。

HashMap每次扩容为原来的2倍，大小总为2的n次幂。HashMap的扩容时机有两个，第一是发生hash冲突链表转为树结构时，第二是插入元素后元素总数量大于承载阈值时，该承载阈值初始值为HashMap初始容量*负载因子，每次扩容该承载阈值也会扩大为原来的2倍

#### TreeMap的实现

​	TreeMap是NavigableMap的红黑树实现。它不同的构造方法可以根据key自然排序或者根据给定的Comparator来排序。TreeMap的containsKey、get、put、remove方法的时间复杂度都是log(n)

#### ConcurrentHashMap

​	ConcurrentHashMap使用分段锁实现，它由多个segment组成，每个segment可以同时被一个线程操作，而HashTable同时只能被一个线程操作。

### 多线程

#### 进程、线程和协程的区别

进程是操作系统中的一个概念，它是为了更好地描述和控制程序并发执行，实现操作系统的并发性和共享性而提出的。为了使并发执行的程序可以独立运行，必须为之配置一个专门的数据结构称为**进程控制块（Process Control Block，PCB）**，操作系统利用PCB描述进程的基本情况和运行状态，进而控进程。而相应地，将PCB、程序段、相关数据段三部分称为**进程映像**。我们可以把进程定义为“**进程是进程映像的运行过程，是操作系统分配资源的独立单位**”

**进程的三个主要状态包括：**

- 就绪状态：进程已处于等待运行的状态，已经获取了处理器之外的所有资源，获取到处理器时间片即可运行
- 运行状态：进程只在处理器中运行
- 阻塞状态：也叫等待状态，进程因等待某种资源而暂停运行，如输入输出，此时即使获取到处理器时间也无法运行



线程直接的理解就是“**轻量级线程**”，引入线程是为了减少程序并发执行时的时空开销，提高系统的并发性能。线程是一个基本的CPU执行单元，也是程序执行流的最小单位。线程属于进程，但线程并不拥有资源，一个进程可以包含多个线程，各个线程可以共享进程的资源。



协程暂时没有使用过。

#### 守护线程和非守护线程的区别

JVM会等待非守护线程结束而结束，而不会等待守护线程。

#### 实现多线程的方式和区别

- 继承Thread类，重写run方法。
- 实现Runnable接口，再将实现类实例传递给Thread类的构造函数或将实例提交到线程池执行，这种方式更灵活，可以不受Java单继承机制的约束

#### 线程的状态

- NEW：初始状态，调用new Thread()，但是还没有调用start()方法
- READY：就绪状态，调用线程的start()方法
- RUNNING：运行状态，虚拟机线程调度程序选择一个线程运行
- BLOCKED：阻塞状态，synchronize或Lock等待获取锁
- WAITING：等待状态，调用了线程的wait、join、LockSupport.park方法，需要调用notify、notifyAl、LockSupport.unpark(Thread)才能唤醒
- TIMED_WAITING：超时等待状态，调用了sleep(long)、wait(long)、join(long)、parkNanos、parkUntil
- TERMINATED：终止状态，线程执行完成

#### 线程池简介

#### ThreadPoolExecutor

​	**新建线程池的参数：**

- **corePoolSize（线程池的基本大小）**：当提交一个任务到线程池时，线程池会创建一个线程来执行任务，即使其他空闲的基本线程能够执行新任务也会创建线程，等到需要执行的任务数大于线程池基本大小时就不再创建。如果调用了线程池的prestartAllCoreThreads方法，线程池会提前创建并启动所有基本线程。

- **maximumPoolSize（线程池最大大小）**：线程池允许创建的最大线程数。如果队列满了，并且已创建的线程数小于最大线程数，则线程池会再创建新的线程执行任务。值得注意的是如果使用了无界的任务队列这个参数就没什么效果。

- **runnableTaskQueue（任务队列）**：用于保存等待执行的任务的阻塞队列。

- **ThreadFactory**：用于设置创建线程的工厂，可以通过线程工厂给每个创建出来的线程设置更有意义的名字，Debug和定位问题时非常又帮助。

- **RejectedExecutionHandler（拒绝策略）**：当队列和线程池都满了，说明线程池处于饱和状态，那么必须采取一种策略处理提交的新任务。这个策略默认情况下是AbortPolicy，表示无法处理新任务时抛出异常。以下是JDK1.5提供的四种策略。n  AbortPolicy：直接抛出异常。

- **keepAliveTime（线程活动保持时间）**：线程池的工作线程空闲后，保持存活的时间。所以如果任务很多，并且每个任务执行的时间比较短，可以调大这个时间，提高线程的利用率。

- **TimeUnit（线程活动保持时间的单位）**：可选的单位有天（DAYS），小时（HOURS），分钟（MINUTES），毫秒(MILLISECONDS)，微秒(MICROSECONDS, 千分之一毫秒)和毫微秒(NANOSECONDS, 千分之一微秒)。

**线程池执行策略：**

1. 线程数量未达到corePoolSize，则新建一个线程(核心线程)执行任务
2. 线程数量达到了corePoolSize，则将任务移入队列等待
3. 队列已满，总线程数未达到maximumPoolSize，新建线程(非核心线程)执行任务
4. 队列已满，总线程数又达到了maximumPoolSize，就会由(RejectedExecutionHandler)抛出异常

**线程池拒绝策略：**

1. AbortPolicy：不执行新任务，直接抛出异常，提示线程池已满，线程池默认策略
2. DiscardPolicy：不执行新任务，也不抛出异常，基本上为静默模式。
3. DisCardOldSetPolicy：将消息队列中的第一个任务替换为当前新进来的任务执行
4. CallerRunPolicy：用于被拒绝任务的处理程序，它直接在 execute 方法的调用线程中运行被拒绝的任务；如果执行程序已关闭，则会丢弃该任务。

**任务队列常用实现：**

- ArrayBlockingQueue：是一个基于数组结构的有界阻塞队列，此队列按FIFO原则对元素进行排序。
- LinkedBlockingQueue：是一个基于链表结构的阻塞队列，此队列按照FIFO排序元素，吞吐量通常高于ArrayBlockingQueue。静态工厂方法Executors.newFixedThreadPool()使用了这个队列
- SynchronousQueue：一个不存储元素的阻塞队列。每个插入操作必须等到另一个线程调用移除操作，否则插入操作一直处于阻塞状态，吞吐量通常高于LinkedBlockingQueue，静态工厂方法Executors.newCachedThreadPool使用了这个队列
- PriorityBlockingQueue：一个具有优先级的阻塞队列

#### volatile

Java支持多个线程同时访问一个对象或者对象的成员元变量，由于每个线程可以拥有这个变量的拷贝（虽然对象以及成员变量分配的内存是在共享内存中的，但是每个执行的线程还是可以拥有一份拷贝，这样做的目的时加速程序的执行，这是现代多核处理器的一个显著特性）所以程序在执行过程中，一个线程看到的变量不一定是最新的。

volatile关键字可以用来修饰字段（成员变量），就是告知程序任何对该变量的访问都均需要要从共享内存中获取，而对它的改变必须同步刷新到共享内存，它能保证所有线程对变量访问的可见性。

### 锁相关

#### 乐观锁和悲观锁

- 悲观锁：在多线程并发操作共享数据时，总是假定最悲观的情况，即总是会发生冲突，所以在操作共享数据时先对数据加锁防止其它线程使用。
- 乐观锁：在多线程并发操作共享数据时，总是假定最乐观的情况，即认为不会发生冲突，但是在更新时会判断数据是否已被修改。

数据库中的乐观锁通常使用版本号来实现，JDK中乐观锁使用Unsafe类提供的CAS（Compare and Swap）算法相关操作实现。

悲观锁适合在并发冲突严重的场景中使用，而乐观锁偏向于在写数据冲突不严重的场景使用。乐观锁会出现ABA问题，使用版本号可以避免该问题，JDK中可以使用AtomicStampedReference。

#### 死锁

死锁是指程序并发执行时多个进程（或线程等）因竞争资源而造成了一种僵局（互相等待资源），若无外力作用，所有的进程都无法推进。

**死锁产生的必要条件：**

- 互斥条件：进程要求对所分配的资源进行排他性控制。即一段时间内资源只能被某一个进程所占有，而此时其它请求该资源的对象只能等待。
- 不可剥夺条件：进程所获取的资源在未使用完毕之前不能被其它资源强行夺走，自能主动释放。
- 请求和保持条件：进程已经保持了至少一个资源，但又提出了新的资源请求，但该资源已被其它进程所占有。
- 循环等待条件

#### synchronize

在多线程并发编程中synchronized一直是元老级的角色，很多人都会称呼它为重量级锁。synchronized实现同步的基础是：Java中的每一个对象都可以作为锁。具体表现为以下三种形式：

- 对于普通同步方法，锁是当前实例对象
- 对于静态同步方法，锁是当前类的Class对象
- 对于同步方法块，锁是synchronized括号中配置的对象。

从JVM规范中可以看到synchronized的实现原理：基于进入和退出Monitor对象。同步代码块使用的是monitorenter和monitorexit指令，而同步方法使用其它方式，其细节并未在JVM规范中明确，但也可以使用这两个指令实现。

synchronized使用的锁是存在于Java对象头中的。如果对象那个是数组类型，则虚拟机用三个字宽存储对象头，非数组类型为2个字宽。在32位虚拟机中1字宽为4字节。

**Java对象头的长度**

| 长度     | 内容                   | 说明                                   |
| -------- | ---------------------- | -------------------------------------- |
| 32/64bit | Mark Word              | 存储对象的hashCode、分代年龄、锁信息等 |
| 32/64bit | Class Metadata Address | 存储到对象类型数据的指针               |
| 32/64bit | Array length           | 数组长度（如果当前对象为数组）         |

##### 偏向锁

大多数情况下，锁不存在多线程竞争，总是由同一个线程多次获得，为了让线程获得锁的代价更低而引入了偏向锁

![](https://raw.githubusercontent.com/zhlzzzzz/notes/master/epub_681633_14.jpeg)

##### 轻量级锁

很多时候同步代码块的执行时间是很短的，因此为了减少多线程竞争锁时的线程调度的消耗，并不挂起线程，而是让线程自旋来获取锁。

**轻量级锁机锁**

线程在执行同步块之前，JVM会现在当前线程的栈桢中创建用于存储锁记录的空间，并将对象头中的Mark Word复制到锁记录空间中，官方称之为Displaced Mark Word。然后线程藏式使用CAS将对象头中的Mark Word替换为指向锁记录的指针。如果成功，当前线程获取锁，如果失败，表示其他线程竞争锁，当前线程便尝试使用自旋来获取锁

**轻量级锁解锁**

轻量级锁解锁时，会使用院子的CAS操作将Displaced Mark Word替换回到对象头重，如果成功，表示没有竞争发生。如果失败，表示当前锁存在竞争，锁就会膨胀为重量级锁。

![](https://raw.githubusercontent.com/zhlzzzzz/notes/master/epub_681633_15.jpeg)

##### 重量级锁

锁竞争会挂起线程

**各种锁的比较**

| 锁       | 优点                                                         | 缺点                                           | 适用场景                           |
| -------- | ------------------------------------------------------------ | ---------------------------------------------- | ---------------------------------- |
| 偏向锁   | 加锁和解锁不需要额外的消耗，和执行非同步方法相比仅存在纳秒级的差别 | 如果线程见存在锁竞争，会带来额外的锁撤销的消耗 | 适用于只有一个线程访问同步块的场景 |
| 轻量级锁 | 竞争的线程不会阻塞，提高了程序的响应速度                     | 如果始终得不到锁竞争的线程，自旋会消耗CPU      | 追求响应时间，同步块执行速度非常快 |
| 重量级锁 | 线程竞争不使用自旋，不会消耗CPU                              | 线程阻塞，响应慢                               | 追求吞吐量，同步块执行时间长       |

#### Java中的Lock接口

| 特性               | 描述                                                         |
| ------------------ | ------------------------------------------------------------ |
| 尝试非阻塞地获取锁 | 当前线程尝试获取锁，如果这一时刻锁没有被线程获取到，则成功获取并持有锁 |
| 能被中断地获取锁   | 获取锁的线程能够响应中断，当获取到锁的线程被中断时，中断异常会被抛出，同时锁会被释放 |
| 超时获取锁         | 在指定的截止时间之前获取锁，如果截止时间到了仍然无法获取锁，则返回 |

##### 队列同步器

一个用于实现锁的帮助类

##### 重入锁（ReentrantLock）

支持一个线程对资源重复加锁，而不会被自己阻塞。除此之外，该锁还支持获取锁时的公平和非公平的选择。

公平锁，即先对锁进行获取的请求一定先被满足，反之为非公平锁

##### 读写锁（ReadWriteLock）

读锁为非拍它锁，写锁为拍它锁

##### Condition接口

提供一组类似对象上监视器方法

## JVM

### 类加载机制

​	javac编译器将代码编译成class文件，当java程序运行时，jvm需要将类描述数据从class文件解析到内存中，并对数据进行校验、转化解析、初始化。这个过程叫做jvm的类加载机制。总共包括五个阶段：

- 加载：通过类的全名获取class文件的二进制字节流。
- 验证：验证类结构是否正确。
- 准备：为静态常量分配内存并赋初始值
- 解析：将二进制符号引用转为直接引用
- 初始化：为静态变量赋程序设定值。

  **Java虚拟机规范规定了有且仅有5种情况必须对类初始化：**

1. 使用new字节码指令创建类的实例，或者使用getstatic、putstatic读取或设置一个静态字段的值（放入常量池中的常量除外），或者调用一个静态方法的时候，对应类必须进行过初始化。
2. 通过java.lang.reflect包的方法对类进行反射调用的时候，如果类没有进行过初始化，则要首先进行初始化
3. 当初始化一个类的时候，如果发现其父类没有进行过初始化，则首先触发父类初始化。
4. 当虚拟机启动时，用户需要指定一个主类（包含main()方法的类），虚拟机会首先初始化这个类。
5. 使用jdk1.7的动态语言支持时，如果一个java.lang.invoke.MethodHandle实例最后的解析结果REF_getStatic、REF_putStatic、RE_invokeStatic的方法句柄，并且这个方法句柄对应的类没有进行初始化，则需要先触发其初始化。

这5种情况称为**“主动引用”**，除了这五种情况，其它情况都不会触发类的初始化，被称为**“被动引用”**

#### 类加载器简介

1. Bootstrap ClassLoader：启动类加载器，也叫根类加载器，它负责加载Java的核心类库，加载如(%JAVA_HOME%/lib)目录下的rt.jar（包含System、String这样的核心类）这样的核心类库。根类加载器非常特殊，它不是java.lang.ClassLoader的子类，它是JVM自身内部由C/C++实现的，并不是Java实现的
2. Extension ClassLoader：扩展类加载器，它负责加载扩展目录(%JAVA_HOME%/jre/lib/ext)下的jar包，用户可以把自己开发的类打包成jar包放在这个目录下即可扩展核心类以外的新功能。
3. System ClassLoader\APP ClassLoader：系统类加载器或称为应用程序类加载器，是加载CLASSPATH环境变量所指定的jar包与类路径。一般来说，用户自定义的类就是由APP ClassLoader加载的。

#### 双亲委派加载机制

​	当一个类收到了类加载请求，他首先不会尝试自己去加载这个类，而是把这个请求委派给父类去完成，每一个层次类加载器都是如此，因此所有的加载请求都应该传送到启动类加载其中，只有当父类加载器反馈自己无法完成这个请求的时候（在它的加载路径下没有找到所需加载的Class），子类加载器才会尝试自己去加载。

### 内存模型

jvm内存主要有三大区域：堆、栈和方法区；其中栈分为本地方法栈和虚拟机栈。

- 方法区：线程共享，它用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。
- 程序计数器：线程私有，是一块较小的内存空间，它的作用可以看做是当前线程所执行的字节码的行号指示器。
- 虚拟机栈：线程私有，生命周期和线程相同。
- 本地方法栈：与虚拟机栈类似，为native方法服务。
- 堆：最大的内存区域，线程共享，几乎所有对象实例都在这里分配内存。

### 垃圾回收

#### 如何判断对象死亡

1. 引用计数器法：为对象添加一个引用计数器，每当有引用则+1；引用失效则-1。引用计数为0则对象不再被使用。缺点是无法解决循环依赖问题。
2. 可达性分析法：从GCRoot对象向下搜索，经过的节点路线称为引用链，当一个对象没有和任何引用链相连，则对象是不可用的。

#### 垃圾回收算法

- 标记-清除算法：先标记所有需要清理的对象，标记完成后统一回收。缺点是会带来内存碎片
- 复制算法：内存分为两个块，一块作为当前使用。垃圾回收时将可用对象复制到另一块区域，然后清除当前使用的所有内存。缺点是只能使用一半内存。
- 标记-整理算法
- 分代收集算法：将内存分为多个区域，根据每个区域的特点应用不同的垃圾回收算法。比如目前大多虚拟机都将堆内存分为新生代、老年代等。



## MySQL

### 简介

#### sql语句类型

- DDL语句（Data Definition Language）：数据定义语句，常用包括create、drop、alter等
- DML语句（Data Manipulation Language）：数据操纵语句，常用包括insert、update、delete、select等
- DCL语句（Data Control Language）：数据控制语句，常用包括grant、revoke等

### 索引

#### 索引类型

- 普通索引
- 唯一索引
- 全文索引
- 组合索引

#### 什么情况索引会失效

- 如果是复合索引，没有使用第一列
- like查询时不是前缀匹配
- or条件查询中有条件没有索引
- where中进行null判断
- where中使用!=、<>等操作符
- =左操作数使用函数。

### 事务

### 锁

### 日志

### 性能优化和分布式

### InnoDB集群

#### 简介

InnoDB集群提供了完整的MySQL高可用解决方案。MySQL Shell包括提供了AdminAPI可以轻松配置和管理一组包括至少三个MySQL服务器的InnoDB集群。每个MySQL实例运行着MySQL Group Replication，它提供了一种在InnoDB集群间复制数据的机制，而且内建了故障转移能力。MySQL Router可以给予部署的集群进行自动配置，透明地将客户端程序连接到服务器。当集群中的实例遇到不可预料的错误时，集群回自动重新配置。在默认的单主模式中，InnoDB集群有一个可读写的主节点。多个副节点作为主节点的备份。如果主节点失败，那么会有一个副节点晋升为主节点。MySQL Router会检测到这个情况并且将客户端实例重定向到新的主节点。

#### **InnoDB体系结构**

![](https://raw.githubusercontent.com/zhlzzzzz/notes/master/innodb_cluster_overview.png)

#### AdminAPI

MySQL Shell包括了AdminApi，可以通过`dba`全局变量和它的相关方法来访问。`dba`变量的方法允许你部署、配置和管理InnoDB集群。例如，使用`dba.createCluster()`方法可以创建InnoDB集群。

MySQL Shell为AdminAPI提供了在线帮助。要列出所有可用的dba命令，可以使用dba.help()方法。要获取特定方法的在线帮助，使用`object.help('methodName')`。例如：

```shell
mysql-js> dba.help('getCluster')

Retrieves a cluster from the Metadata Store.

SYNTAX

  dba.getCluster([name][, options])

WHERE

  name: Parameter to specify the name of the cluster to be returned.
  options: Dictionary with additional options.

RETURNS

  The cluster object identified by the given name or the default cluster.

DESCRIPTION

If name is not specified or is null, the default cluster will be returned.

If name is specified, and no cluster with the indicated name is found, an error
will be raised.

The options dictionary accepts the connectToPrimary option,which defaults to
true and indicates the shell to automatically connect to the primary member of
the cluster.

EXCEPTIONS

  MetadataError in the following scenarios:

   - If the Metadata is inaccessible.
   - If the Metadata update operation failed.

  ArgumentError in the following scenarios:

   - If the Cluster name is empty.
   - If the Cluster name is invalid.
   - If the Cluster does not exist.

  RuntimeError in the following scenarios:

   - If the current connection cannot be used for Group Replication.
```

#### 创建InnoDB集群

##### 部署场景

InnoDB 集群支持下列部署场景：

-  生产部署：如果你想在完全的生产环境中使用InnoDB集群，你需要配置所需数量的机器然后部署服务实例到机器上。生产部署允许你利用到InnoDB集群高可用能力的全部潜力。
- 沙盒部署：如果你想在提交到完全的生产环境之前测试InnoDB集群，沙盒部署的特性允许你在本地机器快速设置集群。沙盒实例以所需的配置创建

**注意：沙盒部署不适合在完全的生产环境中使用**

##### InnoDB集群所需条件

在安装InnoDB集群的生产部署之前，确保这些服务实例满足下列要求：

- InnoDB集群使用Group Replication，因此你的服务实例必须满足同样的要求。AdminAPI提供了`dba.checkInstanceConfiguration()`方法来验证实例是否满足Group Replication的要求，`dba.configurationInstance()`方法可以配置实例来满足所需要求。

  **注意：当使用沙盒部署时实例将自动配置为满足所需要求**

- Group Replication成员可以包含使用InnoDB引擎以外的表，例如MyISAM。这些表不能通过Group Replication写入，因此当使用InnoDB集群时，为了这些表可以通过InnoDB集群写入，要将这些表转为InnoDB引擎。

- 必须启用Perfomance Shema

- MySQL Shell用来配置InnoDB集群的脚本需要访问Python。Windows MySQL Shell包含了Python因此不需要额外的用户配置。在Unix中Python必须作为shell环境的一部分。要检测你的系统是否正确配置了Python可以执行：

  ```/usr/bin/env Python```

- 从8.0.17版本开始，InnoDB集群的实例必须使用唯一的server_id。当你使用`Cluster.addInstance(instance)`操作时，如果实例的server_id已在集群中使用，那么操作将失败。

##### 安装方法

安装InnoDB集群的方法取决于你想要的部署类型。沙盒部署将所有InnoDB集群的所有组件安装到单个机器上，只需要在本地机器执行一次安装即可。生产部署的安装需要在集群的每一台机器中执行一次。以下的安装InnoDB集群的方法都是可用的：

- 下载和安装下列组件
  -  MySQL Server
  - MySQL Shell
  - MySQL Router
- 在Windows上你可以使用沙盒部署的Windows MySQL Installer

## Redis

### 基本数据类型

- string：字符串
- hash：散列表
- list：列表
- set：集合
- sorted set：有序集合

### 持久化

### 分布式锁

### Key过期清理策略

- 立即清除
- 定时清除
- 惰性清除

## ElasticSearch

### 常用索引类型

- Arrays
- Binary
- Boolean
- Date
- Text

## MQ



## Linux

### 常用命令

# 参考书籍

- [Java语言规范（Java SE 8）](https://docs.oracle.com/javase/specs/jls/se8/html/index.html)
- 深入理解Java虚拟机：JVM高级特性与最佳实践（第2版）——周志明
- Java并发编程的艺术——方腾飞、魏鹏、程晓明
- 计算机组成原理——白中英、戴志涛



sjdlkajdkljkald