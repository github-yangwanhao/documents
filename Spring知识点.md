# Spring IOC是什么

（1）IOC，Inversion of Control就是控制反转，是指创建对象的控制权的转移。以前创建对象的主动权和时机是由自己把控的，现在这种权力转移到Spring容器中，并由容器根据配置文件去创建实例和管理各个实例之 间的依赖关系。对象与对象之间松散耦合，也利于功能的复用。DI依赖注入，和控制反转是同一个概念的不同角度的描述，即应用程序在运行时依赖IoC容器来动态注入对象需要的外部资源。

（2）最直观的表达就是，IOC让对象的创建不用去new了，可以由spring自动生产，使用java的反 射机制，根据配置文件在运行时动态的去创建对象以及管理对象，并调用对象的方法的。

（3）Spring的IOC有三种注入方式 ：构造器注入、setter方法注入、属性注入。

# Spring Bean的作用域

- singleton：**默认**，每个容器中只有一个bean的实例，单例的模式由BeanFactory自身来维护。
- prototype：为每一个bean请求提供一个实例。
- request：为每一个网络 请求创建一个实例，在请求完成以后，bean会失效并被垃圾回收器回收。
- session：与request范围类似，确保每个session中有一个bean的实例，在session过期后， bean会随之失效。
- global-session：全局作用域，global-session和Portlet应用相关。当你的应用部署在Portlet 容器中工作时，它包含很多portlet。如果你想要声明让所有的portlet共用全局的存储变量的话，那么这全局变量需要存储在global-session中。全局作用域与Servlet中的session作用域效果相同。

# Spring中的Bean是线程安全的吗

Spring中的Bean默认是单例模式，Spring框架并没有对单例Bean进行多线程的封装处理。 实际上大部分时候 Spring Bean都是无状态的(比如Dao类)，所有某种程度上来说Bean也是安全的，但如果Bean有状态的话(比如 view model对象)，那就要开发者自己去保证线程安全了，最简单的就是改变Bean的作用域，把“singleton”变更为“prototype”，这样请求Bean相当于new Bean()了，就可以保证线程安全了。有状态就是有数据存储功能，无状态就是不会保存数据。

# Spring Bean的生命周期

1. **实例化Bean**

   对于BeanFactory容器，当客户向容器请求一个尚未初始化的bean时，或初始化bean的时候需要注入另一个尚未初始化的依赖时，容器就会调用createBean进行实例化。对于ApplicationContext容器，当容器启动结束后，通过获取BeanDefinition对象中的信息，实例化所有的bean。

2. **设置对象属性（依赖注入）**

   实例化后的对象被封装在BeanWrapper对象中，紧接着，Spring根据BeanDefinition中的信息以及通过BeanWrapper提供的设置属性的接口完成依赖注入。

3. **处理Aware接口**

   接着，Spring会检测该对象是否实现了xxxAware接口，并将相关的xxxAware实例注入给Bean：

   ①如果这个Bean已经实现了BeanNameAware接口，会调用它实现的setBeanName(String beanId)方法，此处传递的就是Spring配置文件中Bean的id值；

   ②如果这个Bean已经实现了BeanFactoryAware接口，会调用它实现的setBeanFactory()方法，传递的是Spring工厂自身。

   ③如果这个Bean已经实现了ApplicationContextAware接口，会调用 setApplicationContext(ApplicationContext)方法，传入Spring上下文；

4. **BeanPostProcessor**

   如果想对Bean进行一些自定义的处理，那么可以让Bean实现了BeanPostProcessor接口，那将会调用postProcessBeforeInitialization(Object obj, String s)方法。

5. **InitializingBean 与 init-method**

   如果Bean在Spring配置文件中配置了init-method属性，则会自动调用其配置的初始化方法。

6. **如果这个Bean实现了BeanPostProcessor接口，将会调用 postProcessAfterInitialization(Object obj, String s)方法；由于这个方法是在Bean初始化结束时调 用的，所以可以被应用于内存或缓存技术**

   以上几个步骤完成后，Bean就已经被正确创建了，之后就可以使用这个Bean了。

7. **DisposableBean**

   当Bean不再需要时，会经过清理阶段，如果Bean实现了DisposableBean这个接口，会调用其实现的destroy()方法。

8. **destroy-method**

   最后，如果这个Bean的Spring配置中配置了destroy-method属性，会自动调用其配置的销毁方法。

# Spring AOP是什么

AOP，Aspect-Oriented Programming，面向切面编程，能够将那些与业务无关，却为业务模块所共同调用的逻辑或责任（例如事务处理、日志管理、权限控制等）封装起来，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可扩展性和可维护性。 

Spring AOP是基于动态代理的，如果要代理的对象实现了某个接口，那么Spring AOP就会使用JDK动态代理去创建代理对象；而对于没有实现接口的对象，就无法使用JDK动态代理，转而使用CGlib 动态代理生成一个被代理对象的子类来作为代理。

当然也可以使用AspectJ，Spring AOP中已经集成了AspectJ，AspectJ应该算得上是Java生态系统中最完整的AOP框架了。

使用AOP之后我们可以把一些通用功能抽象出来，在需要用到的地方直接使用即可，这样可以大大简化代码量。我们需要增加新功能也方便，提高了系统的扩展性。日志功能、事务管理和权限管理等场景都用到了AOP。

# SpringAOP和AspectJ AOP有什么区别

Spring AOP是属于运行时增强，而AspectJ是编译时增强。

Spring AOP基于代理（Proxying），而AspectJ基于字节码操作（Bytecode Manipulation）。

Spring AOP已经集成了AspectJ，AspectJ应该算得上是Java生态系统中最完整的AOP框架了。 AspectJ AOP功能更加强大，但是Spring AOP相对来说更简单。 如果我们的切面比较少，那么两者性能差异不大。但是，当切面太多的话，最好选择AspectJ，它比SpringAOP快很多。

# CGLIB代理和JDK动态代理的区别

- 代理的对象不同：JDK动态代理只支持接口的代理，CGLIB既可以代理接口又可以代理没有实现接口的类，通过继承目标类生成子类的方式来创建代理对象。
- 实现机制不同：JDK动态代理：使用java.lang.reflect.Proxy类和java.lang.reflect.InvocationHandler接口来创建代理对象，工作通过反射机制完成。CGLIB动态代理：使用底层的字节码技术，通过MethodInterceptor接口和Enhancer类来创建代理对象，工作通过字节码增强技术完成。

# Spring的事务传播机制

| 事务传播机制  |                             解释                             |   外部不存在事务   |                         外部存在事务                         |
| :-----------: | :----------------------------------------------------------: | :----------------: | :----------------------------------------------------------: |
|   REQUIRED    | 默认的Spring事物传播级别，若当前存在事务，则加入该事务，若 不存在事务，则新建一个事务 |     开启新事务     |                      融入并使用外部事务                      |
| REQUIRED_NEW  |  不管外部有没有事务都创建一个新的事务，适合于流水登记等场景  |     开启新事务     |                外部事物挂起，开启一个新的事务                |
|    NESTED     | 没有事务， 新建一个事务；存在事务，则新建一个事务**嵌套**在外部事务中。外部事务回滚新事务一定回滚，新事务回滚不一定回滚外部事务。 |     开启新事务     | 创建一个新的事务**嵌套**在外部事务中执行，结合数据库的savePoint功能使用。 |
|   SUPPORTS    |                 支持事务，但是不主动开启事务                 | 以非事务的方式执行 |                         融入外部事务                         |
|   MANDATORY   |              强制使用事务，不存在事务则抛出异常              |     抛出异常×      |                         融入外部事务                         |
| NOT_SUPPORTED |                不支持事务，以非事务的方式运行                | 以非事务的方式执行 |               挂起外部事物，以非事务的方式执行               |
|     NEVER     |                不支持事务，存在事务则抛出异常                | 以非事务的方式执行 |                          抛出异常×                           |

# Spring事务失效的场景

1. bean未被Spring管理。
2. 类内调用，没有经过代理增强。
3. 方法的访问权限不为public，或者方法被static、final关键字修饰导致不可重写。
4. 方法的异常被内部捕捉没有抛出，spring感知不到。
5. @Transactional注解的rollbackFor属性没有赋值，默认为RuntimeException和Error，那么一些CheckException就不会被spring感知并回滚事务。
6. 多线程调用。spring事务是通过代理和数据库连接实现的，数据库连接对象被存放在ThreadLocal中，多线程会导致使用不一样的数据库连接。
7. 使用了错误的传播机制。
8. 数据库本身不支持事务。

# Spring如何解决循环依赖

Spring通过三级缓存来解决循环依赖的问题，也就是三个Map。

一级缓存：存储完整的bean

二级缓存：避免多重循环依赖重复创建动态代理

三级缓存：存储的是lambda函数接口，把bean的实例和名字作为方法参数传进去，进行aop动态代理的创建。会在第二次调用的时候才会调用三级缓存，然后放入二级缓存。

整个流程大致如下：

1. 首先 A 完成初始化第一步并将自己提前曝光出来（通过 ObjectFactory 将自己提前曝光），在 初始化的时候，发现自己依赖对象 B，此时就会去尝试 get(B)，这个时候发现 B 还没有被创建 出来；
2. 然后 B 就走创建流程，在 B 初始化的时候，同样发现自己依赖 C，C 也没有被创建出来； 
3.  这个时候 C 又开始初始化进程，但是在初始化的过程中发现自己依赖 A，于是尝试 get(A)。这 个时候由于 A 已经添加至缓存中（一般都是添加至三级缓存 singletonFactories），通过 ObjectFactory 提前曝光，所以可以通过 ObjectFactory#getObject() 方法来拿到 A 对象。C 拿 到 A 对象后顺利完成初始化，然后将自己添加到一级缓存中；
4.  回到 B，B 也可以拿到 C 对象，完成初始化，A 可以顺利拿到 B 完成初始化。到这里整个链路 就已经完成了初始化过程了。



多例bean不能解决循环依赖，因为多例bean不使用缓存，而解决循环依赖一定要依赖于缓存。

# Spring用到了哪些设计模式

- 简单工厂模式：BeanFactory就是简单工厂的提现，用来来创建对象的工厂。
- 单例模式：默认产生的Spring bean都是单例的
- 代理模式：AOP
- 观察者模式：Spring事件发布和监听
- 责任链模式：AOP的方法调用
- 模板方法模式：对外预留测自定义扩展接口

# Spring Boot可以同时处理多少请求

可以通过四个配置来控制：

```properties
# 是指可以同时处理的http的最大连接数
server.tomcat.maxConnections = 10000
# 超过maxConnections最大连接数之后的请求，会放到等待队列里，如果还超过了等待队列的长度，会拒绝
server.tomcat.accept-count = 100
# 可以理解为Tomcat的线程池的最大线程数
server.tomcat.threads.max = 200
# 可以理解为Tomcat的线程池的核心线程数
server.tomcat.threads.min-spare = 10
```

# Spring Boot自动配置

1. 通过@SpringBootApplication引入了@EnableAutoConfiguration(负责启动自动配置)
2. @EnableAutoConfiguration引入了@Import(AutoConfigurationImportSelector.class)
3. Spring容器启动时，加载IOC容器时会解析@Import注解
4. @Import导入了DeferredImportSelector，它会使SpringBoot的自动配置顺序在最后，方便我们扩展和覆盖
5. 然后读取所有的/META-INF/spring.factories文件
6. 过滤出所有AutoConfigurationClass类型的类
7. 最后通过@Condition排除无效的自动配置类

# Spring常用注解

- web注解
  - @Controller：用于标注控制层组件。
  - @RestController：是`@Controller` 和 `@ResponseBody` 的结合体，返回 JSON 数据时使用。
  - @RequestMapping：用于映射请求 URL 到具体的方法上，还可以细分为：
    - @GetMapping：只能用于处理 GET 请求
    - @PostMapping：只能用于处理 POST 请求
    - @DeleteMapping：只能用于处理 DELETE 请求
  - @RequestBody：表示一个方法参数应该绑定到 Web 请求体。
  - @ResponseBody：直接将返回的数据放入 HTTP 响应正文中，一般用于返回 JSON 数据。
  - @RequestParam：用于接收请求参数。比如 `@RequestParam(name = "key") String key`，这里的 key 就是请求参数。
  - @PathVariable：用于接收路径参数，比如 `@RequestMapping(“/hello/{name}”)`，这里的 name 就是路径参数。
- 容器注解
  - @Component：标识一个类为 Spring 组件，使其能够被 Spring 容器自动扫描和管理。
  - @Service：标识一个业务逻辑组件（服务层）。比如 `@Service("userService")`，这里的 userService 就是 Bean 的名称。
  - @Repository：标识一个数据访问组件（持久层）。
  - @Autowired：按类型自动注入依赖。
  - @Configuration：用于定义配置类，可替换 XML 配置文件。
  - @Bean：代码中手动声明一个spring bean配置，可替换 XML 配置文件。
  - @Value：用于将 Spring Boot 中 application.properties 配置的属性值赋值给变量。
- aop注解
  - @Aspect：用于声明一个切面，可以配合其他注解一起使用
  - @PointCut：定义切点，指定需要拦截的方法
  - @Before：在方法执行之前执行。
  - @After：在方法执行之后执行。
  - @Around：方法前后均执行。
  - @AfterReturning：在方法返回后执行。
  - @AfterThrowing：在方法抛出异常后执行。
- 事务注解
  - @Transcational：用于声明一个方法需要事务支持。



# SpringMVC的执行流程

1. 用户的请求会发送到前端控制器(DispatcherServlet)，前端控制器会请求HandlerMapping查找对应的Handler(根据xml或者注解查找)。
2. HandlerMapping查找到Handler之后会返回给前端控制器，由前端控制器调用处理器适配器(HandlerAdapter)去执行返回的Handler。
3. Handler执行完成后会返回ModelAndView给处理器适配器，处理器适配器再向前端控制器返回ModelAndView。
4. 前端控制器再去请求视图解析器解析接收到的ModelAndView，根据逻辑视图名解析出真正的视图并返回给前端控制器。
5. 前端控制器进行视图渲染并向用户响应结果。

![img](images/1252413-20190814224157959-1136216229.png)

# 如何在Spring Boot启动的时候运行特定的代码

实现接口`ApplicationRunner`或者`CommandLineRunner`，这两个接口实现方式一样，它们都只提供了一个run方法。

# Spring的主要模块

- Spring Core：框架的最基础部分，提供IOC和依赖注入特性。
- Spring Context：构建于Core封装包基础上的Context封装包，提供了一种框架式的对象访问方法。
- Spring AOP：提供了面向切面的编程实现，可以自定义拦截器、切点等。
- Spring Web：提供了针对Web开发的集成特性，例如文件上传，利用Servlet Listeners进行IOC容器初始化和针对Web的ApplicationContext。
- Spring Web MVC：Spring中的MVC封装包提供了Web应用的Model-View-Controller(MVC)的实现。
- Spring Dao：Data Access Object提供了JDBC的抽象层。

# MyBatis二级缓存机制

⼀级缓存是指SqlSession级别的缓存。原理：使用的数据结构是⼀个map，如果两次中间出现commit操作(修改、添加、删除)，本sqlsession中的⼀级缓存区域全部清空。

⼆级缓存是指可以跨SqlSession的缓存，是mapper级别的缓存。原理：是通过CacheExecutor实现的，CacheExecutor其实是Executor的代理对象。