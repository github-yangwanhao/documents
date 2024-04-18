

# Java中8个基本类型

| 数据类型 | 大小(字节) |    默认值    |        最小值         |         最大值         |
| :------: | :--------: | :----------: | :-------------------: | :--------------------: |
|   byte   |     1      |   (byte)0    |      -2^7(-128)       |        2^7 - 1         |
|  short   |     2      |   (short)0   |         -2^15         |        2^15 - 1        |
|   int    |     4      |      0       |         -2^31         |        2^31 - 1        |
|   long   |     8      |      0L      |         -2^63         |        2^63 - 1        |
|  float   |     4      |     0.0f     | 1.4E-45(接近 `0.0F`)  |      3.4028235E38      |
|  double  |     8      |     0.0f     | 4.9E-324(接近 `0.0D`) | 1.7976931348623157E308 |
|   char   |     2      | \u0000(null) |     \u0000(null)      |         \uFFFF         |
| boolean  |     -      |    false     |           -           |           -            |

boolean类型在Java虚拟机中没有专用的字节码指令。单个的boolean类型变量会被编译成**int**类型，而boolean数组将会被编码成**byte数组**。这样我们可以得出boolean类型占了单独使用是4个字节，在数组中又是1个字节。

# Java除了基本类型以外还有哪些类能表示数字

- java.lang.Number，是一个抽象类，是所有数值类的父类
- byte、short、int、long、float、double基本类型对应的包装类
- java.math.BigInteger、java.math.BigDecimal高精度大整数和高精度浮点数
- java.util.concurrent.atomic.AtomicInteger、AtomicLong、LongAdder、DoubleAdder等原子操作类

# String类的intern()方法

// 如果字符串常量池中已经包含一个等于此String对象的字符串，则返回代表池中这个字符串的String对象；
// 否则，将此String对象包含的字符添加到常量池中，并返回此String对象的引用。

# String、String StringBuffer 和 StringBuilder

String是只读字符串，它不是基本数据类型，而是一个对象。从底层源码来看是一个final类型的字符数组，所引用的字符串不能被改变，一经定义，无法再增删改。每次对String的操作都会生成 新的String对象。

每次+操作： 隐式在堆上new了一个跟原字符串相同的StringBuilder对象，再调用append方法 拼 接+后面的字符。 StringBuffer和StringBuilder他们两都继承了AbstractStringBuilder抽象类，从 AbstractStringBuilder抽象类中我们可以看到，他们的底层都是可变的字符数组，所以在进行频繁的字符串操作时，建议使用StringBuffer和 StringBuilder来进行操作。 另外StringBuffer 对方法加了同步锁或者对调用的方法加了同步锁，所 以是线程安全的。StringBuilder 并没有对方法进行加同步锁，所以是非线程安全的。