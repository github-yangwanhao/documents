

# 基础知识

### Java中8个基本类型

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

### Java除了基本类型以外还有哪些类能表示数字

- java.lang.Number，是一个抽象类，是所有数值类的父类
- byte、short、int、long、float、double基本类型对应的包装类
- java.math.BigInteger、java.math.BigDecimal高精度大整数和高精度浮点数
- java.util.concurrent.atomic.AtomicInteger、AtomicLong、LongAdder、DoubleAdder等原子操作类

### float和double的区别是什么

float最多可以存储8位的十进制数，并在内存中占4字节。

double最多可以存储16位的十进制数，并在内存中占8字节。

### BigDecimal比较应该用哪个方法

应该用compareTo()方法。

compareTo方法比较的是数值，会忽略小数点后多余的位数，会认为1、1.0、1.00都是相等的值。

equals方法则会完全比较小数点后的位数，因此会认为1和1.0是不相等的。

```
public class Main {

    public static void main(String[] args) {
        BigDecimal bigDecimal1 = new BigDecimal("1.0");
        BigDecimal bigDecimal2 = new BigDecimal("1.00");
		// true
        System.out.println(bigDecimal1.compareTo(bigDecimal2) == 0);
        // false
        System.out.println(bigDecimal1.equals(bigDecimal2));
    }
}
```

### Java的四种访问修饰符及访问权限

|               | 本类内 | 同包下其他类 | 不同包子类内 | 不同包其他类 |
| :-----------: | :----: | :----------: | :----------: | :----------: |
|    public     |   √    |      √       |      √       |      √       |
|   protected   |   √    |      √       |      √       |              |
| default(默认) |   √    |      √       |              |              |
|    private    |   √    |              |              |              |

### Java中局部变量必须初始化，全局变量可不初始化

全局变量可以不初始化，因为会在编译的时候自动初始化为默认值

```java
public class Constant {

    static int num1;

    public static void main(String[] args) {
        // 可以输出0
        System.out.println(num1);
    }
}
```

局部变量必须手动初始化才能使用，否则编译会报错。

```java
public class Constant {

    public static void main(String[] args) {
        int num1;
        // 在这里编译不通过，报错'Variable 'num1' might not have been initialized'
        System.out.println(num1);
    }
}
```

### instanceof 关键字的作用

instanceof 严格来说是Java中的一个双目运算符，用来测试一个对象是否为一个类的实例，用法为：

```java
boolean result = obj instanceof Class;
```

其中obj为一个对象，Class表示一个类或者一个接口，当obj为Class的对象或者是其直接或间接子类，或者是其接口的实现类，结果result都返回 true，否则返回false。 

注意：编译器会检查 obj 是否能转换成右边的Class类型，如果不能转换则直接报错，如果不能确定类型，则通过编译，具体看运行时定。

```java
// 编译不通过，i必须是引用类型，不能是基本类型
int i = 0;
System.out.println(i instanceof Integer);

// 编译不通过，编译器检查到类型不能转换直接报错
Integer integer = new Integer(1);
System.out.println(integer instanceof String);

// true
Integer integer = 1;
System.out.println(integer instanceof Integer);

// false，在JavaSE规范中对instanceof运算符的规定就是：如果obj为null，那么将返回false
System.out.println(null instanceof Object)
```

### 接口或者父类的方法没有声明异常，实现类或者子类能否声明异常

不能。实现类或者子类的方法上想要声明异常，那么其父类或者接口的方法上也必须声明该异常或者该异常的父类异常。

### String类的intern()方法

如果字符串常量池中已经包含一个等于此String对象的字符串，则返回代表池中这个字符串的String对象；
否则，将此String对象包含的字符添加到常量池中，并返回此String对象的引用。

### String、String StringBuffer 和 StringBuilder

String是只读字符串，它不是基本数据类型，而是一个对象。从底层源码来看是一个final类型的字符数组，所引用的字符串不能被改变，一经定义，无法再增删改。每次对String的操作都会生成 新的String对象。

每次+操作： 隐式在堆上new了一个跟原字符串相同的StringBuilder对象，再调用append方法 拼 接+后面的字符。 StringBuffer和StringBuilder他们两都继承了AbstractStringBuilder抽象类，从 AbstractStringBuilder抽象类中我们可以看到，他们的底层都是可变的字符数组，所以在进行频繁的字符串操作时，建议使用StringBuffer和 StringBuilder来进行操作。 另外StringBuffer 对方法加了同步锁或者对调用的方法加了同步锁，所 以是线程安全的。StringBuilder 并没有对方法进行加同步锁，所以是非线程安全的。

### 为什么重写equals()必须要重写hashcode()

在Java中，hashCode()方法返回对象的哈希码，它在哈希集合中起着关键的作用。当我们将对象放入哈希集合时，集合通过对象的哈希码来决定对象的存储位置。

如果我们在重写了equals()方法的同时不重写hashCode()方法，可能导致以下问题：

相等对象哈希码不同： 如果两个对象通过equals()判断相等，但它们的哈希码不同，这将违反哈希集合的原则，导致无法正确处理相等对象。

相同哈希码不等对象： 如果两个对象的哈希码相等，但它们通过equals()判断不相等，这可能导致哈希集合中存储重复的对象，破坏集合的正确性。

因此，为了确保哈希集合正常运作，必须同时重写equals()和hashCode()方法。

### equals和compareTo

equals方法是Object类的方法，也是所有类都有的方法；compareTo方法是java.lang.Comparable接口里定义的方法，要使用该方法必须实现Comparable接口。

equals默认使用==比较，因此能够接受参数为null的情况；compareTo方法虽然没有具体实现，但是通常来说不能接受参数为null，接口里的参数也被@NotNull注解标记了，java工具类的compareTo如果参数为null一般都会导致空指针异常。

equals方法的返回值是boolean类型的；compareTo方法的返回值是int类型的，通常来说实现类的逻辑要满足小于返回-1，大于返回1，等于返回0的逻辑。

### this关键字

this关键字一般来说有三种应用：访问成员变量、调用成员方法、调用构造方法。

- 访问成员变量：通过this关键字可以明确的访问一个类的成员变量，解决了与局部变量名称冲突的问题。

- 调用成员方法：类内调用可以省略this。

- 调用构造方法：通过this关键字调用构造方法注意点：
  - 只能在构造方法中调用构造方法，不能在成员方法中调用。
  - 在构造方法中，使用this调用构造方法的语句**必须位于第一行**，而且**只能出现一次**！

### super关键字

super关键字一般来说有三种应用：访问父类的成员变量、父类的成员方法和父类的构造方法。

- 访问父类的成员变量、成员方法：在Java中，当子类重写了父类的方法后，子类对象将无法访问父类被重写的方法。通过super可以区分开子类的成员变量、成员方法和父类的同名成员变量、成员方法。
- 访问父类的构造方法：子类的构造方法一定会使用super关键字访问父类的构造方法，如果没有声明则默认访问父类的无参构造方法。通过super调用父类的构造方法的代码必须位于子类构造方法的第一行，且只能出现一次！普通方法则没有这个限制。

### abstract和static、final、native、synchronized

- abstract和static关键字不能同时修饰方法。因为static方法会在类加载的时候就执行初始化，static方法在不生成类的实例的时候可以被直接调用；而abstract方法代表没有具体的方法实现，必须要继承并实现才能调用。两者是互相矛盾的。
- abstract和final关键字不能同时修饰方法。因为abstract代表该方法是抽象的空方法，必须继承实现后才能使用；而final修饰的方法不能被子类覆盖重写。两者是互相矛盾的。
- abstract和native关键字不能同时修饰方法。因为abstract代表该方法是抽象的空方法，必须继承实现后才能使用；而native方法代表该方法已经被实现了，只是实现语言不是java而已。两者是互相矛盾的。
- abstract和synchronized关键字不能同时修饰方法。因为synchronized方法是对调用的对象加锁的；而abstract方法是抽象方法没有具体的对象。两者是互相矛盾的。

### char类型可不可以存储一个汉字？

可以，因为Java默认Unicode编码，Unicode码是16位的，char占2个字节正好16位。

### final关键字的用处

- final修饰的类不可以被继承
- final修饰的方法不可以被重写
- final修饰的基本类型变量值不可以被修改(对应的包装类会被自动拆箱为基本类型变量，一样是值不可修改)；final修饰的引用类型变量引用地址不可以被修改，但是其对象内部的属性可以被重新赋值
- final修饰的常量在编译阶段就会放入常量池中

### a=a+b与a+=b有什么区别吗

+=操作符会进行隐式自动类型转换，此处a+=b隐式的将加操作的结果类型强制转换为持有结果的类型，而a=a+b则不会自动进行类型转换。

### short s1= 1;s1 = s1 + 1;这两句代码错在哪里

short类型在进行运算时会自动提升为int类型，也就是说s1+1的运算结果是int类型，而s1是short

类型，此时编译器会报错。

正确写法：s1 += 1;

+=操作符会对右边的表达式结果强转匹配左边的数据类型，所以没错。

### 工作中空间换时间的案例

- 缓存
- 数据库索引
- ThreadLocal缓存
- 数据库读写分离
- 字段冗余
- List转Map的结构转换



# 对象相关知识点

### java创建对象的几种方式

- 使用new关键字创建对象
- 通过反射创建对象
- 通过序列化和反序列化创建对象
- 通过clone创建对象

### Java创建对象时的实例化顺序

```java
public class Parent {
    // 父类的静态变量和静态代码块
    protected static int num = 0;
    static {
        num = 1;
        System.out.println("父类的静态代码块，此时num = " + num);
    }
	// 父类的无参构造方法
    public Parent() {
        System.out.println("父类的无参构造方法");
    }
	// 父类的普通成员变量和普通代码块
    protected String name;
    {
        name = "张三";
        System.out.println("父类的普通代码块，此时name = " + name);
    }
}
```

```java
public class Children extends Parent {
	// 子类的静态代码块，静态变量则继承自父类
    static {
        num = 2;
        System.out.println("子类的静态代码块，此时num = " + num);
    }
	// 子类的无参构造方法
    public Children() {
        System.out.println("子类的无参构造方法");
    }
	// 子类的普通代码块，成员变量同样继承自父类
    {
        name = "李四";
        System.out.println("子类的普通代码块，此时name = " + name);
    }

    public static void main(String[] args) {
        // 在main方法中new一个子类对象
        new Children();
    }
}
```

在main方法中new一个子类对象，控制台输出结果如下：

```
父类的静态代码块，此时num = 1
子类的静态代码块，此时num = 2
父类的普通代码块，此时name = 张三
父类的无参构造方法
子类的普通代码块，此时name = 李四
子类的无参构造方法
```

因此，创建对象时类的实例化顺序如下：

1，父类的静态成员变量和静态代码块加载
2，子类的静态成员变量和静态代码块加载
3，父类成员变量和方法块加载
4，父类的构造函数加载
5，子类成员变量和方法块加载
6，子类的构造函数加载

### Java对象的四种引用

- 强引用：强引用是平常中使用最多的引用，强引用在程序内存不足(OOM)的时候也不会被回收。一般创建的对象都是强引用。

- 软引用：软引用在程序内存不足时，会被回收。 可用于创建缓存的时候，创建的对象放进缓存中，当内存不足时，JVM就会回收早先创建的对象。

  ```java
  // 注意：wrf这个引用也是强引用，它是指向SoftReference这个对象的，
  // 这里的软引用指的是指向new String("str")的引用，也就是SoftReference类中T
  SoftReference<String> wrf = new SoftReference<String>(new String("str"));
  ```

- 弱引用：弱引用就是只要JVM垃圾回收器发现了它，就会将之回收。

  ```java
  WeakReference<String> wrf = new WeakReference<String>(str);
  ```

- 虚引用：虚引用的回收机制跟弱引用差不多，但是它被回收之前，会被放入ReferenceQueue中。注意，其它引用是被JVM回收后才被传入ReferenceQueue中的。由于这个机制，所以虚引用大多被用于引用销毁前的处理工作。还有就是虚引用创建的时候，必须带有ReferenceQueue。

  ```java
  PhantomReference<String> prf = new PhantomReference<String>(new String("str"), 
  new ReferenceQueue<>());
  ```

### 反射的原理是什么

反射机制是在运行时，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意个对象，都能够调用它的任意一个方法。在Java中，只要给定类的名字，就可以通过反射机制来获得类的所有信息。

反射是一种动态地在运行时检查类、获取类信息和操作类对象的能力。它允许在运行时检查类、调用类方法、操作字段等，而不需要在编译时知道类的具体信息。

反射是通过`java.lang.reflect`包中的类来实现的，主要涉及三个关键类：`Class`、`Field`和`Method`。这些类允许你在运行时获取并操作类的信息、字段和方法。

通过反射，可以做到以下几点：

- 获取 Class 对象： 可以通过类的全限定名或对象的getClass()方法获取 Class 对象。
- 获取类的信息： 可以获取类的构造方法、字段、方法等信息。
- 创建对象： 可以通过反射创建类的实例、调用构造方法。
- 调用方法和操作字段： 可以调用类的方法、访问和修改字段值等。

### 反射的三种实现方式

首先提供一个User类，然后演示三种通过反射生成User类对象的方式。

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {

    private String name;
    private int age;

    public void speak() {
        System.out.println("我的名字是" + name + ",我今年" + age + "岁了.");
    }
}
```

1. **使用Class对象的newInstance()方法：**要求被反射类必须有public的无参构造方法

   ```java
   public class ReflectTest1 {
   
       public static void main(String[] args) throws IllegalAccessException, InstantiationException {
           Class<User> clazz = User.class;
           // 默认使用被反射类的无参构造方法创建对象
           User user = clazz.newInstance();
           user.setAge(13);
           user.setName("张三");
           user.speak();
       }
   }
   ```

2. **通过Class类的getConstructor方法获取public的构造方法：**可以使用被反射类的public的有参构造方法

   ```java
   public class ReflectTest2 {
   
       public static void main(String[] args) throws NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException {
           Class<User> clazz = User.class;
           // 这个方法只能获取到类的public的构造方法
           Constructor<User> constructor = clazz.getConstructor(String.class, Integer.class);
           User user = constructor.newInstance("张三", 13);
           user.speak();
       }
   }
   ```

3. **通过Class类的getDeclaredConstructor方法获取任意权限的构造方法：**

   ```java
   public class ReflectTest3 {
   
       public static void main(String[] args) throws NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException {
           Class<User> clazz = User.class;
           // 这个方法可以获取到类的所有构造方法，包括private的
           Constructor<User> constructor = clazz.getDeclaredConstructor(String.class, Integer.class);
           // 但是这里要设置为true才有访问权限
           constructor.setAccessible(true);
           User user = constructor.newInstance("张三", 13);
           user.speak();
       }
   }
   ```

### Class类的getDeclaredFields()和getFields()区

getDeclaredFields()只能获取当前类的所有属性，但是可以获取到非public的属性

getFields()可以获取到当前类及其父类的所有public的属性

### 反射中Class.forName和ClassLoader区别

Class.forName和ClassLoader是Java反射中用于加载类的两种不同方式。

Class.forName是一个静态方法，通过提供类的完全限定名，在运行时加载类。此方法还会执行类的静态初始化块。如果类名不存在或无法访问，将抛出ClassNotFoundException异常。

ClassLoader是一个抽象类，用于加载类的工具。每个Java类都有关联的ClassLoader对象，负责将类文件加载到Java虚拟机中。ClassLoader可以动态加载类，从不同来源加载类文件，如本地文件系统、网络等。

一般情况下推荐使用ClassLoader来加载和使用类，因为它更灵活，并避免执行静态初始化的副作用。只有特定场景才推荐使用Class.forName，如加载数据库驱动程序。

# 集合相关

### HashMap和HashTable的区别

- 父类不同：HashMap继承自AbstractMap类，HashTable继承自Dictionary类
- 对外提供的方法不同：HashTable比HashMap多提供了elements()和contains()两个方法。elments()方法继承自HashTable的父类Dictionnary。elements()方法用于返回此HashTable中的value的枚举。contains()方法判断该Hashtable是否包含传入的value。它的作用与containsValue()一致。事实上，contansValue()就只是调用了一下contains()方法。
- 对null的支持不同：HashTable的key和value都不支持null；HaspMap的key和value都可以是null，但是为null的key只能有一个。
- 安全性不同：HashTable的方法上都有synchronized修饰，因此是线程安全的；HashMap是线程不安全的，如果需要保证安全性，建议使用ConCurrentHashMap。

### 顺序的Map实现类

我们知道HashMap里的元素是不可重复且无序的。那么哪些Map是有序的呢？

- LinkedHashMap：`LinkedHashMap` 继承自 `HashMap`，底层采用哈希表实现，同时使用双向链表维护插入顺序或访问顺序。通过维护一个双向链表，可以按照插入顺序或者访问顺序（最近最少使用）来迭代元素。插入时，会将新元素放到链表末尾；访问时，会将访问的元素移到链表尾部。因此，迭代时可以保证按照插入或者访问顺序输出元素。
- TreeMap：`TreeMap` 是基于红黑树实现的有序Map，它按照键的自然顺序或者通过自定义比较器进行排序。内部使用红黑树来维护键值对的顺序，因此在遍历时可以按照键的顺序输出元素。

### ArrayList是如何扩容的



# IO相关

### Java里IO流分为几类

- 按照操作流的流向分：输入流和输出流
- 按照操作单元划分为：字节流和字符流
- 按照流的角色划分为：节点流和处理流

Java IO流共涉及40多个类，这些类都是从如下4个抽象类基类中派生出来的。

- InputStream/Reader：所有输入流的基类，前者是字节输入流，后者是字符输入流
- OutputStream/Writer：所有输出流的基类，前者是字节输出流，后者是字符输出流

按照操作分类得到以下图片：

![img](images/Java%20IO.jpeg)