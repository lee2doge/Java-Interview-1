## Spring IOC
java程序中的每个业务逻辑至少需要两个或以上的对象来协作完成。通常，每个对象在使用他的合作对象时，自己均要使用像new object（） 这样的语法来完成合作对象的申请工作。这样对象间的耦合度高了。

IOC的思想是：IoC的核心思想在于资源统一管理,你所持有的资源全部放入到IoC容器中,而你也只需要依赖IoC容器,该容器会自动为你装配所需要的具体依赖. 对于spring框架来说，就是由spring来负责控制对象的生命周期和对象间的关系。

### IOC原理

1. IOC容器的初始化过程 ：资源定位（即定义bean的xml）-------》载入--------》IOC容器注册，注册beanDefinition

IOC容器的初始化过程，一般不包含bean的依赖注入的实现，在spring IOC设计中，bean的注册和依赖注入是两个过程，，依赖注入一般发生在应用第一次索取bean的时候，但是也可以在xm中配置，在容器初始化的时候，这个bean就完成了初始化。

2. 依赖注入，在程序运行期间,由外部容器动态地将依赖对象注入到组件中，三种注入方式，构造器、接口、set注入，我们常用的是set注入

3. bean是如何创建---  工厂模式

4. 数据是如何注入--- 反射

##  Spring AOP
1. AOP利用一种称为“横切”的技术，剖解开封装的对象内部，并将那些影响了 多个类的公共行为封装到一个可重用模块，并将其名为“Aspect”，即方面。所谓“方面”，简单地说，就是将那些与业务无关，却为业务模块所共同调用的 逻辑或责任封装起来，比如日志记录，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可操作性和可维护性。

### 实现AOP的技术
主要分为两大类：一是采用动态代理技术，利用截取消息的方式，对该消息进行装饰，以取代原有对象行为的执行； 二是采用静态织入的方式，引入特定的语法创建“方面”，从而使得编译器可以在编译期间织入有关“方面”的代码。

### Spring实现AOP
1. JDK动态代理：其代理对象必须是某个接口的实现，它是通过在运行期间创建一个接口的实现类来完成对目标对象的代理；其核心的两个类是InvocationHandler和Proxy。
2. CGLIB代理：实现原理类似于JDK动态代理，只是它在运行期间生成的代理对象是针对目标类扩展的子类。CGLIB是高效的代码生成包，底层是依靠ASM（开源的java字节码编辑类库）操作字节码实现的，性能比JDK强；需要引入包asm.jar和cglib.jar。


###  AOP使用场景
1. Authentication 权限检查        
2. Caching 缓存        
3. Context passing 内容传递        
4. Error handling 错误处理        
5. Lazy loading　延迟加载        
6. Debugging　　调试      
7. logging, tracing, profiling and monitoring　日志记录，跟踪，优化，校准        
8. Performance optimization　性能优化，效率检查        
9. Persistence　　持久化        
10. Resource pooling　资源池        
11. Synchronization　同步        
12. Transactions 事务管理  


## Spring @Transactional工作原理
1. 当spring遍历容器中所有的切面，查找与当前实例化bean匹配的切面，这里就是获取事务属性切面，查找@Transactional注解及其属性值，然后根据得到的切面进入createProxy方法，创建一个AOP代理。
2. 默认是使用JDK动态代理创建代理，如果目标类是接口，则使用JDK动态代理，否则使用Cglib。
3. 获取的是当前目标方法对应的拦截器，里面是根据之前获取到的切面来获取相对应拦截器，这时候会得到TransactionInterceptor实例。如果获取不到拦截器，则不会创建MethodInvocation，直接调用目标方法。
4. 在需要进行事务操作的时候，Spring会在调用目标类的目标方法之前进行开启事务、调用异常回滚事务、调用完成会提交事务。是否需要开启新事务，是根据@Transactional注解上配置的参数值来判断的。如果需要开启新事务，获取Connection连接，然后将连接的自动提交事务改为false，改为手动提交
5. Spring并不会对所有类型异常都进行事务回滚操作，默认是只对Unchecked Exception(Error和RuntimeException)进行事务回滚操作。

从上面的分析可以看到，Spring使用AOP实现事务的统一管理,基本都是下面这两种情况：
1. A类的a1方法没有标注@Transactional，a2方法标注@Transactional，在a1里面调用a2。a1方法是目标类A的原生方法，调用a1的时候即直接进入目标类A进行调用，在目标类A里面只有a2的原生方法，在a1里调用a2，即直接执行a2的原生方法，并不通过创建代理对象进行调用，所以并不会进入TransactionInterceptor的invoke方法，不会开启事务。
2. 将@Transactional注解标注在非public方法上。内部使用AOP，所以必须是public修饰的方法才可以被代理



## SpringMVC的工作原理
![](https://github.com/zaiyunduan123/Java-Interview/blob/master/image/frame-1.jpg)
SpringMVC流程
1.  用户发送请求至前端控制器DispatcherServlet。
2.  DispatcherServlet收到请求调用HandlerMapping处理器映射器。
3.   处理器映射器找到具体的处理器(可以根据xml配置、注解进行查找)，生成处理器对象及处理器拦截器(如果有则生成)一并返回给DispatcherServlet。
4.  DispatcherServlet调用HandlerAdapter处理器适配器。
5.  HandlerAdapter经过适配调用具体的处理器(Controller，也叫后端控制器)。
6.   Controller执行完成返回ModelAndView。
7.   HandlerAdapter将controller执行结果ModelAndView返回给DispatcherServlet。
8.   DispatcherServlet将ModelAndView传给ViewReslover视图解析器。
9.   ViewReslover解析后返回具体View。
10. DispatcherServlet根据View进行渲染视图（即将模型数据填充至视图中）。
11.  DispatcherServlet响应用户。

请求 ---> DispatcherServlet（前端控制器）---> 调用HandlerMapping（处理器映射器）---> DispatcherServlet调用 HandlerAdapter（处理器适配器）---> 适配调用具体的Controller ---> 返回ModelAndView  --->  传给ViewReslover视图解析器  --->  解析后返回具体View ---> 根据View进行渲染视图响应用户


## SpringMVC拦截器
### 常见应用场景
1. 日志记录：记录请求信息的日志，以便进行信息监控、信息统计、计算PV（Page View）等
2. 权限检查：如登录检测，进入处理器检测检测是否登录，如果没有直接返回到登录页面
3. 性能监控：有时候系统在某段时间莫名其妙的慢，可以通过拦截器在进入处理器之前记录开始时间，在处理完后记录结束时间，从而得到该请求的处理时间（如果有反向代理，如apache可以自动记录）
4. 通用行为：读取cookie得到用户信息并将用户对象放入请求，从而方便后续流程使用，还有如提取Locale、Theme信息等，只要是多个处理器都需要的即可使用拦截器实现。
5. OpenSessionInView：如Hibernate，在进入处理器打开Session，在完成后关闭Session。

### 拦截器接口
```java
public interface HandlerInterceptor {  
    /**
    * 预处理回调方法，实现处理器的预处理（如登录检查），第三个参数为响应的处理器（如我们上一章的Controller实现）
    * 返回值：true表示继续流程（如调用下一个拦截器或处理器）；
    * false表示流程中断（如登录检查失败），不会继续调用其他的拦截器或处理器，此时我们需要通过response来产生响应
    */
    boolean preHandle(  
            HttpServletRequest request, HttpServletResponse response,   
            Object handler)   
            throws Exception;  
    /**
    * 后处理回调方法，实现处理器的后处理（但在渲染视图之前），此时我们可以通过modelAndView（模型和视图对象）对模型数据进行处理或对视图进行处理，modelAndView也可能为null。
    */
    void postHandle(  
            HttpServletRequest request, HttpServletResponse response,   
            Object handler, ModelAndView modelAndView)   
            throws Exception;  
    /**
    * 整个请求处理完毕回调方法，即在视图渲染完毕时回调，如性能监控中我们可以在此记录结束时间并输出消耗时间 ，
    * 还可以进行一些资源清理，类似于try-catch-finally中的finally，但仅调用处理器执行链中preHandle返回true的拦截器的afterCompletion。
    */
    void afterCompletion(  
            HttpServletRequest request, HttpServletResponse response,   
            Object handler, Exception ex)  
            throws Exception;  
}   
```


## MyBatis原理
**MyBatis完成2件事情**

1. 封装JDBC操作
2. 利用反射打通Java类与SQL语句之间的相互转换

**MyBatis的主要成员**

1. Configuration        MyBatis所有的配置信息都保存在Configuration对象之中，配置文件中的大部分配置都会存储到该类中
2. SqlSession           作为MyBatis工作的主要顶层API，表示和数据库交互时的会话，完成必要数据库增删改查功能
3. Executor             MyBatis执行器，是MyBatis 调度的核心，负责SQL语句的生成和查询缓存的维护
4. StatementHandler     封装了JDBC Statement操作，负责对JDBC statement 的操作，如设置参数等
5. ParameterHandler     负责对用户传递的参数转换成JDBC Statement 所对应的数据类型
6. ResultSetHandler     负责将JDBC返回的ResultSet结果集对象转换成List类型的集合
7. TypeHandler          负责java数据类型和jdbc数据类型(也可以说是数据表列类型)之间的映射和转换
8. MappedStatement      MappedStatement维护一条select|update|delete|insert节点的封装
9. SqlSource            负责根据用户传递的parameterObject，动态地生成SQL语句，将信息封装到BoundSql对象中，并返回
10. BoundSql            表示动态生成的SQL语句以及相应的参数信息


![](https://github.com/zaiyunduan123/Java-Interview/blob/master/image/frame-2.jpg)


## MyBatis缓存

MyBatis提供查询缓存，用于减轻数据库压力，提高性能。MyBatis提供了一级缓存和二级缓存。
![](https://github.com/zaiyunduan123/Java-Interview/blob/master/image/frame-3.jpg)

1. 一级缓存是SqlSession级别的缓存，每个SqlSession对象都有一个哈希表用于缓存数据，不同SqlSession对象之间缓存不共享。同一个SqlSession对象对象执行2遍相同的SQL查询，在第一次查询执行完毕后将结果缓存起来，这样第二遍查询就不用向数据库查询了，直接返回缓存结果即可。一级缓存是MyBatis内部实现的一个特性，用户不能配置，默认情况下自动支持的缓存，用户没有定制它的权利

2.  二级缓存是Application应用级别的缓存，它的是生命周期很长，跟Application的声明周期一样，也就是说它的作用范围是整个Application应用。MyBatis默认是不开启二级缓存的，可以在配置文件中使用如下配置来开启二级缓存
```xml
<settings>
    <setting name="cacheEnabled" value="true"/>
</settings>
```