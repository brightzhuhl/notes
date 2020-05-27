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

### 分库分表

#### 目的

分库和分表是两个独立的概念。分库分表都是为了防止数据库服务因为同一时间的访问量过大导致宕机的一种应对策略。

#### mysqlslap

mysqlslap是MySQL自带的一个性能测试工具。mysqlslap运行参数可以从mysql默认配置文件中读取。

## Redis

Redis是一个基于BSD协议开源的键值对内存数据存储，可用作数据库、缓存和消息代理。

Redis的优势有：

- 基于内存操作，效率极高，读写都很快，支持10w QPS并发
- 单线程模型，线程安全，采用IO多路复用机制
- 支持丰富的数据类型
- 支持数据持久化
- 支持主从复制，哨兵，高可用
- 支持发布/订阅机制，可用作消息中间件

### 基本数据类型

Redis内部使用一个RedisObject对象来表示所有的key和value。

- string：字符串
- hash：散列表
- list：列表
- set：集合
- sorted set：有序集合

### 缓存

#### Key过期清理策略

- 立即清除
- 定时清除
- 惰性清除

#### 缓存淘汰策略

- noeviction：当达到内存限制时，如果客户端还尝试执行消耗更多内存的命令会返回错误
- allkeys-lru：淘汰最近最少使用的key
- volatile-lru：设置了过期时间的key之中淘汰最近最少使用的key
- allkeys-random：随机淘汰
- volatile-random：在设置了过期时间的key中间随机淘汰
- volatile-ttl：淘汰设置了过期时间的key，并且尝试淘汰具有更短的存活时间的key

#### 缓存雪崩

当原有缓存失效，新缓存还未更新（例如大量缓存设置了童颜的过期时间，同一时刻大量缓存失效），导致大量请求同时访问数据库而对数据库造成巨大压力，严重时可能宕机。从而形成一系列连锁反应，导致系统崩溃。

**解决方案：**

采用加锁或者队列的方式保证不会有大量的线程对数据库一次性读写，从而避免大量并发请求落到数据库上。获取将缓存失效时间分散开。

#### 缓存穿透

当用户查询数据在数据库中不存在时，因此也没有缓存到Redis中，这样每次用户查询时都要去查询数据库。

**解决办法：**

- 使用布隆过滤器，将存在的数据存放到过滤器中，查询时不存在的数据可以预先拦截掉。
- 不管数据库中查询的结果是否为空，都把数据放到缓存中，但是空数据的过期时间设置为较短。

#### 缓存击穿

一个key为热点数据时，大量并发集中访问这个key，当这个key失效瞬间，会有大量并发穿透缓存，转而直接请求数据库。

**解决方案：**

- 分级缓存：采用L1和L2两级缓存方式，L1缓存失效时间短，L2失效时间长。请求有限从L1缓存获取数据，如果L1未命中
- 更新缓存加锁，避免多个线程同时从数据库加载数据。

#### 缓存一致性问题

由于更新数据库和操作缓存两个操作不是原子操作，因此在高并发情况下可能会出现问题。需要根据不同的场景调整更新数据库和操作缓存的顺序

#### 缓存降级

### 持久化

#### RDB

Redis DataBase，在给定的时间间隔对数据进行快照存储

##### 配置

```shell
# 时间策略
save 900 1
save 300 10
save 60 10000

# 文件名称
dbfilename dump.rdb

# 文件保存路径
dir /home/work/app/redis/data/

# 如果持久化出错，主进程是否停止写入
stop-writes-on-bgsave-error yes

# 是否压缩
rdbcompression yes

# 导入时是否检查
rdbchecksum yes

```

##### 执行机制 

- 手动触发
  - save：会阻塞当前Redis服务器，知道持久化完成，线上应该禁止使用
  - bgsave：改触发方式会fork一个子进程，由该子进程负责持久化过程，因此阻塞只会发生在fork子进程时
- 自动触发：
  - 根据`save m n `配置
  - 从节点全量复制时，主节点发送rdb文件给从节点完成复制操作，主节点会触发`bgsave`
  - 执行`debug reload`时
  - 执行`shutdown`时，如果没有开启aof，也会触发

#### AOF

Append-only file，记录每次对服务器的写操作的日志，当服务器重启的时候会重写执行这些命令来恢复原始的数据

##### 配置

```shell
# 是否开启aof
appendonly yes

# 文件名称
appendfilename "appendonly.aof"

# 同步方式
appendfsync everysec

# aof重写期间是否同步
no-appendfsync-on-rewrite no

# 重写触发配置
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb

# 加载aof时如果有错如何处理
aof-load-truncated yes

# 文件重写策略
aof-rewrite-incremental-fsync yes
```

#### 备份恢复

恢复数据时只需要重新启动redis即可，流程如下:

![](https://raw.githubusercontent.com/zhlzzzzz/notes/master/redis数据恢复流程.jpg)

### 分布式锁

在进程需要排他访问共享资源的环境中，分布式锁是十分有用的原语。

### set实现

```shell
set resourceId threadUniqueId PX expireMilliseconds NX
- resourceId 加锁的资源id
- threadUniqueId 当前获取锁的线程id，全局唯一
- expireMilliseconds 过期毫秒数
```

### Lua+setnx+expire实现

```lua
if redis.call('setnx','resourceId','threadUniqueId') == 1
then 
  redis.call('expire','resourceId','expireMilliseconds') 
  return 1
else 
  return 0
end
```

使用Lua脚本是为了保证`setnx`和`expire`命令的原子性

### Redlock算法和Redission实现

Redlock是Redis作者基于分布式环境提出的一种更高级的分布式锁机制

Redisson是Redis的另一个java客户端，使用Netty实现非阻塞I/O，也提供了Redlock锁的实现，用法如下：

```xml
<dependency>
    <groupId>org.redisson</groupId>
    <artifactId>redisson</artifactId>
    <version>3.10.6</version>
</dependency>
```

```java
RedissonClient redissonClient = Redisson.create(config);

RLock lock = redissonClient.getLock("redlock");
lock.lock();
try {
    System.out.println("获取锁成功，实现业务逻辑");
    Thread.sleep(10000);
} catch (InterruptedException e) {
    e.printStackTrace();
} finally {
    lock.unlock
}
```

### Redis高可用

#### 主从复制

在Redis中，用户可哟通过执行SLAVEOF命令或者设置slaveof选项，让一个服务器去复制（replicate）另一个服务器，被复制的服务器为主服务器（master），对主服务器进行复制的服务器称为从服务器（slave）

Redis的复制功能分为同步和命令传播两个操作：

- 同步操作用于将从服务器的数据库状态更新到主服务器当前所处的状态。
- 命令传播则用于在主服务器的数据库状态被修改，导致主从服务器的数据库状态出现不一致时，让主从服务器的数据重新回到一致状态。

##### 同步

同步操作主要通过**PSYNC**命令实现，它包括完整重同步和部分重同步两种模式：

- 完整重同步用于处理初次复制情况：通过让主服务器创建并发送RDB文件，以及想从服务器发送保存在缓冲区里面的写命令来进行同步。
- 部分重同步则用于处理断线后重复制情况：当服务器在短线后重写连接主服务器时，如果条件允许，主服务器可以将从服务器断开期间执行的写命令发送给从服务器，从服务器再执行这些命令实现部分同步。

部分重同步功能由以下三个部分构成：

- 主服务器的复制偏移量和从服务器的复制偏移量
  - 主服务器每次向从服务器传播N个字节的数据时，就将自己的复制偏移量的值加N。
  - 从服务器每次收到主服务器传播来的N个字节数据，就将自己的复制偏移量值加N。
- 主服务器的复制积压缓冲区：是一个固定长度的先进先出队列，默认大小为1MB。
  - 固定长度的队列不会动态调整长度，当入队元素数量大于队列长度时，最先入队的元素会被弹出，新元素会放入队列。
  - 复制积压缓冲区会为队列中的每个字节记录响应的复制偏移量
  - 当从服务器断线重练时，从服务器会通过PSYNC命令将自己的复制偏移量发送给主服务器，主服务器会根据这个偏移量来决定对从服务器执行何种操作：
    - 如果从服务器偏移量仍然未愈复制积压缓冲区，那么主服务器执行部分重同步
    - 否则执行完全重同步
- 服务器的运行ID

##### 命令传播

当完成了同步之后，主从服务器就会进入命令传播阶段，这是主服务器只要一致将自己执行的写命令发送给从服务器，而从服务器只要一直接受并执行主服务器发来的写命令，就可以保证主从服务器一直保持一致了

#### 哨兵（Sentinel）

Sentinel时Redis高可用性解决方案：由一个或多个Sentinel实例组成的Sentinel系统可以监视任意多个主从服务器，并在被监视的主服务器下线时，自动将下线的主服务器下的某个从服务器升级为新的主服务器。

## ElasticSearch

### 常用索引类型

- Arrays
- Binary
- Boolean
- Date
- Text

### 倒排索引

## MQ

### 目标

- 解耦
- 异步
- 削峰

### 缺点

- 系统复杂性增加：加入消息队列之后，需要保证消息不会重复消费、消息的可靠性、消息队列的高可用。
- 系统的可用性降低：如果消息队列挂掉，整个系统也会收到影响

### 常用消息队列介绍

#### ActiveMQ

ActiveMq是Apache下的一款开源消息中间件，它的设计目标是提供标准的、面向消息的、多语言应用集成消息通信中间件。ActiveMQ实现了JMS1.1标准并提供了很多附加的特性。主要特性包括：

1. 支持多语言：Java、C、C++、C#、Python等；多种协议包括：OpenWire、Stomp、AMQP、MQTT
2. 完全支持JMS1.1和J2EE1.4规范（包括持久化、分布式事务消息、事务）
3. 可以通过Spring配置文件内嵌到Spring应用中。

##### 消息传送模型

- 点对点：使用队列作为消息通信的载体，满足生产者消费者模式，一条消息只能被一个消费者消费。
- 发布/订阅：使用主题作为消息通信载体，类型广播模式，发布者发布一条消息，该消息通过主题传递给所有的订阅者，在消息广播后才订阅的用户收不到该消息。

##### 基本组件

ActiveMQ使用时的基本组件和JMS是相同的：

- Broker：消息代理，表示消息队列服务器的尸体，接受客户端连接，提供消息通信的核心服务。

- Producer：消息生产者，业务的发起方，负责生产消息并传输给Broker。

- Consumer：消息消费者，业务的处理房，负责从Broker获取消息并进行业务逻辑处理。

- Topic：主题，发布订阅模型下消息的统一汇集地，不同生产者向Topic发送消息，由Broker分发到不同的订阅者，实现消息的广播。

- Queue：消息队列，点对点模式下特定生产者向特定队列发消息，消费者订阅特定队列接收消息并进行业务逻辑处理。

- Message：消息体

##### 消息存储

JMS规范中的消息分发有两种：

- 非持久化：JMS实现者保证尽最大努力分发消息，但是消息不会持久化存储
- 持久化：通过本方式分发的消息必须进行持久化存储。消息北方送到消息服务器后，只有当消息被消费者确认后，消息才会从消息服务器删除。

#### RabbitMQ

RabbitMQ是世界范围内使用最广泛的开源消息代理，采用Erlang编写，实现了AMQP（Advanced Message Queuing Protocol）

##### AMQP

基本组件如下：

- Broker：消息代理，RabbitMQ Server就是Message Broker。
- Producer：消息生产者，业务的发起方，负责生产消息并传输给Broker。
- Consumer：消息消费者，业务的处理房，负责从Broker获取消息并进行业务逻辑处理。
- Queue：消息存储队列，用于保存消息直到发送给消费者。
- Message：消息体
- Virtual host：处于多租户和安全因素设计的，把AMQP的基本组件划分到一个虚拟的分组中，相当于命名空间。
- Exchange：交换机，路由组件，负责根据分发规则，匹配路由表，将消息分发到queue中，主要类型包括
  - Direct：直连交换机，如果message中的routing key与Binding中的binding key相同，则将message转发到该queue
  - Topic：主题交换机，通过通配符匹配routing key和binding key，将message转发到匹配的queue
  - Fanout：将消息转发到和该交换机绑定的所有queue

- Connection：网络连接
- Channel：信道，多路复用连接中的一条独立的双向数据流通道
- Binding：消息队列和交换机的虚拟连接

##### 部署模式

- 普通集群模式：在多台机器上安装RabbitMQ服务，这些服务通过RabbitMQ内部提供的命令构建成一个集群，特点如下：
  - 消息队列只存在其中一个节点上
  - 消息队列的元数据同步到集群的每个节点上
  - 客户端连接任意节点即可监听消费到所有消息
- 镜像集群模式：消息队列同步到每一个节点，但是缺点也很明显：消息数据需要同步到所有的节点，开销过大，很难扩展，且当某一queue负载过大时，可能导致集群雪崩。为了减轻这个问题可以通过调整同步策略：
  - HA-mode为all：镜像队列将会在整个集群中复制。当一个新的节点加入后，也会在这个节点上复制一份。
  - HA-mode为exactly：可以配置count值，镜像队列将会在集群上复制count份。如果集群数量少于count时候，队列会复制到所有节点上。如果大于Count集群，有一个节点crash后，新进入节点也不会做新的镜像。（可以阻止集群雪崩）



## Linux

iptables和firewall

### 常用命令

## 分布式架构

分布式系统的目标是提升系统的整体性能和吞吐量，并且保证系统的容错性

### 核心理论

#### 中心化和去中心化

#### CAP理论

- 一致性（Consistence）
- 可用性（Availability）
- 分区容错性（Partition tolerance）

对于一个分布式系统来说，不可能同时满足以上三个要求

#### BASE理论

- 基本可用性（Basically Available）：当分布式系统出现不可预知的故障时，运势损失部分可用性，比如：
  - 响应时间上的损失：正常情况下，搜索引擎可以在0.5秒内返回结果，但由于出现故障，响应时间增加了1-2s
  - 系统功能上的损失：正常情况下，电子商务网站的每一笔订单都可以顺利完成，但是在一些促销高峰时，由于并发过高，为了保护系统的稳定性，部分消费者会被引导到一个降级页面
- 软状态（Soft-state）：允许系统中的数据存在中间状态，并认为该中间状态不会影响系统的整体可用性。即允许系统在不同节点的数据副本同步可以有延时。
- 最终一致性（Eventually Consistence）：系统中的所有数据副本在经过一段时间的同步之后，最终能打到一个一致的状态。

## 工程化

### 代码规范

建立代码规范的核心要点是**循序渐进**，建立代码规范的目的不是产出一份高大上的文档说明，而是应该切实地统一团队的代码风格，建立一致的编码习惯，才能提高代码审查效率，降低项目维护、交接的成本。开发人员为了将自己的编码风格调整适应制定的代码规范时，往往会降低开发效率，所以不宜一开始就制定一份完整细致的规范，而应该从一些简单实用的方面切入，比如：代码格式、命名规范、注释规范、日志规范。

**代码格式**

可以使用IDEA CheckStyle插件统一代码格式

git pre-commit可以在提交前执行校验，可以编写脚本统一校验代码格式

# 参考书籍

- [Java语言规范（Java SE 8）](https://docs.oracle.com/javase/specs/jls/se8/html/index.html)
- 深入理解Java虚拟机：JVM高级特性与最佳实践（第2版）——周志明
- Java并发编程的艺术——方腾飞、魏鹏、程晓明
- 计算机组成原理——白中英、戴志涛