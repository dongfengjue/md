1. log4j
理由：向system.out.println()说永别，刚开始学java的时候总是喜欢依靠system.out.println()的输出来查看异常和调试。后来工作后就果断log4j了，这样项目开发和发布的时候，可以根据自己的需求开关日志级别，把日志打印到远程服务等多种功能。现在这个基本成为标配了。

2.guava
google出品的第三方工具库。当java.util 提供的数据结构不能满足的时候从这里你可以快速找到大量已经写好的数据结构了，这使得你不用花费心思在一些常用的数据结构上了。比如LRU缓存之类的。只是好几个版本的跨度比较大，兼容也不怎么好。

3.apache commons 包含的组件 http://commons.apache.org/。
apache commons 涵盖了大量的小工具，比如发邮件(线上告警用)，快速且方便的IO操作封装。等等工具很多，可以自己慢慢去学习。

4.netty 
一个网络通信框架,当需要实现自定义协议的时候我就用这个，netty的新版本自带了很多协议的实现版本，这是搞网络快速开发不二的选择。

5.httpclient 系列
主要是用在测试线上服务的时候用的。毕竟是一个基于http协议网络工具，当开发的web上线的时候，利用httpclient来写测试用例，效果很不错。测试的工具有很多，但是这个可以满足你定制http请求的需求。

6.jetty
httpclient 的同一个项目下有一个简易的http server 但是没有实现servlet，这个时候jetty的效果就体现出来了。特别的是，当你打算对 jsp jstl 等方式编写的网页进行功能测试的时候，jetty就可以承担 mock的作用，好用得很。使得你可以在junit的框架下对jsp编写的网页进行测试。

7.maven
现在的java已经离不开这个玩意了。你可以自己搭建一个nexus 来做maven私服。当你存在RPC的需求的时候。完全可以把自己的接口部分和client打包上传到maven私服，调用的服务只需要include这个包就可以远程调用你的服务了。在国内配合上dubbo这类 SOA框架。那个效果酸爽的很。完成了实际意义上的接口于实现在网络层级的分离。让java 的package 形成一个网络上的package。需要某个服务的时候，include 直接调用。其他的一律不用管。

8. Disruptor http://lmax-exchange.github.io/disruptor/
高性能的并发框架，一般用来在涉及到 生产者--消费者模型的时候会用到。抛开性能不谈(实际上性能相当棒)它的抽象方式和接口都设计得很好。

9.quartz http://www.quartz-scheduler.org/
一个调度器，当涉及到多任务定时调用的时候这个框架能帮上非常多。特别在网络游戏服务器中，如果需要定时或者短时定时来做某些事情的时候（用户的长时间buff状态，刷新时间等），quart是一个非常不错的选择。如果时间比较短的话，利用java内置的DelayQueue 也可以。

10.jOOQ/jOOQ · GitHub
用来替代hibernate等，第一次用的时候就眼前一亮。之前用hibernate的时候，如果遇到复杂查询需要优化，是个很麻烦的事情，dba给的建议肯定是基于sql的，需要在sql、hql之间转来转去，或者用原生sql去做。
jooq基本就解决了这个问题，掌握一门sql就可以了



1，Struts2与Spring整合后, 由spring来管理Struts2的Action, bean默认是单实例有情况下,会有如下问题:
1) Struts2的Action是单例,其中的FieldError,actionerror中的错误信息会累加, 即使再次输入了正确的信息,也过不了验证.
2) Struts2的Action是有状态的,他有自己的成员属性, 所以在多线程下,会有线程安全问题，这是最大的问题。
如何解决?
方案一: 就是不用单例, spring中bean的作用域设为prototype,每个请求对应一个Action实例.(建议这样做)
方案二: spring中bean的作用域设为session ,每个session对应一个实例,解决了多线程问题.
总结 :
方案一：bean的作用域设为prototype, 不用担心性能不好, 实际测试过,多实例Action性能没问题.
方案二: 有人担心方案一性能不好， 所有才有了方案二, 不知比方案一性能 能高多少？应该不会高多少。

2，Spring MVC Controller默认是单例的：
单例的原因有二：
1、为了性能。
2、不需要多例。
万一必须要定义一个非静态成员变量时候，则通过注解@Scope("prototype")，将其设置为多例模式
threadlocal 可以保证线程安全

3，机制：spring mvc的入口是servlet，而struts2是filter，这样就导致了二者的机制不同。 
 性能：spring会稍微比struts快。spring mvc是基于方法的设计，而sturts是基于类，每次发一次请求都会实例一个action，每个action都会被注入属性，而spring基于方法，粒度更细，但要小心把握像在servlet控制数据一样。spring3 mvc是方法级别的拦截，拦截到方法后根据参数上的注解，把request数据注入进去，在spring3 mvc中，一个方法对应一个request上下文。而struts2框架是类级别的拦截，每次来了请求就创建一个Action，然后调用setter getter方法把request中的数据注入；struts2实际上是通过setter getter方法与request打交道的；struts2中，一个Action对象对应一个request上下文。 

参数传递：struts是在接受参数的时候，可以用属性来接受参数，这就说明参数是让多个方法共享的。

设计思想上：struts更加符合oop的编程思想， spring就比较谨慎，在servlet上扩展。 

intercepter的实现机制：struts有以自己的interceptor机制，spring mvc用的是独立的AOP方式。这样导致struts的配置文件量还是比spring mvc大，虽然struts的配置能继承，所以我觉得论使用上来讲，spring mvc使用更加简洁，开发效率Spring MVC确实比struts2高。spring mvc是方法级别的拦截，一个方法对应一个request上下文，而方法同时又跟一个url对应，所以说从架构本身上spring3 mvc就容易实现restful url。struts2是类级别的拦截，一个类对应一个request上下文；实现restful url要费劲，因为struts2 action的一个方法可以对应一个url；而其类属性却被所有方法共享，这也就无法用注解或其他方式标识其所属方法了。spring3 mvc的方法之间基本上独立的，独享request response数据，请求数据通过参数获取，处理结果通过ModelMap交回给框架方法之间不共享变量，而struts2搞的就比较乱，虽然方法之间也是独立的，但其所有Action变量是共享的，这不会影响程序运行，却给我们编码，读程序时带来麻烦。 

另外，spring3 mvc的验证也是一个亮点，支持JSR303，处理ajax的请求更是方便，只需一个注解@ResponseBody ，然后直接返回响应文本即可。
总结:
这样也说明了SpringMVC还有Servlet都是方法的线程安全，所以在类方法声明的私有或者公有变量不是线程安全的，而struts2的确实是线程安全的。

4，多线程  扩展thread 类，继承， 实现 Runnable 接口  实现Runnable接口比继承Thread类所具有的优势：

1）：适合多个相同的程序代码的线程去处理同一个资源
2）：可以避免java中的单继承的限制
3）：增加程序的健壮性，代码可以被多个线程共享，代码和数据独立

sleep（） 直接停滞，一定等到指定时间后，再调用，阻塞当前线程，留一定时间给其它线程执行

wait（） 等待，暂时失去对象的机锁，其它线程可以访问，wait()使用notify或者notifyAlll或者指定睡眠时间来唤醒当前等待池中的线程，wiat()必须放在synchronized block中，否则会在program runtime时扔出”java.lang.IllegalMonitorStateException“异常。


sysnchronized  方法，只允许一个线程访问，修饰代码块，只允许一个线程访问该代码块，修饰关键字，表示该资源实行互斥访问

5，spring 源码  getBean --》 doGetBean --》createBean --》 doCreateBean --》createBeanInstance --》 instantiateBean


populateBean --》applyPropertyValues --》resolveValueIfNecessary --》setPropertyValue



6， <bean id="eatHelper" class="springmvc.EatHelper"></bean>
    <bean id="man" class="springmvc.Man"></bean>
    
    <!-- 配置pointcut  切点相当于事故发生的地点 吃饭的这个动作在系统的什么地方出发的呢？-->
    <bean id="eatPointcut" class="org.springframework.aop.support.JdkRegexpMethodPointcut">
        <property name="pattern" value=".*eat"></property>
    </bean>
    <!-- 通知  通知相当于事故时间及内容,连接切点和通知 -->
    <bean id="eatHelperAdvisor" class="org.springframework.aop.support.DefaultPointcutAdvisor">
        <property name="advice" ref="eatHelper"></property>
        <property name="pointcut" ref="eatPointcut"></property>
    </bean>
    <!-- 调用ProxyFactoryBean产生代理对象 （proxyInterfaces）  -->
    <bean id="manProxy" class="org.springframework.aop.framework.ProxyFactoryBean">
        <property name="target" ref="man"></property>
        <property name="interceptorNames" value="eatHelperAdvisor"></property>
        <property name="proxyInterfaces" value="springmvc.EatAble"></property>
    </bean>

7，过滤器和拦截器的区别：
　　①拦截器是基于Java的反射机制的，而过滤器是基于函数回调。
　　②拦截器不依赖与servlet容器，过滤器依赖与servlet容器。
　　③拦截器只能对action请求起作用，而过滤器则可以对几乎所有的请求起作用。
　　④拦截器可以访问action上下文、值栈里的对象，而过滤器不能访问。
　　⑤在action的生命周期中，拦截器可以多次被调用，而过滤器只能在容器初始化时被调用一次。
　　⑥拦截器可以获取IOC容器中的各个bean，而过滤器就不行，这点很重要，在拦截器里注入一个service，可以调用业务逻辑。

8，gc 
年轻代（存放实例化的对象）
年轻代分为三个区：Eden和两个存活区（Survivor0和Survivor1），分别占内存的80%、10%、10%
使用“停止-复制（Stop-and-copy）”清理法（将Eden区和一个Survivor中仍然存活的对象拷贝到另一个Survivor中）
当Eden区满时，就执行一次MinorGC，并将剩余存活的对象都添加到Surivivor0，回收Eden中的没有存活的对象。
当Surivivor0页都满了的时候，就将仍然存活的存到Surivivor1中，回收Surivivor0中的对象
Surivivor0和Surivivor1依次去存，当两个存活区切换了几次后（HotSpot默认是15次），将仍然存活的对象复制到老年代
2.老年代的GC（存放较大的实例化的对象和在年轻代中存活了足够久的对象）
老年代GC用的是标记-整理算法，即标记存活的对象，向一端移动，保证内存的完整性，然后将未标记的清掉
3.永久代的GC（存放常量、类）
说明：在JDK1.6版本之后，永久代就要被取消掉了，只留下年轻代和老年代

9，
(1) 连通性：
注册中心负责服务地址的注册与查找，相当于目录服务，服务提供者和消费者只在启动时与注册中心交互，注册中心不转发请求，压力较小
监控中心负责统计各服务调用次数，调用时间等，统计先在内存汇总后每分钟一次发送到监控中心服务器，并以报表展示
服务提供者向注册中心注册其提供的服务，并汇报调用时间到监控中心，此时间不包含网络开销
服务消费者向注册中心获取服务提供者地址列表，并根据负载算法直接调用提供者，同时汇报调用时间到监控中心，此时间包含网络开销
注册中心，服务提供者，服务消费者三者之间均为长连接，监控中心除外
注册中心通过长连接感知服务提供者的存在，服务提供者宕机，注册中心将立即推送事件通知消费者
注册中心和监控中心全部宕机，不影响已运行的提供者和消费者，消费者在本地缓存了提供者列表
注册中心和监控中心都是可选的，服务消费者可以直连服务提供者
(2) 健状性：
监控中心宕掉不影响使用，只是丢失部分采样数据
数据库宕掉后，注册中心仍能通过缓存提供服务列表查询，但不能注册新服务
注册中心对等集群，任意一台宕掉后，将自动切换到另一台
注册中心全部宕掉后，服务提供者和服务消费者仍能通过本地缓存通讯
服务提供者无状态，任意一台宕掉后，不影响使用
服务提供者全部宕掉后，服务消费者应用将无法使用，并无限次重连等待服务提供者恢复
(3) 伸缩性：

注册中心为对等集群，可动态增加机器部署实例，所有客户端将自动发现新的注册中心
服务提供者无状态，可动态增加机器部署实例，注册中心将推送新的服务提供者信息给消费者


------------------------


1，Struts2与Spring整合后, 由spring来管理Struts2的Action, bean默认是单实例有情况下,会有如下问题:
1) Struts2的Action是单例,其中的FieldError,actionerror中的错误信息会累加, 即使再次输入了正确的信息,也过不了验证.
2) Struts2的Action是有状态的,他有自己的成员属性, 所以在多线程下,会有线程安全问题，这是最大的问题。
如何解决?
方案一: 就是不用单例, spring中bean的作用域设为prototype,每个请求对应一个Action实例.(建议这样做)
方案二: spring中bean的作用域设为session ,每个session对应一个实例,解决了多线程问题.

springbean的作用域
singleton
在每个Spring IoC容器中一个bean定义对应一个对象实例。
prototype
一个bean定义对应多个对象实例。
request
在一次HTTP请求中，一个bean定义对应一个实例；即每次HTTP请求将会有各自的bean实例， 它们依据某个bean定义创建而成。该作用域仅在基于web的Spring ApplicationContext 情形下有效。
session
在一个HTTP Session 中，一个bean定义对应一个实例。该作用域仅在基于web的SpringApplicationContext 情形下有效。
global session
在一个全局的HTTP Session 中，一个bean定义对应一个实例。典型情况下，仅在使用portlet context的时候有效。该作用域仅在基于web的Spring ApplicationContext 情形下有效。

总结 :
方案一：bean的作用域设为prototype, 不用担心性能不好, 实际测试过,多实例Action性能没问题.
方案二: 有人担心方案一性能不好， 所有才有了方案二, 不知比方案一性能 能高多少？应该不会高多少。

2，Spring MVC Controller默认是单例的：
单例的原因有二：
1、为了性能。
2、不需要多例。
万一必须要定义一个非静态成员变量时候，则通过注解@Scope("prototype")，将其设置为多例模式
threadlocal 可以保证线程安全

threadlocal

在同步机制中，通过对象的锁机制保证同一时间只有一个线程访问变量
以空间换时间,(ThreadLocalMap)ThreadLocal会为每一个线程提供一个独立的变量副本，从而隔离了多个线程对数据的访问冲突。因为每一个线程都拥有自己的变量副本，从而也就没有必要对该变量进行同步了。ThreadLocal提供了线程安全的共享对象，在编写多线程代码时，可以把不安全的变量封装进ThreadLocal。

3，机制：spring mvc的入口是servlet，而struts2是filter，这样就导致了二者的机制不同。 
 性能：spring会稍微比struts快。spring mvc是基于方法的设计，而sturts是基于类，每次发一次请求都会实例一个action，每个action都会被注入属性，而spring基于方法，粒度更细，但要小心把握像在servlet控制数据一样。spring3 mvc是方法级别的拦截，拦截到方法后根据参数上的注解，把request数据注入进去，在spring3 mvc中，一个方法对应一个request上下文。而struts2框架是类级别的拦截，每次来了请求就创建一个Action，然后调用setter getter方法把request中的数据注入；struts2实际上是通过setter getter方法与request打交道的；struts2中，一个Action对象对应一个request上下文。 

参数传递：struts是在接受参数的时候，可以用属性来接受参数，这就说明参数是让多个方法共享的。

设计思想上：struts更加符合oop的编程思想， spring就比较谨慎，在servlet上扩展。 

intercepter的实现机制：struts有以自己的interceptor机制，spring mvc用的是独立的AOP方式。这样导致struts的配置文件量还是比spring mvc大，虽然struts的配置能继承，所以我觉得论使用上来讲，spring mvc使用更加简洁，开发效率Spring MVC确实比struts2高。spring mvc是方法级别的拦截，一个方法对应一个request上下文，而方法同时又跟一个url对应，所以说从架构本身上spring3 mvc就容易实现restful url。struts2是类级别的拦截，一个类对应一个request上下文；实现restful url要费劲，因为struts2 action的一个方法可以对应一个url；而其类属性却被所有方法共享，这也就无法用注解或其他方式标识其所属方法了。spring3 mvc的方法之间基本上独立的，独享request response数据，请求数据通过参数获取，处理结果通过ModelMap交回给框架方法之间不共享变量，而struts2搞的就比较乱，虽然方法之间也是独立的，但其所有Action变量是共享的，这不会影响程序运行，却给我们编码，读程序时带来麻烦。 

另外，spring3 mvc的验证也是一个亮点，支持JSR303，处理ajax的请求更是方便，只需一个注解@ResponseBody ，然后直接返回响应文本即可。
总结:
这样也说明了SpringMVC还有Servlet都是方法的线程安全，所以在类方法声明的私有或者公有变量不是线程安全的，而struts2的确实是线程安全的。

4，多线程  扩展thread 类，继承， 实现 Runnable 接口  实现Runnable接口比继承Thread类所具有的优势：

1）：适合多个相同的程序代码的线程去处理同一个资源
2）：可以避免java中的单继承的限制
3）：增加程序的健壮性，代码可以被多个线程共享，代码和数据独立

sleep（） 直接停滞，一定等到指定时间后，再调用，阻塞当前线程，留一定时间给其它线程执行

wait（） 等待，暂时失去对象的机锁，其它线程可以访问，wait()使用notify或者notifyAlll或者指定睡眠时间来唤醒当前等待池中的线程，wiat()必须放在synchronized block中，否则会在program runtime时扔出”java.lang.IllegalMonitorStateException“异常。


sysnchronized  方法，只允许一个线程访问，修饰代码块，只允许一个线程访问该代码块，修饰关键字，表示该资源实行互斥访问

5，spring 源码  getBean --》 doGetBean --》createBean --》 doCreateBean --》createBeanInstance --》 instantiateBean


populateBean --》applyPropertyValues --》resolveValueIfNecessary --》setPropertyValue



6， <bean id="eatHelper" class="springmvc.EatHelper"></bean>
    <bean id="man" class="springmvc.Man"></bean>
    
    <!-- 配置pointcut  切点相当于事故发生的地点 吃饭的这个动作在系统的什么地方出发的呢？-->
    <bean id="eatPointcut" class="org.springframework.aop.support.JdkRegexpMethodPointcut">
        <property name="pattern" value=".*eat"></property>
    </bean>
    <!-- 通知  通知相当于事故时间及内容,连接切点和通知 -->
    <bean id="eatHelperAdvisor" class="org.springframework.aop.support.DefaultPointcutAdvisor">
        <property name="advice" ref="eatHelper"></property>
        <property name="pointcut" ref="eatPointcut"></property>
    </bean>
    <!-- 调用ProxyFactoryBean产生代理对象 （proxyInterfaces）  -->
    <bean id="manProxy" class="org.springframework.aop.framework.ProxyFactoryBean">
        <property name="target" ref="man"></property>
        <property name="interceptorNames" value="eatHelperAdvisor"></property>
        <property name="proxyInterfaces" value="springmvc.EatAble"></property>
    </bean>

7，过滤器和拦截器的区别：
　　①拦截器是基于Java的反射机制的，而过滤器是基于函数回调。
　　②拦截器不依赖与servlet容器，过滤器依赖与servlet容器。
　　③拦截器只能对action请求起作用，而过滤器则可以对几乎所有的请求起作用。
　　④拦截器可以访问action上下文、值栈里的对象，而过滤器不能访问。
　　⑤在action的生命周期中，拦截器可以多次被调用，而过滤器只能在容器初始化时被调用一次。
　　⑥拦截器可以获取IOC容器中的各个bean，而过滤器就不行，这点很重要，在拦截器里注入一个service，可以调用业务逻辑。

8，gc 
年轻代（存放实例化的对象）
年轻代分为三个区：Eden和两个存活区（Survivor0和Survivor1），分别占内存的80%、10%、10%
使用“停止-复制（Stop-and-copy）”清理法（将Eden区和一个Survivor中仍然存活的对象拷贝到另一个Survivor中）
当Eden区满时，就执行一次MinorGC，并将剩余存活的对象都添加到Surivivor0，回收Eden中的没有存活的对象。
当Surivivor0页都满了的时候，就将仍然存活的存到Surivivor1中，回收Surivivor0中的对象
Surivivor0和Surivivor1依次去存，当两个存活区切换了几次后（HotSpot默认是15次），将仍然存活的对象复制到老年代
2.老年代的GC（存放较大的实例化的对象和在年轻代中存活了足够久的对象）
老年代GC用的是标记-整理算法，即标记存活的对象，向一端移动，保证内存的完整性，然后将未标记的清掉
3.永久代的GC（存放常量、类）
说明：在JDK1.6版本之后，永久代就要被取消掉了，只留下年轻代和老年代

9，
(1) 连通性：
注册中心负责服务地址的注册与查找，相当于目录服务，服务提供者和消费者只在启动时与注册中心交互，注册中心不转发请求，压力较小
监控中心负责统计各服务调用次数，调用时间等，统计先在内存汇总后每分钟一次发送到监控中心服务器，并以报表展示
服务提供者向注册中心注册其提供的服务，并汇报调用时间到监控中心，此时间不包含网络开销
服务消费者向注册中心获取服务提供者地址列表，并根据负载算法直接调用提供者，同时汇报调用时间到监控中心，此时间包含网络开销
注册中心，服务提供者，服务消费者三者之间均为长连接，监控中心除外
注册中心通过长连接感知服务提供者的存在，服务提供者宕机，注册中心将立即推送事件通知消费者
注册中心和监控中心全部宕机，不影响已运行的提供者和消费者，消费者在本地缓存了提供者列表
注册中心和监控中心都是可选的，服务消费者可以直连服务提供者
(2) 健状性：
监控中心宕掉不影响使用，只是丢失部分采样数据
数据库宕掉后，注册中心仍能通过缓存提供服务列表查询，但不能注册新服务
注册中心对等集群，任意一台宕掉后，将自动切换到另一台
注册中心全部宕掉后，服务提供者和服务消费者仍能通过本地缓存通讯
服务提供者无状态，任意一台宕掉后，不影响使用
服务提供者全部宕掉后，服务消费者应用将无法使用，并无限次重连等待服务提供者恢复
(3) 伸缩性：

注册中心为对等集群，可动态增加机器部署实例，所有客户端将自动发现新的注册中心
服务提供者无状态，可动态增加机器部署实例，注册中心将推送新的服务提供者信息给消费者

10，java.util.List中有一个subList方法，用来返回一个list的一部分的视图

11，数据库隔离机制

                脏读  不可重复读   幻读
Read uncommitted    √   √   √
Read committed      ×   √   √
Repeatable read     ×   ×   √
Serializable        ×   ×   ×


Read uncommitted 读未提交
公司发工资了，领导把5000元打到singo的账号上，但是该事务并未提交，而singo正好去查看账户，发现工资已经到账，是5000元整，非常高兴。可是不幸的是，领导发现发给singo的工资金额不对，是2000元，于是迅速回滚了事务，修改金额后，将事务提交，最后singo实际的工资只有2000元，singo空欢喜一场。
出现上述情况，即我们所说的脏读 ，两个并发的事务，“事务A：领导给singo发工资”、“事务B：singo查询工资账户”，事务B读取了事务A尚未提交的数据。


Read committed 读提交
singo拿着工资卡去消费，系统读取到卡里确实有2000元，而此时她的老婆也正好在网上转账，把singo工资卡的2000元转到另一账户，并在singo之前提交了事务，当singo扣款时，系统检查到singo的工资卡已经没有钱，扣款失败，singo十分纳闷，明明卡里有钱，为何

出现上述情况，即我们所说的不可重复读 ，两个并发的事务，“事务A：singo消费”、“事务B：singo的老婆网上转账”，事务A事先读取了数据，事务B紧接了更新了数据，并提交了事务，而事务A再次读取该数据时，数据已经发生了改变。


Repeatable read 重复读
当隔离级别设置为Repeatable read时，可以避免不可重复读。当singo拿着工资卡去消费时，一旦系统开始读取工资卡信息（即事务开始），singo的老婆就不可能对该记录进行修改，也就是singo的老婆不能在此时转账。

singo的老婆工作在银行部门，她时常通过银行内部系统查看singo的信用卡消费记录。有一天，她正在查询到singo当月信用卡的总消费金额 （select sum(amount) from transaction where month = 本月）为80元，而singo此时正好在外面胡吃海塞后在收银台买单，消费1000元，即新增了一条1000元的消费记录（insert transaction ... ），并提交了事务，随后singo的老婆将singo当月信用卡消费的明细打印到A4纸上，却发现消费总额为1080元，singo的老婆很诧异，以为出 现了幻觉，幻读就这样产生了。

Serializable 序列化
Serializable是最高的事务隔离级别，同时代价也花费最高，性能很低，一般很少使用，在该级别下，事务顺序执行，不仅可以避免脏读、不可重复读，还避免了幻像读。

12，
netty 对比 tomcat
实现更少的资源占用（CPU, Memory）和单个业务服务器更高的并发。

13，java 内存模型 Java 虚拟机具有一个堆，堆是运行时数据区域，所有类实例和数组的内存均从此处分配。
堆是Java代码可及的内存，留给开发人员使用的；非堆是JVM留给自己用的

增量式GC就是通过一定的回收算法，把一个长时间的中断，划分为很多个小的中断，通过这种方式减少GC对用户程序的影响。虽然，增量式GC在整体性能上可能不如普通GC的效率高，但是它能够减少程序的最长停顿时间。


-------------------

http 协议
原文
http://www.cnblogs.com/dinglang/archive/2012/02/11/2346430.html

------
所谓协议，就是指双方遵循的规范。
osi 七层参考协议，应用层（http协议） --> 表示层 --> 会话层 --> 传输层（tcp udp） --> 网络层 --> 数据链路层 -->　物理层

------
tcp/udp
tcp 可靠性，耗性能，要和服务器连接，有状态
udp 不可靠性，无状态

------
http，利用tcp，请求之后，服务器关闭连接，继承了可靠性，消耗性能少，因此无状态，为了清楚客户端的状态，有了session

------
抓包工具
Fiddler
wireshark
Charles
httpwatch

------
请求头（消息头）包含（客户机请求的服务器主机名，客户机的环境信息等）：
Accept：用于告诉服务器，客户机支持的数据类型  （例如：Accept:text/html,image/*）
Accept-Charset：用于告诉服务器，客户机采用的编码格式
Accept-Encoding：用于告诉服务器，客户机支持的数据压缩格式
Accept-Language：客户机语言环境
Host:客户机通过这个服务器，想访问的主机名
If-Modified-Since：客户机通过这个头告诉服务器，资源的缓存时间
Referer：客户机通过这个头告诉服务器，它（客户端）是从哪个资源来访问服务器的（防盗链）
User-Agent：客户机通过这个头告诉服务器，客户机的软件环境（操作系统，浏览器版本等）
Cookie：客户机通过这个头，将Coockie信息带给服务器
Connection：告诉服务器，请求完成后，是否保持连接
Date：告诉服务器，当前请求的时间
------
响应头(消息头)包含:
Location：这个头配合302状态吗，用于告诉客户端找谁
Server：服务器通过这个头，告诉浏览器服务器的类型
Content-Encoding：告诉浏览器，服务器的数据压缩格式
Content-Length：告诉浏览器，回送数据的长度
Content-Type：告诉浏览器，回送数据的类型
Last-Modified：告诉浏览器当前资源缓存时间
Refresh：告诉浏览器，隔多长时间刷新
Content-Disposition：告诉浏览器以下载的方式打开数据。例如： context.Response.AddHeader("Content-Disposition","attachment:filename=aa.jpg");                                        context.Response.WriteFile("aa.jpg");
Transfer-Encoding：告诉浏览器，传送数据的编码格式
ETag：缓存相关的头（可以做到实时更新）
Expries：告诉浏览器回送的资源缓存多长时间。如果是-1或者0，表示不缓存
Cache-Control：控制浏览器不要缓存数据   no-cache
Pragma：控制浏览器不要缓存数据          no-cache

Connection：响应完成后，是否断开连接。  close/Keep-Alive
Date：告诉浏览器，服务器响应时间

状态行：  例如：  HTTP/1.1  200 OK   （协议的版本号是1.1  响应状态码为200  响应结果为 OK）

实体内容（实体头）：响应包含浏览器能够解析的静态内容，例如：html，纯文本，图片等等信息
------http请求过程大致步骤就是：浏览器先向服务器发送请求，服务器接收到请求后，做相应的处理，然后封装好响应报文，再回送给浏览器。浏览器拿到响应报文后，再通过 浏览器引擎去渲染网页，解析DOM树，ｊａｖａｓｃｒｉｐｔ引擎解析并执行脚本操作，插件去干插件该干的事儿



----------------------

BigDecimal
------
java.lang.ArithmeticException: Rounding necessary

精度丢失报错，BigDecimal数据类型精度要求高，需要手动设置舍入类型
bigDecimal.setScale(2,BigDecimal.ROUND_HALF_UP);

------
舍入模式

1、ROUND_UP
舍入远离零的舍入模式。
在丢弃非零部分之前始终增加数字(始终对非零舍弃部分前面的数字加1)。
注意，此舍入模式始终不会减少计算值的大小。1.21 ->1.31.26 ->1.32、ROUND_DOWN
接近零的舍入模式。
在丢弃某部分之前始终不增加数字(从不对舍弃部分前面的数字加1，即截短)。
注意，此舍入模式始终不会增加计算值的大小。1.21 ->1.2
1.26 ->1.23、ROUND_CEILING
接近正无穷大的舍入模式。
如果 BigDecimal 为正，则舍入行为与 ROUND_UP 相同;
如果为负，则舍入行为与 ROUND_DOWN 相同。
注意，此舍入模式始终不会减少计算值。1.21 ->1.3
-1.26 ->-1.2
4、ROUND_FLOOR
接近负无穷大的舍入模式。
如果 BigDecimal 为正，则舍入行为与 ROUND_DOWN 相同;
如果为负，则舍入行为与 ROUND_UP 相同。
注意，此舍入模式始终不会增加计算值。1.26 ->1.2
-1.21 ->-1.25、ROUND_HALF_UP
向“最接近的”数字舍入，如果与两个相邻数字的距离相等，则为向上舍入的舍入模式。
如果舍弃部分 >= 0.5，则舍入行为与 ROUND_UP 相同;否则舍入行为与 ROUND_DOWN 相同。
注意，这是我们大多数人在小学时就学过的舍入模式(四舍五入)。
6、ROUND_HALF_DOWN
向“最接近的”数字舍入，如果与两个相邻数字的距离相等，则为上舍入的舍入模式。
如果舍弃部分 > 0.5，则舍入行为与 ROUND_UP 相同;否则舍入行为与 ROUND_DOWN 相同(五舍六入)。
7、ROUND_HALF_EVEN
向“最接近的”数字舍入，如果与两个相邻数字的距离相等，则向相邻的偶数舍入。
如果舍弃部分左边的数字为奇数，则舍入行为与 ROUND_HALF_UP 相同;
如果为偶数，则舍入行为与 ROUND_HALF_DOWN 相同。
注意，在重复进行一系列计算时，此舍入模式可以将累加错误减到最小。
此舍入模式也称为“银行家舍入法”，主要在美国使用。四舍六入，五分两种情况。
如果前一位为奇数，则入位，否则舍去。
以下例子为保留小数点1位，那么这种舍入方式下的结果。
1.15>1.2 1.25>1.2
8、ROUND_UNNECESSARY
断言请求的操作具有精确的结果，因此不需要舍入。
如果对获得精确结果的操作指定此舍入模式，则抛出ArithmeticException。



---------------------------
java rmi (远程方法调用)
------
原理
应用了代理模式来封装了本地存根与真实的远程对象进行通信的细节。


------
例子

IService接口：

import java.rmi.Remote; 
import java.rmi.RemoteException; 
public interface IService extends Remote { 
  //声明服务器端必须提供的服务 
  String service(String content) throws RemoteException; 
}
---
ServiceImpl实现类：

import java.rmi.RemoteException; 
//UnicastRemoteObject用于导出的远程对象和获得与该远程对象通信的存根。 
import java.rmi.server.UnicastRemoteObject; 

public class ServiceImpl extends UnicastRemoteObject implements IService { 

  private String name; 

  public ServiceImpl(String name) throws RemoteException { 
    this.name = name; 
  } 
  @Override 
  public String service(String content) { 
    return "server >> " + content; 
  } 
}
---Server类：

/* 
* Context接口表示一个命名上下文，它由一组名称到对象的绑定组成。 
* 它包含检查和更新这些绑定的一些方法。 
*/ 
import javax.naming.Context; 
/* 
* InitialContext类是执行命名操作的初始上下文。    
* 该初始上下文实现 Context 接口并提供解析名称的起始点。 
*/ 
import javax.naming.InitialContext; 
public class Server { 
  public static void main(String[] args) { 
    try { 
      //实例化实现了IService接口的远程服务ServiceImpl对象 
      IService service02 = new ServiceImpl("service02"); 
      //初始化命名空间 
      Context namingContext = new InitialContext(); 
      //将名称绑定到对象,即向命名空间注册已经实例化的远程服务对象 
      namingContext.rebind("rmi://localhost/service02", service02); 
    } catch (Exception e) { 
      e.printStackTrace(); 
    } 
    System.out.println("服务器向命名表注册了1个远程服务对象！"); 
  } 
}

---

Client类：

import javax.naming.Context; 
import javax.naming.InitialContext; 

public class Client { 
  public static void main(String[] args) { 
    String url = "rmi://localhost/"; 
    try { 
      Context namingContext = new InitialContext(); 
      // 检索指定的对象。 即找到服务器端相对应的服务对象存根 
      IService service02 = (IService) namingContext.lookup(url 
          + "service02"); 
      Class stubClass = service02.getClass(); 
      System.out.println(service02 + " 是 " + stubClass.getName() 
          + " 的实例！"); 
      // 获得本底存根已实现的接口类型 
      Class[] interfaces = stubClass.getInterfaces(); 
      for (Class c : interfaces) { 
        System.out.println("存根类实现了 " + c.getName() + " 接口！"); 
      } 
      System.out.println(service02.service("你好！")); 
    } catch (Exception e) { 
      e.printStackTrace(); 
    } 
  } 
}
---原理时序图



---------------------------


java的历史
---
1991 01
绿色计划，项旨在为家用电器提供支持，使这些电器智能化并且能够彼此交互。而且这些家电可以由远程客户端控制。

1993
家庭消费电子产品 -->电视盒机顶盒的相关平台 --> 支持浏览器（1994）

------
版本发布

1997年2月
JDK1.1版本发布。主要特点是JDBC、RMI、内部类。
1998年12月
JDK1.2版本发布，代号Playground。该版本通常被称为Java 2版本，是见证重大转变的最流行版本。主要特点是集合框架、JIT编译器、策略工具、Java基础类、Java二维类库和JDBC改进。

2000年5月
JDK1.3版本发布，代号Kestrel。
2002年2月
J2SE1.4版本发布，代号Merlin。主要特点是XML处理、Java打印、支持日志、JDBC 3.0、断言和正则表达式处理。
2004年9月
J2SE5.0发布，代号Tiger。主要特点是支持泛型、自动装箱、注释处理、Instrumentation。
2006年12月
Java SE 6版本发布，代号Mustang。主要特点是支持脚本语言、JDBC4.0、Java编译API并整合了Web服务。
2011年7月
Java SE 7.0版本发布，代号Dolphin。这个版本距上次发布有5年之久，并且只有这个版本花费了这么久。主要特点是支持动态语言、Java nio包、多重异常处理、try with resourece功能和诸多小的增强。

-----------------------


分布式事务
------
例子

第一阶段，张老师作为“协调者”，给小强和小明（参与者、节点）发微信，组织他们俩明天8点在学校门口集合，一起去爬山，然后开始等待小强和小明答复。

第二阶段，如果小强和小明都回答没问题，那么大家如约而至。如果小强或者小明其中一人回答说“明天没空，不行”，那么张老师会立即通知小强和小明“爬山活动取消”。

细心的读者会发现，这个过程中可能有很多问题的。如果小强没看手机，那么张老师会一直等着答复，小明可能在家里把爬山装备都准备好了却一直等着张老师确认信息。更严重的是，如果到明天8点小强还没有答复，那么就算“超时”了，那小明到底去还是不去集合爬山呢？


--------------------


mybatis中的#和$的区别

------#相当于对数据 加上 双引号，$相当于直接显示数据

1. #将传入的数据都当成一个字符串，会对自动传入的数据加一个双引号。如：order by #user_id#，如果传入的值是111,那么解析成sql时的值为order by "111", 如果传入的值是id，则解析成的sql为order by "id".
　　
2. $将传入的数据直接显示生成在sql中。如：order by $user_id$，如果传入的值是111,那么解析成sql时的值为order by user_id,  如果传入的值是id，则解析成的sql为order by id.
　　
3. #方式能够很大程度防止sql注入。

4.$方式无法防止Sql注入。

5.$方式一般用于传入数据库对象，例如传入表名.
　　
6.一般能用#的就别用$.


MyBatis排序时使用order by 动态参数时需要注意，用$而不是#

---------------------


java web
------
Java Web，是用Java技术来解决相关web互联网领域的技术总和。web包括：web服务器和web客户端两部分。Java在客户端的应用有java applet，不过使用得很少，Java在服务器端的应用非常的丰富，比如Servlet，JSP和第三方框架等等。Java技术对Web领域的发展注入了强大的动力。

------
jsp

JSP全名为Java Server Pages，中文名叫java服务器页面，其根本是一个简化的Servlet设计。（目的，动态化网页）

有自带的标签库，
JSTL表达式语言可以使用标记格式方便地访问JSP的隐含对象和JavaBeans组件，JSTL的核心标记提供了流程和循环控制功能。

<c:out> 输出

<c:set> 保存数据

<c:remove> 删除数据

（好像mybatis里的标签）

------
servlet

Servlet（Server Applet）是Java Servlet的简称，称为小服务程序或服务连接器，用Java编写的服务器端程序，主要功能在于交互式地浏览和修改数据，生成动态Web内容。

------
servlet 响应过程

客户端发送请求至服务器端；
服务器将请求信息发送至 Servlet；
Servlet 生成响应内容并将其传给服务器。响应内容动态生成，通常取决于客户端的请求；
服务器将响应返回给客户端。
-----
具体过程

客户端请求该 Servlet；
加载 Servlet 类到内存；
实例化并调用init()方法初始化该 Servlet；
service()（根据请求方法不同调用doGet() 或者 doPost()，此外还有doHead()、doPut()、doTrace()、doDelete()、doOptions()、destroy())。
加载和实例化 Servlet。这项操作一般是动态执行的。然而，Server 通常会提供一个管理的选项，用于在 Server 启动时强制装载和初始化特定的 Servlet。

Server 创建一个 Servlet的实例
第一个客户端的请求到达 Server
Server 调用 Servlet 的 init() 方法（可配置为 Server 创建 Servlet 实例时调用，在 web.xml 中  标签下配置  标签，配置的值为整型，值越小 Servlet 的启动优先级越高）
一个客户端的请求到达 Server
Server 创建一个请求对象，处理客户端请求
Server 创建一个响应对象，响应客户端请求
Server 激活 Servlet 的 service() 方法，传递请求和响应对象作为参数
service() 方法获得关于请求对象的信息，处理请求，访问其他资源，获得需要的信息
service() 方法使用响应对象的方法，将响应传回Server，最终到达客户端。service()方法可能激活其它方法以处理请求，如 doGet() 或 doPost() 或程序员自己开发的新的方法。
对于更多的客户端请求，Server 创建新的请求和响应对象，仍然激活此 Servlet 的 service() 方法，将这两个对象作为参数传递给它。如此重复以上的循环，但无需再次调用 init() 方法。一般 Servlet 只初始化一次(只有一个对象)，当 Server 不再需要 Servlet 时（一般当 Server 关闭时），Server 调用 Servlet 的 destroy() 方法。




---------------------------------------


jdbc 
------
jdbc 作用
简单地说，JDBC 可做三件事：与 数据库建立连接、发送 操作数据库的语句并处理结果。下列代码段给出了以上三步的基本示例：
Connection con = DriverManager.getConnection("jdbc:odbc:wombat","login",
"password");
Statement stmt = con.createStatement();
ResultSet rs = stmt.executeQuery("SELECT a, b, c FROM Table1");
while (rs.next()) {
int x = rs.getInt("a");
String s = rs.getString("b");
float f = rs.getFloat("c");
}

------
使用语言
基于关系型数据库的标准SQL语言


---------------------------------------

jdbc hibernate mybatis
------
hibernate 与jdbc对比

相同点：

◆两者都是JAVA的数据库操作中间件。
◆两者对于数据库进行直接操作的对象都不是线程安全的，都需要及时关闭。
◆两者都可以对数据库的更新操作进行显式的事务处理

不同点：

◆使用的SQL语言不同：JDBC使用的是基于关系型数据库的标准SQL语言，Hibernate使用的是HQL(Hibernate query language)语言
◆操作的对象不同：JDBC操作的是数据，将数据通过SQL语句直接传送到数据库中执行，Hibernate操作的是持久化对象，由底层持久化对象的数据更新到数据库中。
◆数据状态不同：JDBC操作的数据是“瞬时”的，变量的值无法与数据库中的值保持一致，而Hibernate操作的数据是可持久的，即持久化对象的数据属性的值是可以跟数据库中的值保持一致的。
------

Hibernate：
    Hibernate是建立在若干POJO通过xml映射文件（或注解）提供的规则映射到数据库表上的。我们可以通过POJO直接操作数据库的数据，他提供的是一种全表映射的模型。相对而言，Hibernate对JDBC的封装程度还是比较高的，我们已经不需要写SQL，只要使用HQL语言就可以了。

使用Hibernate进行编程有以下好处：
    1，消除了代码的映射规则，它全部分离到了xml或者注解里面去配置。
    2，无需在管理数据库连接，它也配置到xml里面了。
    3，一个会话中不需要操作多个对象，只需要操作Session对象。
    4，关闭资源只需要关闭一个Session便可。
    这就是Hibernate的优势，在配置了映射文件和数据库连接文件后，Hibernate就可以通过Session操作，非常容易，消除了jdbc带来的大量代码，大大提高了编程的简易性和可读性。Hibernate还提供了级联，缓存，映射，一对多等功能。Hibernate是全表映射，通过HQL去操作pojo进而操作数据库的数据。

Hibernate的缺点：
    1，全表映射带来的不便，比如更新时需要发送所有的字段。
    2，无法根据不同的条件组装不同的SQL。
    3，对多表关联和复杂的sql查询支持较差，需要自己写sql，返回后，需要自己将数据封装为pojo。
    4，不能有效的支持存储过程。
    5，虽然有HQL，但是性能较差，大型互联网系统往往需要优化sql，而hibernate做不到。
------
mybatis
Mybatis是半自动的框架。解决hibernate的缺点，支持存储过程，方便sql优化，与自己写一些复杂的sql



---------------------------------------


map 四种遍历方式
总结起来，一，获取所有的key，然后getvalue，二，直接获取所有的value，第三种，获取包含所有的key，value的entry，再获取value，第四种，获取包含所有的key，value的entry，然后用迭代器while获取

import java.util.HashMap;
import java.util.Iterator;
import java.util.Map;

public class TestMap {
    public static void main(String[] args) {
        Map map = new HashMap();
        map.put(1, "a");
        map.put(2, "b");
        map.put(3, "ab");
        map.put(4, "ab");
        map.put(4, "ab");// 和上面相同 ， 会自己筛选
        System.out.println(map.size());
        // 第一种：
        /*
         * Set set = map.keySet(); //得到所有key的集合
         * 
         * for (Integer in : set) { String str = map.get(in);
         * System.out.println(in + "     " + str); }
         */
        System.out.println("第一种：通过Map.keySet遍历key和value：");
        for (Integer in : map.keySet()) {
            //map.keySet()返回的是所有key的值
            String str = map.get(in);//得到每个key多对用value的值
            System.out.println(in + "     " + str);
        }
        // 第二种：
        System.out.println("第二种：通过Map.entrySet使用iterator遍历key和value：");
        Iterator> it = map.entrySet().iterator();
        while (it.hasNext()) {
             Map.Entry entry = it.next();
               System.out.println("key= " + entry.getKey() + " and value= " + entry.getValue());
        }
        // 第三种：推荐，尤其是容量大时
        System.out.println("第三种：通过Map.entrySet遍历key和value");
        for (Map.Entry entry : map.entrySet()) {
            //Map.entry 映射项（键-值对）  有几个方法：用上面的名字entry
            //entry.getKey() ;entry.getValue(); entry.setValue();
            //map.entrySet()  返回此映射中包含的映射关系的 Set视图。
            System.out.println("key= " + entry.getKey() + " and value= "
                    + entry.getValue());
        }
        // 第四种：
        System.out.println("第四种：通过Map.values()遍历所有的value，但不能遍历key");
        for (String v : map.values()) {
            System.out.println("value= " + v);
        }
    }
}

---------------------------------------


遍历List集合的三种方法
List list = new ArrayList();
list.add("aaa");
list.add("bbb");
list.add("ccc");
方法一：
超级for循环遍历
for(String attribute : list) {
  System.out.println(attribute);
}
方法二：
对于ArrayList来说速度比较快, 用for循环, 以size为条件遍历:
for(int i = 0 ; i < list.size() ; i++) {
  system.out.println(list.get(i));
}
方法三：
集合类的通用遍历方式, 从很早的版本就有, 用迭代器迭代
Iterator it = list.iterator();
while(it.hasNext()) {
  System.ou.println(it.next);
}
    
    
---------------------------------------


jdk1.7新特性
------
1.switch语句支持字符串变量

case "Tuesday":

2.泛型实例化类型自动推断
ArrayList al1 = new ArrayList<String>();    // Old
ArrayList al2 = new ArrayList<>();           // New
3,在单个catch代码块中捕获多个异常，以及用升级版的类型检查重新抛出异常
catch(IOException | SQLException | Exception ex){
    logger.error(ex);
    throw new MyException(ex.getMessage());
}

来源：http://www.cnblogs.com/hihtml5/p/6483909.html



---------------------------------------

java 

1，Struts2与Spring整合后, 由spring来管理Struts2的Action, bean默认是单实例有情况下,会有如下问题:
1) Struts2的Action是单例,其中的FieldError,actionerror中的错误信息会累加, 即使再次输入了正确的信息,也过不了验证.
2) Struts2的Action是有状态的,他有自己的成员属性, 所以在多线程下,会有线程安全问题，这是最大的问题。
如何解决?
方案一: 就是不用单例, spring中bean的作用域设为prototype,每个请求对应一个Action实例.(建议这样做)
方案二: spring中bean的作用域设为session ,每个session对应一个实例,解决了多线程问题.

springbean的作用域
singleton
在每个Spring IoC容器中一个bean定义对应一个对象实例。
prototype
一个bean定义对应多个对象实例。
request
在一次HTTP请求中，一个bean定义对应一个实例；即每次HTTP请求将会有各自的bean实例， 它们依据某个bean定义创建而成。该作用域仅在基于web的Spring ApplicationContext 情形下有效。
session
在一个HTTP Session 中，一个bean定义对应一个实例。该作用域仅在基于web的SpringApplicationContext 情形下有效。
global session
在一个全局的HTTP Session 中，一个bean定义对应一个实例。典型情况下，仅在使用portlet context的时候有效。该作用域仅在基于web的Spring ApplicationContext 情形下有效。

总结 :
方案一：bean的作用域设为prototype, 不用担心性能不好, 实际测试过,多实例Action性能没问题.
方案二: 有人担心方案一性能不好， 所有才有了方案二, 不知比方案一性能 能高多少？应该不会高多少。

2，Spring MVC Controller默认是单例的：
单例的原因有二：
1、为了性能。
2、不需要多例。
万一必须要定义一个非静态成员变量时候，则通过注解@Scope("prototype")，将其设置为多例模式
threadlocal 可以保证线程安全

threadlocal

在同步机制中，通过对象的锁机制保证同一时间只有一个线程访问变量
以空间换时间,(ThreadLocalMap)ThreadLocal会为每一个线程提供一个独立的变量副本，从而隔离了多个线程对数据的访问冲突。因为每一个线程都拥有自己的变量副本，从而也就没有必要对该变量进行同步了。ThreadLocal提供了线程安全的共享对象，在编写多线程代码时，可以把不安全的变量封装进ThreadLocal。

3，机制：spring mvc的入口是servlet，而struts2是filter，这样就导致了二者的机制不同。 
 性能：spring会稍微比struts快。spring mvc是基于方法的设计，而sturts是基于类，每次发一次请求都会实例一个action，每个action都会被注入属性，而spring基于方法，粒度更细，但要小心把握像在servlet控制数据一样。spring3 mvc是方法级别的拦截，拦截到方法后根据参数上的注解，把request数据注入进去，在spring3 mvc中，一个方法对应一个request上下文。而struts2框架是类级别的拦截，每次来了请求就创建一个Action，然后调用setter getter方法把request中的数据注入；struts2实际上是通过setter getter方法与request打交道的；struts2中，一个Action对象对应一个request上下文。 

参数传递：struts是在接受参数的时候，可以用属性来接受参数，这就说明参数是让多个方法共享的。

设计思想上：struts更加符合oop的编程思想， spring就比较谨慎，在servlet上扩展。 

intercepter的实现机制：struts有以自己的interceptor机制，spring mvc用的是独立的AOP方式。这样导致struts的配置文件量还是比spring mvc大，虽然struts的配置能继承，所以我觉得论使用上来讲，spring mvc使用更加简洁，开发效率Spring MVC确实比struts2高。spring mvc是方法级别的拦截，一个方法对应一个request上下文，而方法同时又跟一个url对应，所以说从架构本身上spring3 mvc就容易实现restful url。struts2是类级别的拦截，一个类对应一个request上下文；实现restful url要费劲，因为struts2 action的一个方法可以对应一个url；而其类属性却被所有方法共享，这也就无法用注解或其他方式标识其所属方法了。spring3 mvc的方法之间基本上独立的，独享request response数据，请求数据通过参数获取，处理结果通过ModelMap交回给框架方法之间不共享变量，而struts2搞的就比较乱，虽然方法之间也是独立的，但其所有Action变量是共享的，这不会影响程序运行，却给我们编码，读程序时带来麻烦。 

另外，spring3 mvc的验证也是一个亮点，支持JSR303，处理ajax的请求更是方便，只需一个注解@ResponseBody ，然后直接返回响应文本即可。
总结:
这样也说明了SpringMVC还有Servlet都是方法的线程安全，所以在类方法声明的私有或者公有变量不是线程安全的，而struts2的确实是线程安全的。

4，多线程  扩展thread 类，继承， 实现 Runnable 接口  实现Runnable接口比继承Thread类所具有的优势：

1）：适合多个相同的程序代码的线程去处理同一个资源
2）：可以避免java中的单继承的限制
3）：增加程序的健壮性，代码可以被多个线程共享，代码和数据独立

sleep（） 直接停滞，一定等到指定时间后，再调用，阻塞当前线程，留一定时间给其它线程执行

wait（） 等待，暂时失去对象的机锁，其它线程可以访问，wait()使用notify或者notifyAlll或者指定睡眠时间来唤醒当前等待池中的线程，wiat()必须放在synchronized block中，否则会在program runtime时扔出”java.lang.IllegalMonitorStateException“异常。


sysnchronized  方法，只允许一个线程访问，修饰代码块，只允许一个线程访问该代码块，修饰关键字，表示该资源实行互斥访问

5，spring 源码  getBean --》 doGetBean --》createBean --》 doCreateBean --》createBeanInstance --》 instantiateBean


populateBean --》applyPropertyValues --》resolveValueIfNecessary --》setPropertyValue



6， <bean id="eatHelper" class="springmvc.EatHelper"></bean>
    <bean id="man" class="springmvc.Man"></bean>
    
    <!-- 配置pointcut  切点相当于事故发生的地点 吃饭的这个动作在系统的什么地方出发的呢？-->
    <bean id="eatPointcut" class="org.springframework.aop.support.JdkRegexpMethodPointcut">
        <property name="pattern" value=".*eat"></property>
    </bean>
    <!-- 通知  通知相当于事故时间及内容,连接切点和通知 -->
    <bean id="eatHelperAdvisor" class="org.springframework.aop.support.DefaultPointcutAdvisor">
        <property name="advice" ref="eatHelper"></property>
        <property name="pointcut" ref="eatPointcut"></property>
    </bean>
    <!-- 调用ProxyFactoryBean产生代理对象 （proxyInterfaces）  -->
    <bean id="manProxy" class="org.springframework.aop.framework.ProxyFactoryBean">
        <property name="target" ref="man"></property>
        <property name="interceptorNames" value="eatHelperAdvisor"></property>
        <property name="proxyInterfaces" value="springmvc.EatAble"></property>
    </bean>

7，过滤器和拦截器的区别：
　　①拦截器是基于Java的反射机制的，而过滤器是基于函数回调。
　　②拦截器不依赖与servlet容器，过滤器依赖与servlet容器。
　　③拦截器只能对action请求起作用，而过滤器则可以对几乎所有的请求起作用。
　　④拦截器可以访问action上下文、值栈里的对象，而过滤器不能访问。
　　⑤在action的生命周期中，拦截器可以多次被调用，而过滤器只能在容器初始化时被调用一次。
　　⑥拦截器可以获取IOC容器中的各个bean，而过滤器就不行，这点很重要，在拦截器里注入一个service，可以调用业务逻辑。

8，gc 
年轻代（存放实例化的对象）
年轻代分为三个区：Eden和两个存活区（Survivor0和Survivor1），分别占内存的80%、10%、10%
使用“停止-复制（Stop-and-copy）”清理法（将Eden区和一个Survivor中仍然存活的对象拷贝到另一个Survivor中）
当Eden区满时，就执行一次MinorGC，并将剩余存活的对象都添加到Surivivor0，回收Eden中的没有存活的对象。
当Surivivor0页都满了的时候，就将仍然存活的存到Surivivor1中，回收Surivivor0中的对象
Surivivor0和Surivivor1依次去存，当两个存活区切换了几次后（HotSpot默认是15次），将仍然存活的对象复制到老年代
2.老年代的GC（存放较大的实例化的对象和在年轻代中存活了足够久的对象）
老年代GC用的是标记-整理算法，即标记存活的对象，向一端移动，保证内存的完整性，然后将未标记的清掉
3.永久代的GC（存放常量、类）
说明：Java7中已经将运行时常量池从永久代移除，在Java 堆（Heap）中开辟了一块区域存放运行时常量池。而在Java8中，已经彻底没有了永久代，将方法区直接放在一个与堆不相连的本地内存区域，这个区域被叫做元空间。 
9，
(1) 连通性：
注册中心负责服务地址的注册与查找，相当于目录服务，服务提供者和消费者只在启动时与注册中心交互，注册中心不转发请求，压力较小
监控中心负责统计各服务调用次数，调用时间等，统计先在内存汇总后每分钟一次发送到监控中心服务器，并以报表展示
服务提供者向注册中心注册其提供的服务，并汇报调用时间到监控中心，此时间不包含网络开销
服务消费者向注册中心获取服务提供者地址列表，并根据负载算法直接调用提供者，同时汇报调用时间到监控中心，此时间包含网络开销
注册中心，服务提供者，服务消费者三者之间均为长连接，监控中心除外
注册中心通过长连接感知服务提供者的存在，服务提供者宕机，注册中心将立即推送事件通知消费者
注册中心和监控中心全部宕机，不影响已运行的提供者和消费者，消费者在本地缓存了提供者列表
注册中心和监控中心都是可选的，服务消费者可以直连服务提供者
(2) 健状性：
监控中心宕掉不影响使用，只是丢失部分采样数据
数据库宕掉后，注册中心仍能通过缓存提供服务列表查询，但不能注册新服务
注册中心对等集群，任意一台宕掉后，将自动切换到另一台
注册中心全部宕掉后，服务提供者和服务消费者仍能通过本地缓存通讯
服务提供者无状态，任意一台宕掉后，不影响使用
服务提供者全部宕掉后，服务消费者应用将无法使用，并无限次重连等待服务提供者恢复
(3) 伸缩性：

注册中心为对等集群，可动态增加机器部署实例，所有客户端将自动发现新的注册中心
服务提供者无状态，可动态增加机器部署实例，注册中心将推送新的服务提供者信息给消费者

10，java.util.List中有一个subList方法，用来返回一个list的一部分的视图

11，数据库隔离机制

                脏读  不可重复读   幻读
Read uncommitted    √   √   √
Read committed      ×   √   √
Repeatable read     ×   ×   √
Serializable        ×   ×   ×


Read uncommitted 读未提交
公司发工资了，领导把5000元打到singo的账号上，但是该事务并未提交，而singo正好去查看账户，发现工资已经到账，是5000元整，非常高兴。可是不幸的是，领导发现发给singo的工资金额不对，是2000元，于是迅速回滚了事务，修改金额后，将事务提交，最后singo实际的工资只有2000元，singo空欢喜一场。
出现上述情况，即我们所说的脏读 ，两个并发的事务，“事务A：领导给singo发工资”、“事务B：singo查询工资账户”，事务B读取了事务A尚未提交的数据。


Read committed 读提交
singo拿着工资卡去消费，系统读取到卡里确实有2000元，而此时她的老婆也正好在网上转账，把singo工资卡的2000元转到另一账户，并在singo之前提交了事务，当singo扣款时，系统检查到singo的工资卡已经没有钱，扣款失败，singo十分纳闷，明明卡里有钱，为何

出现上述情况，即我们所说的不可重复读 ，两个并发的事务，“事务A：singo消费”、“事务B：singo的老婆网上转账”，事务A事先读取了数据，事务B紧接了更新了数据，并提交了事务，而事务A再次读取该数据时，数据已经发生了改变。


Repeatable read 重复读
当隔离级别设置为Repeatable read时，可以避免不可重复读。当singo拿着工资卡去消费时，一旦系统开始读取工资卡信息（即事务开始），singo的老婆就不可能对该记录进行修改，也就是singo的老婆不能在此时转账。

singo的老婆工作在银行部门，她时常通过银行内部系统查看singo的信用卡消费记录。有一天，她正在查询到singo当月信用卡的总消费金额 （select sum(amount) from transaction where month = 本月）为80元，而singo此时正好在外面胡吃海塞后在收银台买单，消费1000元，即新增了一条1000元的消费记录（insert transaction ... ），并提交了事务，随后singo的老婆将singo当月信用卡消费的明细打印到A4纸上，却发现消费总额为1080元，singo的老婆很诧异，以为出 现了幻觉，幻读就这样产生了。

Serializable 序列化
Serializable是最高的事务隔离级别，同时代价也花费最高，性能很低，一般很少使用，在该级别下，事务顺序执行，不仅可以避免脏读、不可重复读，还避免了幻像读。

12，
netty 对比 tomcat
实现更少的资源占用（CPU, Memory）和单个业务服务器更高的并发。

13，java 内存模型 Java 虚拟机具有一个堆，堆是运行时数据区域，所有类实例和数组的内存均从此处分配。
堆是Java代码可及的内存，留给开发人员使用的；非堆是JVM留给自己用的

增量式GC就是通过一定的回收算法，把一个长时间的中断，划分为很多个小的中断，通过这种方式减少GC对用户程序的影响。虽然，增量式GC在整体性能上可能不如普通GC的效率高，但是它能够减少程序的最长停顿时间。



14，加密技术
对称加密以数据加密标准  DES
对称加密采用了对称密码编码技术，它的特点是文件加密和解密使用相同的密钥，即加密密钥也可以用作解密密钥，这种方法在密码学中叫做对称加密算法，对称加密算法使用起来简单快捷，密钥较短，且破译困难，除了数据加密标准（DES），另一个对称密钥加密系统是国际数据加密算法（IDEA），它比DES的加密性好，而且对计算机功能要求也没有那么高

非对称加密通常以RSA

Base64加密算法

Base64加密算法是网络上最常见的用于传输8bit字节代码的编码方式之一，Base64编码可用于在HTTP环境下传递较长的标识信息。例如，在JAVAPERSISTENCE系统HIBEMATE中，采用了Base64来将一个较长的唯一标识符编码为一个字符串，用作HTTP表单和HTTPGETURL中的参数。在其他应用程序中，也常常需要把二进制数据编码为适合放在URL（包括隐藏表单域）中的形式。此时，采用Base64编码不仅比较简短，同时也具有不可读性，即所编码的数据不会被人用肉眼所直接看到。

MD5加密算法

MD5为计算机安全领域广泛使用的一种散列函数，用以提供消息的完整性保护。对MD5加密算法简要的叙述可以为：MD5以512位分组来处理输入的信息，且每一分组又被划分为16个32位子分组，经过了一系列的处理后，算法的输出由四个32位分组组成，将这四个32位分组级联后将生成—个128位散列值。
MD5被广泛用于各种软件的密码认证和钥匙识别上。MD5用的是哈希函数，它的典型应用是对一段信息产生信息摘要，以防止被篡改。MD5的典型应用是对一段Message产生fingerprin指纹，以防止被“篡改”。如果再有—个第三方的认证机构，用MD5还可以防止文件作者的“抵赖”，这就是所谓的数字签名应用。MD5还广泛用于操作系统的登陆认证上，如UNIX、各类BSD系统登录密码、数字签名等诸多方。


15，线程池
Executor

ThreadPoolExecutor的使用需要注意以下概念：

若线程池中的线程数量小于corePoolSize，即使线程池中的线程都处于空闲状态，也要创建新的线程来处理被添加的任务。
若线程池中的线程数量等于 corePoolSize且缓冲队列 workQueue未满，则任务被放入缓冲队列。
若线程池中线程的数量大于corePoolSize且缓冲队列workQueue满，且线程池中的数量小于maximumPoolSize，则建新的线程来处理被添加的任务。
若线程池中线程的数量大于corePoolSize且缓冲队列workQueue满，且线程池中的数量等于maximumPoolSize，那么通过 handler所指定的策略来处理此任务。
当线程池中的线程数量大于corePoolSize时，如果某线程空闲时间超过keepAliveTime，线程将被终止。
 


16，设计题和数据库体

17，用栈实现队列

public class Demo07 {
    Stack<Integer> stack1 = new Stack<Integer>();
    Stack<Integer> stack2 = new Stack<Integer>();

    public void push(int node) {
        stack1.push(node);
    }

    public int pop() {
        if(stack2.size()<=0){
            while(stack1.size()>0){
                /*int data = stack1.peek();//查看栈顶元素，但不移除它
                stack1.pop();//弹出栈顶元素
                stack2.push(data);//压入
                 */                             
                stack2.push(stack1.pop());
            }
        }

        if(stack2.isEmpty()){
            try {
                throw new Exception("queue is empty.");
            } catch (Exception e) {
            }
        }
        /**
         * int head = stack2.peek();
         * stack2.pop();
         */
        int head = stack2.pop();
        return head;

    }

18，哈希和一致性哈希的区别

一致性哈希算法(consistent hashing) ，主要用于发布式缓存中。在一些高速发展的web系统中，传统的哈希函数，如hash取模法，存在明显缺陷。随着系统访问压力的增长，缓存系统不得不通过增加机器节点的方式提高集群的相应速度和数据承载量。增加机器意味着，如果按照hash取模的方式，在增加机器节点的这一时刻，大量的缓存不能命中，缓存数据需要重新建立，甚至是整体迁移，这一瞬间会给DB带来极高的系统负载。

一致性哈希基本解决了在P2P环境中最为关键的问题——如何在动态的网络拓扑中分布存储和路由

哈希
插入和删除只需要接近0(1）的时间级（由于碰撞冲突的存在，实际时间大于这个值）
哈希表也有一些缺点。它通常是基于数组的，数组创建后难于扩展。


19，hashcode方法和equals方法的联系
hashcode 变量内存地址 的hash值
equals方法主要用于判断对象的内存地址引用是不是同一个地址
==是地址判断

重写，要保持一致
如果 equals true  hashcode 相等
如果 equals false hashcode 不相等 
--- 
equals重写约定

自反性:  x.equals(x) 一定是true。

对null:  x.equals(null) 一定是false。

对称性:  x.equals(y)和y.equals(x)结果一致。

传递性：x.equals(y), 并且y.equals(z)，那么x.equals(z)。

一致性：对于任何非null的引用值x和y，只要equals的比较操作在对象中所用的信息没有被修改，多次调用x.equals(y)返回结果一致;因此，equals方法里面不应该依赖任何不可靠的资源。
hashCode重写约定
在某个运行时期间，只要对象的变化不会影响equals方法的决策结果，那么，在这个期间，无论调用多少次hashCode，都必须返回同一个散列码。

通过equals调用返回true的2个对象的hashCode一定一样。

通过equasl返回false的2个对象的散列码不需要不同，也就是他们的hashCode方法的返回值允许出现相同的情况。

总结一句话：等价的(调用equals返回true)对象必须产生相同的散列码。不等价的对象，不要求产生的散列码不相同。
注：关于hashCode的约定，参考哈希表的定义，就变得很好理解。因为要同时兼顾时间空间，所以允许一定的哈希冲突，但必须保证等价对象的哈希值相等（当然哈希函数还是要尽量减少冲突的）。
为了更好地遵从上面的约定，可以如此规范：重写了euqls方法的对象必须同时重写hashCode方法。

---

20，新生代垃圾回收机制
新生代通常存活时间较短，因此基于复制算法来进行回收，所谓复制算法就是扫描出存活的对象，并复制到一块新的完全未使用的空间中，对应于新生代，就是在Eden和其中一个Survivor，复制到另一个之间Survivor空间中，然后清理掉原来就是在Eden和其中一个Survivor中的对象。

21，threadlocal
get
set
remove
initialValue
在同步机制中，通过对象的锁机制保证同一时间只有一个线程访问变量。这时该变量是多个线程共享的，使用同步机制要求程序慎密地分析什么时候对变量进行读写，什么时候需要锁定某个对象，什么时候释放对象锁等繁杂的问题，程序设计和编写难度相对较大。
变量副本 （以空间换时间）


22，对象实例化的五种情况
Java中创建（实例化）对象的五种方式
1、用new语句创建对象，这是最常见的创建对象的方法。
2、通过工厂方法返回对象，如：String str = String.valueOf(23); 
3、运用反射手段,调用java.lang.Class或者java.lang.reflect.Constructor类的newInstance()实例方法。如：Object obj = Class.forName("java.lang.Object").newInstance(); 
4、调用对象的clone()方法。
5、通过I/O流（包括反序列化），如运用反序列化手段，调用java.io.ObjectInputStream对象的 readObject()方法。

23，三次握手，五次挥手，客户端和服务端的状态


24，数据库锁机制
锁包括行级锁、表级锁、悲观锁、乐观锁

MySql的innodb存储引擎默认是行级锁。特点:开锁大，加锁慢；会出现死锁；锁定粒度最小，发生锁冲突的概率最低，并发度也最高。适合于有大量按索引更新少量不同数据，同时又有并发查询的应用，如一些在线事务处理系统。

表级锁:5种

   行共享 (ROW SHARE) – 禁止排他锁定表，与行排他类似，区别是别的事务还可以在此表上加任何排他锁。（除排他（exclusive）外）
   行排他(ROW EXCLUSIVE) – 禁止使用排他锁和共享锁，其他事务依然可以并发地对相同数据表执行查询，插入，更新，删除操作，或对表内数据行加锁的操作，但不能有其他的排他锁（自身是可以的，没发现有什么用）
   共享锁(SHARE) - 锁定表，对记录只读不写，多个用户可以同时在同一个表上应用此锁，在表没有被任何DML操作时，多个事务都可加锁，但只有在仅一个事务加锁的情况下只有此事务才能对表更新；当表已经被更新或者指定要更新时（select for update），任何事务都不能加此锁了。
   共享行排他(SHARE ROW EXCLUSIVE) – 比共享锁更多的限制，禁止使用共享锁及更高的锁，在表没有被任何DML操作时，只有一个事务可以加锁，可以更新，书上说别的事务可以使用select for update锁定选中的数据行，可是实验后没被验证。
   排他(EXCLUSIVE) – 限制最强的表锁，仅允许其他用户查询该表的行。禁止修改和锁定表
   
   
   
   
   
   
   java 垃圾回收机制
   ------
   
   新生代
   
   新生代内存按照8:1:1的比例分为一个eden区和两个survivor(survivor0,survivor1)区。一个Eden区，两个 Survivor区(一般而言)。大部分对象在Eden区中生成。回收时先将eden区存活对象复制到一个survivor0区，然后清空eden区，当这个survivor0区也存放满了时，则将eden区和survivor0区存活对象复制到另一个survivor1区，然后清空eden和这个survivor0区，此时survivor0区是空的，然后将survivor0区和survivor1区交换，即保持survivor1区为空， 如此往复。
   
   -----
   老年代
   
   在年轻代中经历了N次垃圾回收后仍然存活的对象，就会被放到年老代中。因此，可以认为年老代中存放的都是一些生命周期较长的对象。
   
   ------
   年轻代（存放实例化的对象）
   年轻代分为三个区：Eden和两个存活区（Survivor0和Survivor1），分别占内存的80%、10%、10%
   使用“停止-复制（Stop-and-copy）”清理法（将Eden区和一个Survivor中仍然存活的对象拷贝到另一个Survivor中）
   当Eden区满时，就执行一次MinorGC，并将剩余存活的对象都添加到Surivivor0，回收Eden中的没有存活的对象。
   当Surivivor0页都满了的时候，就将仍然存活的存到Surivivor1中，回收Surivivor0中的对象
   Surivivor0和Surivivor1依次去存，当两个存活区切换了几次后（HotSpot默认是15次），将仍然存活的对象复制到老年代
   2.老年代的GC（存放较大的实例化的对象和在年轻代中存活了足够久的对象）
   老年代GC用的是标记-整理算法，即标记存活的对象，向一端移动，保证内存的完整性，然后将未标记的清掉
   3.永久代的GC（存放常量、类）
   说明：Java7中已经将运行时常量池从永久代移除，在Java 堆（Heap）中开辟了一块区域存放运行时常量池。而在Java8中，已经彻底没有了永久代，将方法区直接放在一个与堆不相连的本地内存区域，这个区域被叫做元空间。 





tomcat 
------
1，目录结构
bin : 存放启动和关闭tomcat脚本 
conf : 包含不同的配置文件,server.xml(Tomcat的主要配置文件)和web.xml 
work : 存放jsp编译后产生的class文件 
webapp: 存放应用程序示例，以后你要部署的应用程序也要放到此目录 
logs : 存放日志文件 
lib/japser/common : 这三个目录主要存放tomcat所需的jar文件 
------2，jar包加载顺序1、首先加载Tomcat_HOME/lib目录下的jar包 
2、然后加载Tomcat_HOME/webapps/项目名/WEB-INF/lib的jar包 
3、最后加载的是Tomcat_HOME/webapps/项目名/WEB-INF/classes下的类文件 

资源相重时，后面的覆盖前面的
3，server.xml 配置详解


server.xml大致结构


<server>元素
它代表整个容器,是Tomcat实例的顶层元素.由org.apache.catalina.Server接口来定义.它包含一个元素.并且它不能做为任何元素的子元素.
<!-- 一个“Server”是一个提供完整的JVM的独立组件，它可以包含一个或多个“Service”实例。服务器在指定的端口上监听shutdown命令。-->注意：一个“Server”自身不是一个“Container”（容器），因此在这里你
 不可以定义诸如“Valves”或者“Loggers”子组件-->
<!-- 启动Server
 在端口8005处等待关闭命令
 如果接受到"SHUTDOWN"字符串则关闭服务器-->
------tomcat 页面管理 webapp

tomcat tomcat-users.xml 增加   <user username="tomcat" password="123456" roles="manager-gui,manager-script,tomcat,admin-gui,admin-script"/>





java类加载器
-----
类加载器 
在Proxy类中的newProxyInstance（）方法中需要一个ClassLoader类的实例，ClassLoader实际上对应的是类加载器，在Java中主要有一下三种类加载器; 
Booststrap ClassLoader：此加载器采用C++编写，一般开发中是看不到的； 
Extendsion ClassLoader：用来进行扩展类的加载，一般对应的是jre\lib\ext目录中的类; 
AppClassLoader：(默认)加载classpath指定的类，是最常使用的是一种加载器。 






java代理
------
静态代理

优点
1，隐藏委托类的实现
2，解耦，不修改委托类，就可以做额外的处理

动态代理

通过反射机制

传入参数

ClassLoader loader, Class[]interfaces,InvocationHandler h

// 获得与指定类装载器和一组接口相关的代理类类型对象
    Class cl = getProxyClass(loader, interfaces); 

    // 通过反射获取构造函数对象并生成代理类实例
    try { 
        Constructor cons = cl.getConstructor(constructorParams); 
        return (Object) cons.newInstance(new Object[] { h }); 
    } catch (NoSuchMethodException e) { throw new InternalError(e.toString()); .....
}

------

jdk 自带代理

JDK的动态代理依靠接口实现，如果有些类并没有实现接口，则不能使用JDK代理，这就要使用cglib动态代理了。 

------
jdk自带代理

更换接口不方便，一个类绑定一个接口

public static void main(String[] args) {  
        BookFacadeImpl bookFacadeImpl=new BookFacadeImpl();
        BookFacadeProxy proxy = new BookFacadeProxy();  
        BookFacade bookfacade = (BookFacade) proxy.bind(bookFacadeImpl);  
        bookfacade.addBook();  
    }  
------

cgclib 代理

不需要接口

public static void main(String[] args) {      
        BookFacadeImpl1 bookFacade=new BookFacadeImpl1()；
        BookFacadeCglib  cglib=new BookFacadeCglib();  
        BookFacadeImpl1 bookCglib=(BookFacadeImpl1)cglib.getInstance(bookFacade);  
        bookCglib.addBook();  
    }  







------
反射原理

就是在运行期间，如果我们要产生某个类的对象，Java虚拟机（JVM）会检查该类型的Class对象是否已被加载。如果没有被加载，JVM会根据类的名称找到.class文件并加载它。一旦某个类型的Class对象已被加载到内存，就可以用它来产生该类型的所有对象




spring面向切面

------
例子

<bean id="eatHelper" class="springmvc.EatHelper"></bean>
<bean id="man" class="springmvc.Man"></bean>
    
    <!-- 配置pointcut  切点相当于事故发生的地点 吃饭的这个动作在系统的什么地方出发的呢？-->
<bean id="eatPointcut" class="org.springframework.aop.support.JdkRegexpMethodPointcut">
        <property name="pattern" value=".*eat"></property>
</bean>

<!-- 通知  通知相当于事故时间及内容,连接切点和通知 -->
<bean id="eatHelperAdvisor" class="org.springframework.aop.support.DefaultPointcutAdvisor">
        <property name="advice" ref="eatHelper"></property>
        <property name="pointcut" ref="eatPointcut"></property>
</bean>

<!-- 调用ProxyFactoryBean产生代理对象 （proxyInterfaces）  -->
<bean id="manProxy" class="org.springframework.aop.framework.ProxyFactoryBean">
        <property name="target" ref="man"></property>
        <property name="interceptorNames" value="eatHelperAdvisor"></property>
        <property name="proxyInterfaces" value="springmvc.EatAble"></property>
</bean>

------
相关概念

1.通知(Advice): 
通知定义了切面是什么以及何时使用。描述了切面要完成的工作和何时需要执行这个工作。 
2.连接点(Joinpoint): 
程序能够应用通知的一个“时机”，这些“时机”就是连接点，例如方法被调用时、异常被抛出时等等。 
3.切入点(Pointcut) 
通知定义了切面要发生的“故事”和时间，那么切入点就定义了“故事”发生的地点，例如某个类或方法的名称，Spring中允许我们方便的用正则表达式来指定 
4.切面(Aspect) 
通知和切入点共同组成了切面：时间、地点和要发生的“故事” 
5.引入(Introduction) 
引入允许我们向现有的类添加新的方法和属性(Spring提供了一个方法注入的功能） 
6.目标(Target) 
即被通知的对象，如果没有AOP,那么它的逻辑将要交叉别的事务逻辑，有了AOP之后它可以只关注自己要做的事（AOP让他做爱做的事） 
7.代理(proxy) 
应用通知的对象，详细内容参见设计模式里面的代理模式 
8.织入(Weaving) 
把切面应用到目标对象来创建新的代理对象的过程，织入一般发生在如下几个时机: 
(1)编译时：当一个类文件被编译时进行织入，这需要特殊的编译器才可以做的到，例如AspectJ的织入编译器 
(2)类加载时：使用特殊的ClassLoader在目标类被加载到程序之前增强类的字节代码 
(3)运行时：切面在运行的某个时刻被织入,SpringAOP就是以这种方式织入切面的，原理应该是使用了JDK的动态代理技术 





spring 配置
<context:annotation-config>
是用于激活那些已经在spring容器里注册过的bean（无论是通过xml的方式还是通过package sanning的方式）上面的注解。

<context:component-scan>
除了<context:annotation-config>具有的功能之外，还可以在指定的package下扫描以及注册javabean 。(还具有自动将带有@component,@service,@Repository等注解的对象注册到spring容器中的功能。)

<aop:aspectj-autoproxy/>通过aop命名空间的声明自动为spring容器中那些配置@aspectJ切面的bean创建代理，织入切面。

<context:property-placeholder location="classpath:conf/*.properties" ignore-unresolvable="true" >
读取配置文件来源：http://www.cnblogs.com/leiOOlei/p/3713989.html




java 注解------
注解也叫元数据，例如我们常见的@Override和@Deprecated，注解是JDK1.5版本开始引入的一个特性，用于对代码进行说明，可以对包、类、接口、字段、方法参数、局部变量等进行注解

------
作用
生成文档，通过代码里标识的元数据生成javadoc文档。
编译检查，通过代码里标识的元数据让编译器在编译期间进行检查验证。
编译时动态处理，编译时通过代码里标识的元数据动态处理，例如动态生成代码。
运行时动态处理，运行时通过代码里标识的元数据动态处理，例如使用反射注入实例。
------
分类

一类是Java自带的标准注解，包括@Override、@Deprecated和@SuppressWarnings，分别用于标明重写某个方法、标明某个类或方法过时、标明要忽略的警告，用这些注解标明后编译器就会进行检查。
一类为元注解，元注解是用于定义注解的注解，包括@Retention、@Target、@Inherited、@Documented，@Retention用于标明注解被保留的阶段，@Target用于标明注解使用的范围，@Inherited用于标明注解可继承，@Documented用于标明是否生成javadoc文档。
一类为自定义注解，可以根据自己的需求定义注解，并可用元注解对自定义注解进行注解。 

来源：http://blog.csdn.net/wangyangzhizhou/article/details/51698638



spring原理------spring容器高层视图


------spring创建bean流程图





１、ResourceLoader从存储介质中加载Spring配置信息，并使用Resource表示这个配置文件的资源；
２、BeanDefinitionReader读取Resource所指向的配置文件资源，然后解析配置文件。配置文件中每一个解析成一个BeanDefinition对象，并保存到BeanDefinitionRegistry中；
３、容器扫描BeanDefinitionRegistry中的BeanDefinition，使用Java的反射机制自动识别出Bean工厂后处理后器（实现BeanFactoryPostProcessor接口）的Bean，然后调用这些Bean工厂后处理器对BeanDefinitionRegistry中的BeanDefinition进行加工处理。主要完成以下两项工作：
1）对使用到占位符的元素标签进行解析，得到最终的配置值，这意味对一些半成品式的BeanDefinition对象进行加工处理并得到成品的BeanDefinition对象；
2）对BeanDefinitionRegistry中的BeanDefinition进行扫描，通过Java反射机制找出所有属性编辑器的Bean（实现java.beans.PropertyEditor接口的Bean），并自动将它们注册到Spring容器的属性编辑器注册表中（PropertyEditorRegistry）；
4．Spring容器从BeanDefinitionRegistry中取出加工后的BeanDefinition，并调用InstantiationStrategy着手进行Bean实例化的工作；
5．在实例化Bean时，Spring容器使用BeanWrapper对Bean进行封装，BeanWrapper提供了很多以Java反射机制操作Bean的方法，它将结合该Bean的BeanDefinition以及容器中属性编辑器，完成Bean属性的设置工作；
6．利用容器中注册的Bean后处理器（实现BeanPostProcessor接口的Bean）对已经完成属性设置工作的Bean进行后续加工，直接装配出一个准备就绪的Bean。
Spring容器确实堪称一部设计精密的机器，其内部拥有众多的组件和装置。Spring的高明之处在于，它使用众多接口描绘出了所有装置的蓝图，构建好Spring的骨架，继而通过继承体系层层推演，不断丰富，最终让Spring成为有血有肉的完整的框架。所以查看Spring框架的源码时，有两条清晰可见的脉络：
1）接口层描述了容器的重要组件及组件间的协作关系；
2）继承体系逐步实现组件的各项功能。
接口层清晰地勾勒出Spring框架的高层功能，框架脉络呼之欲出。有了接口层抽象的描述后，不但Spring自己可以提供具体的实现，任何第三方组织也可以提供不同实现， 可以说Spring完善的接口层使框架的扩展性得到了很好的保证。纵向继承体系的逐步扩展，分步骤地实现框架的功能，这种实现方案保证了框架功能不会堆积在某些类的身上，造成过重的代码逻辑负载，框架的复杂度被完美地分解开了。
Spring组件按其所承担的角色可以划分为两类：
1）物料组件：Resource、BeanDefinition、PropertyEditor以及最终的Bean等，它们是加工流程中被加工、被消费的组件，就像流水线上被加工的物料；
2）加工设备组件：ResourceLoader、BeanDefinitionReader、BeanFactoryPostProcessor、InstantiationStrategy以及BeanWrapper等组件像是流水线上不同环节的加工设备，对物料组件进行加工处理。
------spring 源码  getBean --》 doGetBean --》createBean --》 doCreateBean --》createBeanInstance --》 instantiateBean


populateBean --》applyPropertyValues --》resolveValueIfNecessary --》setPropertyValue



restful 设计原则
http://api.qc.com/v1/newsfeed: 获取某人的新鲜; http://api.qc.com/v1/friends: 获取某人的好友列表;http://api.qc.com/v1/profile: 获取某人的详细信息;
用HTTP协议里的动词来实现资源的添加，修改，删除等操作。即通过HTTP动词来实现资源的状态扭转：GET    用来获取资源，POST  用来新建资源（也可以用于更新资源），PUT    用来更新资源，DELETE  用来删除资源。比如：DELETE http://api.qc.com/v1/friends: 删除某人的好友 （在http parameter指定好友id）POST http://api.qc.com/v1/friends: 添加好友UPDATE http://api.qc.com/v1/profile: 更新个人资料




struts2 与 springmvc 对比
------struts2                   /              springmvc
类级别拦截 一个类对应一个请求  /  方法级别拦截人口filter / 入口是servlet
没有 / 集成了servlet @ResponseBody 返回响应文本没有/和springmvc无缝集成oop编程思想/ 在servlet/性能高/配置少/验证支持JSR303
1，Struts2与Spring整合后, 由spring来管理Struts2的Action, bean默认是单实例有情况下,会有如下问题:
1) Struts2的Action是单例,其中的FieldError,actionerror中的错误信息会累加, 即使再次输入了正确的信息,也过不了验证.
2) Struts2的Action是有状态的,他有自己的成员属性, 所以在多线程下,会有线程安全问题，这是最大的问题。
如何解决?
方案一: 就是不用单例, spring中bean的作用域设为prototype,每个请求对应一个Action实例.(建议这样做)
方案二: spring中bean的作用域设为session ,每个session对应一个实例,解决了多线程问题.

springbean的作用域
singleton
在每个Spring IoC容器中一个bean定义对应一个对象实例。
prototype
一个bean定义对应多个对象实例。
request
在一次HTTP请求中，一个bean定义对应一个实例；即每次HTTP请求将会有各自的bean实例， 它们依据某个bean定义创建而成。该作用域仅在基于web的Spring ApplicationContext 情形下有效。
session
在一个HTTP Session 中，一个bean定义对应一个实例。该作用域仅在基于web的SpringApplicationContext 情形下有效。
global session
在一个全局的HTTP Session 中，一个bean定义对应一个实例。典型情况下，仅在使用portlet context的时候有效。该作用域仅在基于web的Spring ApplicationContext 情形下有效。

总结 :
方案一：bean的作用域设为prototype, 不用担心性能不好, 实际测试过,多实例Action性能没问题.
方案二: 有人担心方案一性能不好， 所有才有了方案二, 不知比方案一性能 能高多少？应该不会高多少。

2，Spring MVC Controller默认是单例的：
单例的原因有二：
1、为了性能。
2、不需要多例。
万一必须要定义一个非静态成员变量时候，则通过注解@Scope("prototype")，将其设置为多例模式



zookeeper功能及工作原理
------

1.ZooKeeper是什么？
ZooKeeper是一个分布式的，开放源码的分布式应用程序协调服务，是Google的Chubby一个开源的实现，它是集群的管理者，监视着集群中各个节点的状态根据节点提交的反馈进行下一步合理操作。最终，将简单易用的接口和性能高效、功能稳定的系统提供给用户
------
zookeeper 设计目的
1.最终一致性：client不论连接到哪个Server，展示给它都是同一个视图，这是zookeeper最重要的性能。 

2.可靠性：具有简单、健壮、良好的性能，如果消息被到一台服务器接受，那么它将被所有的服务器接受。 

3.实时性：Zookeeper保证客户端将在一个时间间隔范围内获得服务器的更新信息，或者服务器失效的信息。但由于网络延时等原因，Zookeeper不能保证两个客户端能同时得到刚更新的数据，如果需要最新数据，应该在读数据之前调用sync()接口。 

4.等待无关（wait-free）：慢的或者失效的client不得干预快速的client的请求，使得每个client都能有效的等待。 

5.原子性：更新只能成功或者失败，没有中间状态。 

6.顺序性：包括全局有序和偏序两种：全局有序是指如果在一台服务器上消息a在消息b前发布，则在所有Server上消息a都将在消息b前被发布；偏序是指如果一个消息b在消息a后被同一个发送者发布，a必将排在b前面。 
------工作原理
Zookeeper 的核心是原子广播，这个机制保证了各个Server之间的同步。实现这个机制的协议叫做Zab协议。Zab协议有两种模式，它们分别是恢复模式（选主）和广播模式（同步）。当服务启动或者在领导者崩溃后，Zab就进入了恢复模式，当领导者被选举出来，且大多数Server完成了和 leader的状态同步以后，恢复模式就结束了。状态同步保证了leader和Server具有相同的系统状态。 

为了保证事务的顺序一致性，zookeeper采用了递增的事务id号（zxid）来标识事务。所有的提议（proposal）都在被提出的时候加上了zxid。实现中zxid是一个64位的数字，它高32位是epoch用来标识leader关系是否改变，每次一个leader被选出来，它都会有一个新的epoch，标识当前属于那个leader的统治时期。低32位用于递增计数。

来源：http://www.cnblogs.com/felixzh/p/5869212.html



BIO NIO
BIO：Apache，Tomcat。主要是并发量要求不高的场景
NIO：Nginx，Netty。主要是高并发量要求的场景
AIO：还不是特别成熟，底层也基本是多线程模拟，所以应用场景不多，Netty曾经用了，但又放弃了。
BIO，同步阻塞式IO，简单理解：一个连接一个线程
NIO，同步非阻塞IO，简单理解：一个请求一个线程
AIO，异步非阻塞IO，简单理解：一个有效请求一个线程

同步阻塞IO（JAVA BIO）： 
    同步并阻塞，服务器实现模式为一个连接一个线程，即客户端有连接请求时服务器端就需要启动一个线程进行处理，如果这个连接不做任何事情会造成不必要的线程开销，当然可以通过线程池机制改善。 

同步非阻塞IO(Java NIO) ： 同步非阻塞，服务器实现模式为一个请求一个线程，即客户端发送的连接请求都会注册到多路复用器上，多路复用器轮询到连接有I/O请求时才启动一个线程进行处理。用户进程也需要时不时的询问IO操作是否就绪，这就要求用户进程不停的去询问。 

异步阻塞IO（Java NIO）：  
   此种方式下是指应用发起一个IO操作以后，不等待内核IO操作的完成，等内核完成IO操作以后会通知应用程序，这其实就是同步和异步最关键的区别，同步必须等待或者主动的去询问IO是否完成，那么为什么说是阻塞的呢？因为此时是通过select系统调用来完成的，而select函数本身的实现方式是阻塞的，而采用select函数有个好处就是它可以同时监听多个文件句柄（如果从UNP的角度看，select属于同步操作。因为select之后，进程还需要读写数据），从而提高系统的并发性！  


（Java AIO(NIO.2)）异步非阻塞IO:  
   在此种模式下，用户进程只需要发起一个IO操作然后立即返回，等IO操作真正的完成以后，应用程序会得到IO操作完成的通知，此时用户进程只需要对数据进行处理就好了，不需要进行实际的IO读写操作，因为真正的IO读取或者写入操作已经由内核完成了。    
-----例子
同步阻塞：你到饭馆点餐，然后在那等着，还要一边喊：好了没啊！ 

同步非阻塞：在饭馆点完餐，就去遛狗了。不过溜一会儿，就回饭馆喊一声：好了没啊！ 

异步阻塞：遛狗的时候，接到饭馆电话，说饭做好了，让您亲自去拿。 

异步非阻塞：饭馆打电话说，我们知道您的位置，一会给你送过来，安心遛狗就可以了。


Java中short、int、long、float、double的取值范围

一、基本数据类型的特点，位数，最大值和最小值。
1、
基本类型：short 二进制位数：16 
包装类：java.lang.Short 
最小值：Short.MIN_VALUE=-32768 （-2的15此方）
最大值：Short.MAX_VALUE=32767 （2的15次方-1）
2、
基本类型：int 二进制位数：32
包装类：java.lang.Integer
最小值：Integer.MIN_VALUE= -2147483648 （-2的31次方）
最大值：Integer.MAX_VALUE= 2147483647  （2的31次方-1）
3、
基本类型：long 二进制位数：64
包装类：java.lang.Long
最小值：Long.MIN_VALUE=-9223372036854775808 （-2的63次方）
最大值：Long.MAX_VALUE=9223372036854775807 （2的63次方-1）
4、
基本类型：float 二进制位数：32
包装类：java.lang.Float
最小值：Float.MIN_VALUE=1.4E-45 （2的-149次方）
最大值：Float.MAX_VALUE=3.4028235E38 （2的128次方-1）
5、
基本类型：double 二进制位数：64
包装类：java.lang.Double
最小值：Double.MIN_VALUE=4.9E-324 （2的-1074次方）
最大值：Double.MAX_VALUE=1.7976931348623157E308 （2的1024次方-1）
基本类型 字节数 位数 最大值 最小值 
byte 1byte 8bit 2^7 - 1 -2^7 
short 2byte 16bit 2^15 - 1 -2^15 
int 4byte 32bit 2^31 - 1 -2^31 
long 8byte 64bit 2^63 - 1 -2^63 
float 4byte 32bit 3.4028235E38 1.4E - 45 
double 8byte 64bit 1.7976931348623157E308 4.9E - 324 
char 2byte 16bit 2^16 - 1 0 



    