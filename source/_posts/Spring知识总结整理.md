---
title: Spring知识总结整理
date: 2019-03-29 19:40:51
tags:  [面试,Spring]
categories: [面试,Spring]

---

# Spring面试问题总结整理
参考：
[ssm常见面试题](https://blog.csdn.net/qq_38262968/article/details/79474455)、
[ssm面试题总结](https://blog.csdn.net/weixin_39801925/article/details/80585758)、
[java ssm框架基础面试题](https://www.jianshu.com/p/231a582d2a02)、
[JavaSSM框架精选50道面试题](https://blog.csdn.net/qq_41735004/article/details/87199651)、
[69道Spring面试题和答案](http://ifeve.com/spring-interview-questions-and-answers/)

---

### spring mvc
![springmvc](/image/interview_prep/springmvc.png)

#### 简单介绍对springMVC的理解
Spring MVC是基于java的实现MVC设计模式的请求驱动类型的轻量级web框架，通过把model、view、controller分离，将web层进行职责解耦，把复杂的web应用分成逻辑清晰的几部分，简化开发，减少出错。
- model负责对应用数据封装，并对外提供应用功能的接口；
- view负责通过向控制器发送请求，得到响应，并向用户展示处理好的数据；
- controller负责定义应用的功能，接收用户的动作，并选择响应的view
特点：
- 它是基于组件技术的。全部的应用对象，无论控制器和视图还是业务对象之类的，都是基于java组件，并且和spring基础组件集成。
- 不依赖于servletAPI（虽然具体具体实现还是依赖servlet）
- 可以使用任何视图技术，不仅仅jsp
- 支持各种请求资源的映射策略
- 它是易于扩展的

#### spring mvc的流程
1. 用户发送请求到DispatcherServlet；
2. servlet接收到请求后，调用HandlerMapping，请求获取Handler；
3. HandlerMapping根据url得到具体的Handler，生成Handler对象和Handler拦截器，一起返回给DispatcherServlet；
4. DispatcherServlet调用HandlerAdapter；
5. HandlerAdapter执行Handler
6. Handler执行完返回ModerAndView；
7. HandlerAdapter将ModerAndView返回给DispatcherServlet；
8. DispatcherServlet将ModerAndView传给ViewResolver解析；
9. ViewResolver解析后将view返回；
10. DispatcherServlet渲染view（将模型数据填充）
11. 响应用户。

#### spring mvc优点
- 可以支持各种视图技术，不仅jsp
- 与spring框架集成（ioc、aop）
- 清晰的角色分配：DispatcherServlet、HandlerMapping、HandlerAdapter、ViewResolver.
- 支持各种请求资源的映射策略。

#### spring mvc主要组件
- **DispatcherServlet**（不需要程序员开发）：接收请求，响应结果，相当于转发器，有了它可以减少其他组件之间的耦合度。
- **HandlerMapping**（不需要程序员开发）：根据url查找Handler。
- **HandlerAdapter**：根据 Handler 来找到支持它的 HandlerAdapter，通过 HandlerAdapter 执行这个 Handler 得到 ModelAndView 对象。
- **Handler**（需要程序员开发）
- **ViewResolver**：进行视图解析，根据视图逻辑名解释成真正的视图。
- **View**（需要开发）：View是一个接口， 它的实现类支持不同的视图类型（jsp，freemarker，pdf等等）

#### spring mvc和struts2区别
- spring mvc的入口是一个servlet——DispatcherServlet，struts2的入口是一个filter过滤器——StrutsPrepareAndExecuteFilter。
- spring mvc是基于方法开发（一个url对应一个方法），请求参数传递到方法的形参，可以是单例也可以是多例；Struts2是基于类开发，传递参数是通过类属性，只能设计为多例。
- Struts采用值栈存储请求和响应的数据，通过OGNL存取数据；springmvc通过参数解析器将request请求内容解析，并给方法的形参赋值，将数据和视图封装成ModelAndView对象，最后将ModelAndView的模型数据通过request域传输到页面。jsp视图解析器默认jstl。


#### springmvc如何设定重定向和转发
- 转发：返回值前加<font color='red'>forward:</font>，如：forward:user.do?name=method4
- 重定向：返回值前加<font color='red'>redirect:</font>，如：redirect:http://www.baidu.com


#### springmvc如何AJAX交互
通过jackson框架，可以把java对象转为json。
1. 加入jackson.jar
2. 配置文件中配置json映射
3. 方法前加@ResponseBody

#### 解决POST、GET的中文乱码
POST：web.xml中配置CharacterEncodingFilter过滤器，设置成utf-8

GET: 
- 修改tomcat配置文件添加编码与工程编码一致
- 对参数进行重新编码，String userName = new String(request.getParamter("userName").getBytes("ISO8859-1"),"utf-8")


#### SpringMvc的控制器是不是单例模式,如果是,有什么问题,怎么解决
是单例模式,所以在多线程访问的时候有线程安全问题,不要用同步,会影响性能的,解决方案是在控制器里面不能写字段。

#### springmvc常用注解

- @RequestMapping：用于处理url映射的注解，用于类或方法上。
- @RequestBody：用于实现接收http请求的json数据。
- @ResponseBody：用于实现将java对象转化为json对象，返回响应。

#### SpringMvc中函数的返回值是什么
返回值可以有很多类型,有String, ModelAndView。ModelAndView类把视图和数据都合并的一起的

#### SpringMvc用什么对象从后台向前台传递数据的
通过ModelMap对象，调用put方法，把对象加入，前台用el表达式

#### ModelMap的数据放入session里面
类上加@SessionAttributes注解，里面包含的字符串就是要放入session里的key。

#### springmvc拦截器
两种写法，一种是实现HandlerInterceptor接口，一种是继承适配器类，在接口方法中实现。然后再springmvc的配置文件中配置。

```xml
<mvc:interceptors>
    <!--默认拦截所有-->
    <bean id='myInterceptor' class='cn.keinz.myHandlerInterceptor'>
    <!--只针对某个url-->
    <mvc: interceptor>
        <mvc: mapping path='/user'/>
        <bean class='cn.keinz.myHandlerInterceptorAdapter'>
    </mvc: interceptor>
</mvc:interceptors>
```

#### 注解原理
[JAVA 注解的基本原理](https://www.cnblogs.com/yangming1996/p/9295168.html)

注解本质是继承了Annotation接口的接口，具体的实现类是通过运行时生成的动态 代理类。我们通过反射获取注解时，返回的是java运行时生成的动态代理对象。通过动态代理对象调用自定义注解的方法，会最终调用AnnotationInvocationHandler的invoke方法。该方法会通过memberValues这个Map中索引出对应的值。而memberValues的来源是Java常量池。

#### Spring为什么要结合使用HandlerMapping以及HandlerAdapter来处理Handler

符合面向对象的单一职责原则，代码结构清晰，便于维护，最重要的是可复用性高，如HandlerAdapter可能被用于处理多种Handler。

---

### mybatis
#### mybatis的理解
- 连接数据库通过核心配置文件：SqlMapConfig.xml，配置数据库连接池的获得和连接数据库
- 操作数据库通过映射配置文件，这个配置文件设计为dao的实现映射，可以视作dao的实现
- 通过SqlMapConfig.xml构造SqlSessionFactory，由SqlSessionFactory.openSession()获得SqlSession，操作数据库由SqlSession完成。
- mybatis底层定义了Executor接口操作数据库，Executor接口有两个实现：一个基本执行器，一个缓存执行器。
- Mapped Statement也是mybatis的一个底层封装对象，它包装了mybatis的配置信息和sql映射信息等，mapper.xml的一个sql对应一个Mapped Statement对象，sql的id就是Mapped Statement的id。
- Mapped Statement对sql执行的输入参数进行定义，包括HashMap、基本类型和pojo，Executor通过Mapped Statement在执行前将java对象映射到sql中，输入参数映射就是jdbc编程中对prepared statement设置参数。
- Mapped Statement对sql执行的输出参数进行定义，包括HashMap、基本类型和pojo，Executor通过Mapped Statement在执行后将输出映射到java对象，输出结果映射相当于jdbc编程中对结果的解析。

mybatis是一个暴露出sql语句的jdbc封装，是一个暴露sql语句的持久层框架，暴露的方式是映射配置文件。

#### 使用MyBatis(IBatis)的好处是什么
- mybatis把sql语句从java源程序中独立出来，放在xml里，系统维护更便利。
- mybatis封装了底层jdbc api调用细节，并能自动将结果转为java bean对象。
- mybatis需要自己编写sql，可以灵活控制sql语句，比hibernate等全自动orm框架有更高的效率，能完成复杂的查询。


#### 对MyBatis的缓存的理解
mybatis缓存分为一级缓存和二级缓存，一级缓存在sqlsession中，默认就有，二级缓存基于mapper文件的namespace，开始需要在mybatis-configuration.xml配置文件中添加：

```
<settings>
    <setting name="cacheEnabled" value="true"/>
</settings>
```
然后在mapper.xml中：

```
<cache type="org.apache.ibatis.cache.impl.PerpetualCache"/>
```
最后，po类实现serializable接口。

Mybatis首先去缓存中查询结果集，如果没有则查询数据库，如果有则从缓存取出返回结果集就不走数据库。Mybatis内部存储缓存使用一个HashMap，key为hashCode+sqlId+Sql语句。value为从查询出来映射生成的java对象
Mybatis的二级缓存即查询缓存，它的作用域是一个mapper的namespace，即在同一个namespace中查询sql可以从缓存中获取数据。二级缓存是可以跨SqlSession的。


#### ${} 和 #{}的区别
- ${} ：预编译处理，直接把${} 替换成变量的值；$一般用入传入数据库对象，比如数据库表名
- #{} ：sql解析时会加上""，当成字符串解析，有效的防止Sql语句注入
- order by 动态参数时需要注意，使用${}而不用#{}

#### mybatis一对一的实现方式
- 联表查询：几个表联合查询，只查询一次，通过resultMap节点配置association节点。
- 嵌套查询：先查一个表，得到外键id，再到另外的表查询，也是配置association节点，但另外的表查询通过select属性配置。
```
<association property="topicType" select="selectType" column="topics_type_id" javaType="com.tmf.bbs.pojo.Type"/>
```
- property：属性名
- select：要连接的查询
- column：共同列
- javaType：集合中元素类型

#### mybatis一对多的实现方式
- 联表查询：几个表联合查询，只查询一次，通过resultMap节点配置collection节点。
- 嵌套查询：先查一个表，得到外键id，再到另外的表查询，也是配置collectionn节点，但另外的表查询通过select属性配置。

```
<collection property="topicComment" column="id" ofType="com.tmf.bbs.pojo.Comment" select="selectComment" />
```
- property：属性名
- select：要连接的查询
- column：共同列
- ofType：集合中元素类型

#### mybatis的接口绑定，好处
mybatis实现了dao接口和xml映射文件的绑定，自动生成了接口的实现，更方便。

#### 接口绑定的实现方式，什么情况下使用
注解绑定和xml绑定。
sql简单时使用注解绑定。复杂时使用xml绑定。

#### JDBC与数据库交互的流程是什么
1. 加载数据库驱动
2. 获取数据库连接
3. 生成statement对象
4. 设置sql语句
5. 设置sql参数
6. 使用statement执行sql
7. 获取结果集，解析结果集
8. 关闭连接，释放资源


#### 谈谈对mybatis中的sqlSession、sqlSessionFactoryBuild和sqlSessionFactory的理解。
- sqlSession：封装了对数据库操作的方法，是通过sqlSessionFactory创建的
- sqlSessionFactory：通过sqlSessionFactoryBuild创建的
- sqlSessionFactoryBuild：是创建sqlSessionFactory时使用的，因为sqlSession是由sqlSessionFactory创建，所以可以当做工具类使用
- sqlSessionFactory是一个接口，重载了opensession的方法。使用范围是在整个运行范围内，一旦创建可以重复使用，所以可以当做单实例来管理。
- sqlSession是面向用户的一个操作数据库的接口，每个线程都有自己的sqlSession，sqlSession不能线程共享，sqlSession不使用就要关闭。

#### 简述Mybatis的插件运行原理
Mybatis仅可以编写针对ParameterHandler、ResultSetHandler、StatementHandler、Executor这4种接口的插件，Mybatis使用JDK的动态代理，为需要拦截的接口生成代理对象以实现接口方法拦截功能，每当执行这4种接口对象的方法时，就会进入拦截方法，具体就是InvocationHandler的invoke()方法，当然，只会拦截那些你指定需要拦截的方法。实现Mybatis的Interceptor接口并复写intercept()方法，然后在给插件编写注解，指定要拦截哪一个接口的哪些方法即可，记住，还需要在配置文件中配置你编写的插件。


---

### spring
#### 如何理解Spring
具体来说Spring是一个轻量级的容器，用于管理业务相关对象的。核心功能主要为：IOC,AOP,MVC。

- IOC：控制反转，将对象的创建过程交给容器，让容器管理对象的生命周期如创建，初始化，销毁等。
- AOP：面向切面编程，对关注点进行模块化，通过对某一功能点进行编程，比如记录日志，有很多个类都需要记录日志的方法，则创建记录日志的代理方法，需要调用该功能是只需要调用代理方法，这就是AOP。
- MVC：SpringMvc,Spring提供的基于MVC模式设计的Web框架，如今比较流行的框架之一。

#### 使用Spring框架能带来哪些好处
- 轻量：Spring 是轻量的，基本的版本大约2MB。
- 控制反转：Spring通过控制反转实现了松散耦合，对象们给出它们的依赖，而不是创建或查找依赖的对象们。
- 面向切面的编程(AOP)：Spring支持面向切面的编程，并且把应用业务逻辑和系统服务分开。
- 容器：Spring 包含并管理应用中对象的生命周期和配置。
- MVC框架：Spring的WEB框架是个精心设计的框架，是Web框架的一个很好的替代品。
- 事务管理：Spring 提供一个持续的事务管理接口，可以扩展到上至本地事务下至全局事务（JTA）。
- 异常处理：Spring 提供方便的API把具体技术相关的异常（比如由JDBC，Hibernate or JDO抛出的）转化为一致的unchecked 异常。

#### 什么是控制反转(IOC)？什么是依赖注入
- 控制反转是应用于软件工程领域中的，在运行时被装配器对象来绑定耦合对象的一种编程技巧，对象之间耦合关系在编译时通常是未知的。在传统的编程方式中，业务逻辑的流程是由应用程序中的早已被设定好关联关系的对象来决定的。在使用控制反转的情况下，业务逻辑的流程是由对象关系图来决定的，该对象关系图由装配器负责实例化，这种实现方式还可以将对象之间的关联关系的定义抽象化。而绑定的过程是通过“依赖注入”实现的。
- 控制反转是一种以给予应用程序中目标组件更多控制为目的设计范式，并在我们的实际工作中起到了有效的作用。
- 依赖注入是在编译阶段尚未知所需的功能是来自哪个的类的情况下，将其他对象所依赖的功能对象实例化的模式。这就需要一种机制用来激活相应的组件以提供特定的功能，所以依赖注入是控制反转的基础。否则如果在组件不受框架控制的情况下，框架又怎么知道要创建哪个组件？

#### 注入有以下三种实现方式
- 构造器注入
- setter方法注入
- 接口注入

#### 解释下Spring框架中的IoC
IOC:把对象的创建、初始化、销毁交给spring来管理，而不是由开发者控制，实现控制反转。
- Spring中的 org.springframework.beans 包和 org.springframework.context包构成了Spring框架IoC容器的基础。
- BeanFactory 接口提供了一个先进的配置机制，使得任何类型的对象的配置成为可能。ApplicationContex接口对BeanFactory（是一个子接口）进行了扩展，在BeanFactory的基础上添加了其他功能，比如与Spring的AOP更容易集成，也提供了处理message resource的机制（用于国际化）、事件传播以及应用层的特别配置，比如针对Web应用的WebApplicationContext。
- org.springframework.beans.factory.BeanFactory 是Spring IoC容器的具体实现，用来包装和管理前面提到的各种bean。BeanFactory接口是Spring IoC 容器的核心接口。

#### BeanFactory和ApplicationContext有什么区别
##### 描述：
BeanFactory：是Spring里面最低层的接口，提供了最简单的容器的功能，只提供了实例化对象和拿对象的功能；
ApplicationContext：应用上下文，继承BeanFactory接口，它是Spring的一各更高级的容器，提供了更多的有用的功能：
1. 国际化（MessageSource）
2. 访问资源，如URL和文件（ResourceLoader）
3. 载入多个（有继承关系）上下文 ，使得每一个上下文都专注于一个特定的层次，比如应用的web层  
4. 消息发送、响应机制（ApplicationEventPublisher）
5. AOP（拦截器）

##### 两者装载bean的区别：
BeanFactory：BeanFactory在启动的时候不会去实例化Bean，只有从容器中拿Bean的时候才会去实例化；
ApplicationContext：ApplicationContext在启动的时候就把所有的Bean全部实例化了。它还可以为Bean配置lazy-init=true来让Bean延迟实例化； 
##### 用BeanFactory还是ApplicationContent
延迟实例化的优点：（BeanFactory）
应用启动的时候占用资源很少；对资源要求较高的应用，比较有优势； 
不延迟实例化的优点： （ApplicationContext）
1. 所有的Bean在启动的时候都加载，系统运行的速度快； 
2. 在启动的时候所有的Bean都加载了，我们就能在系统启动的时候，尽早的发现系统中的配置问题 
3. 建议web应用，在启动的时候就把所有的Bean都加载了。（把费时的操作放到系统启动中完成）

#### Spring有几种配置方式
- 基于xml配置
- 基于注解配置
- 基于java配置

#### 如何用基于XML配置的方式配置Spring

在Spring框架中，依赖和服务需要在专门的配置文件来实现，常用的XML格式的配置文件。这些配置文件的格式通常用&lt;beans&gt;开头，然后一系列的bean定义和专门的应用配置选项组成。

SpringXML配置的主要目的时候是使所有的Spring组件都可以用xml文件的形式来进行配置。这意味着不会出现其他的Spring配置类型（比如声明的方式或基于Java Class的配置方式）

Spring的XML配置方式是使用被Spring命名空间的所支持的一系列的XML标签来实现的。Spring有以下主要的命名空间：context、beans、jdbc、tx、aop、mvc和aso。

#### 如何用基于Java配置的方式配置Spring
Spring对Java配置的支持是由@Configuration注解和@Bean注解来实现的。由@Bean注解的方法将会实例化、配置和初始化一个新对象，这个对象将由Spring的IoC容器来管理。@Bean声明所起到的作用与<bean/> 元素类似。被@Configuration所注解的类则表示这个类的主要目的是作为bean定义的资源。被@Configuration声明的类可以通过在同一个类的内部调用@bean方法来设置嵌入bean的依赖关系。

#### 怎样用注解的方式配置Spring

Spring在2.5版本以后开始支持用注解的方式来配置依赖注入。可以用注解的方式来替代XML方式的bean描述，可以将bean描述转移到组件类的内部，只需要在相关类上、方法上或者字段声明上使用注解即可。注解注入将会被容器在XML注入之前被处理，所以后者会覆盖掉前者对于同一个属性的处理结果。

注解装配在Spring中是默认关闭的。所以需要在Spring文件中配置一下才能使用基于注解的装配模式。如果你想要在你的应用程序中使用关于注解的方法的话，请参考如下的配置。

```
<beans>    
   <context:annotation-config/>    
   <!-- bean definitions go here -->    
</beans>    
```
在 <context:annotation-config/>标签配置完成以后，就可以用注解的方式在Spring中向属性、方法和构造方法中自动装配变量。
- @Required：该注解应用于设值方法。
- @Autowired：该注解应用于有值设值方法、非设值方法、构造方法和变量。
- @Qualifier：该注解和@Autowired注解搭配使用，用于消除特定bean自动装配的歧义。
- JSR-250 Annotations：Spring支持基于JSR-250 注解的以下注解，@Resource、@PostConstruct 和 @PreDestroy。

#### 什么是Spring Beans
Spring beans 是那些形成Spring应用的主干的java对象。它们被Spring IOC容器初始化，装配，和管理。这些beans通过容器中配置的元数据创建。比如，以XML文件中&lt;bean/&gt; 的形式定义。

Spring 框架定义的beans都是单件beans。在bean tag中有个属性”singleton”，如果它被赋为TRUE，bean 就是单件，否则就是一个 prototype bean。默认是TRUE，所以所有在Spring框架中的beans 缺省都是单件。

#### 请解释Spring Bean的生命周期

- 实例化bean对象(通过构造方法或者工厂方法)
- 设置对象属性(setter等)（依赖注入）
- 如果Bean实现了BeanNameAware接口，工厂调用Bean的setBeanName()方法传递Bean的ID。（和下面的一条均属于检查Aware接口）
- 如果Bean实现了BeanFactoryAware接口，工厂调用setBeanFactory()方法传入工厂自身
- 将Bean实例传递给Bean的前置处理器的postProcessBeforeInitialization(Object bean, String beanname)方法
- InitializingBean的afterPropertiesSet()，如果实现了该接口，则执行其afterPropertiesSet()方法
- 调用Bean的初始化方法init-method
- 将Bean实例传递给Bean的后置处理器的postProcessAfterInitialization(Object bean, String beanname)方法
- DisposableBean的destroy()，在容器关闭时，如果Bean类实现了该接口，则执行它的destroy()方法
- 容器关闭之前，调用Bean的销毁方法destroy-method，可以在Bean定义文件中使用destory-method定义的方法

如果使用ApplicationContext来维护一个Bean的生命周期，则基本上与上边的流程相同，只不过在执行BeanNameAware的setBeanName()后，若有Bean类实现了org.springframework.context.ApplicationContextAware接口，则执行其setApplicationContext()方法，然后再进行BeanPostProcessors的processBeforeInitialization()
实际上，ApplicationContext除了向BeanFactory那样维护容器外，还提供了更加丰富的框架功能，如Bean的消息，事件处理机制等


#### Spring支持的几种bean的作用域
当定义一个<bean> 在Spring里，我们还能给这个bean声明一个作用域。它可以通过bean 定义中的scope属性来定义。如，当Spring要在需要的时候每次生产一个新的bean实例，bean的scope属性被指定为prototype。另一方面，一个bean每次使用的时候必须返回同一个实例，这个bean的scope 属性 必须设为 singleton。
- **singleton** : bean在每个Spring ioc 容器中只有一个实例。
- **prototype**：一个bean的定义可以有多个实例。
- **request**：每次http请求都会创建一个bean，该作用域仅在基于web的Spring ApplicationContext情形下有效。
- **session**：在一个HTTP Session中，一个bean定义对应一个实例。该作用域仅在基于web的Spring ApplicationContext情形下有效。
- **global-session**：在一个全局的HTTP Session中，一个bean定义对应一个实例。该作用域仅在基于web的Spring ApplicationContext情形下有效。

#### 哪些是重要的bean生命周期方法？ 你能重载它们吗？
有两个重要的bean 生命周期方法，第一个是setup ， 它是在容器加载bean的时候被调用。第二个方法是 teardown  它是在容器卸载类的时候被调用。

The bean 标签有两个重要的属性（init-method和destroy-method）。用它们你可以自己定制初始化和注销方法。它们也有相应的注解（@PostConstruct和@PreDestroy）。

#### 什么是Spring的内部bean
当一个bean仅被用作另一个bean的属性时，它能被声明为一个内部bean，为了定义inner bean，在Spring 的 基于XML的 配置元数据中，可以在&lt;property/&gt;或 &lt;constructor-arg/&gt; 元素内使用&lt;bean/&gt;元素，内部bean通常是匿名的，它们的Scope一般是prototype。

#### 在 Spring中如何注入一个java集合

- &lt;list&gt;类型用于注入一列值，允许有相同的值。
- &lt;set&gt;类型用于注入一组值，不允许有相同的值。
- &lt;map&gt;类型用于注入一组键值对，键和值都可以为任意类型。
- &lt;props&gt;类型用于注入一组键值对，键和值都只能为String类型。

#### bean装配、bean的自动装配
装配，或bean 装配是指在Spring 容器中把bean组装到一起，前提是容器需要知道bean的依赖关系，如何通过依赖注入来把它们装配到一起。

Spring 容器能够自动装配相互合作的bean，这意味着容器不需要&lt;constructor-arg&gt;和&lt;property&gt;配置，能通过Bean工厂自动处理bean之间的协作。

#### 解释不同方式的自动装配
- **no**：默认的方式是不进行自动装配，通过显式设置ref 属性来进行装配。
- **byName**：通过参数名 自动装配，Spring容器在配置文件中发现bean的autowire属性被设置成byname，之后容器试图匹配、装配和该bean的属性具有相同名字的bean。
- **byType**:：通过参数类型自动装配，Spring容器在配置文件中发现bean的autowire属性被设置成byType，之后容器试图匹配、装配和该bean的属性具有相同类型的bean。如果有多个bean符合条件，则抛出错误。
- **constructor**：这个方式类似于byType， 但是要提供给构造器参数，如果没有确定的带参数的构造器参数类型，将会抛出异常。
- **autodetect**：首先尝试使用constructor来自动装配，如果无法工作，则使用byType方式。

#### 自动装配有哪些局限性

- 重写： 你仍需用 &lt;constructor-arg&gt;和 &lt;property&gt; 配置来定义依赖，意味着总要重写自动装配。
- 基本数据类型：你不能自动装配简单的属性，如基本数据类型，String字符串，和类。
- 模糊特性：自动装配不如显式装配精确，如果有可能，建议使用显式装配。

#### @Required  @Autowired @Qualifier 注解
- **@Required**：这个注解表明bean的属性必须在配置的时候设置，通过一个bean定义的显式的属性值或通过自动装配，若@Required注解的bean属性未被设置，容器将抛出BeanInitializationException。
- **@Autowired** 注解提供了更细粒度的控制，包括在何处以及如何完成自动装配。它的用法和@Required一样，修饰setter方法、构造器、属性或者具有任意名称和/或多个参数的PN方法。
- **@Qualifier** 注解
当有多个相同类型的bean却只有一个需要自动装配时，将@Qualifier 注解和@Autowire 注解结合使用以消除这种混淆，指定需要装配的确切的bean。

#### 介绍一下Spring的事物管理
事务就是对一系列的数据库操作（比如插入多条数据）进行统一的提交或回滚操作，如果插入成功，那么一起成功，如果中间有一条出现异常，那么回滚之前的所有操作。这样可以防止出现脏数据，防止数据库数据出现问题。

开发中为了避免这种情况一般都会进行事务管理。Spring中也有自己的事务管理机制，一般是使用TransactionMananger进行管理，可以通过Spring的注入来完成此功能。

#### Spring支持的事务管理类型

- **编程式事务管理**：这意味你通过编程的方式管理事务，给你带来极大的灵活性，但是难维护。
- **声明式事务管理**：这意味着你可以将业务代码和事务管理分离，你只需用注解和XML配置来管理事务。

#### Spring框架的事务管理有哪些优点
- 它为不同的事务API  如 JTA，JDBC，Hibernate，JPA 和JDO，提供一个不变的编程模式。
- 它为编程式事务管理提供了一套简单的API而不是一些复杂的事务API如
- 它支持声明式事务管理。
- 它和Spring各种数据访问抽象层很好得集成。

#### 解释AOP
AOP核心就是切面，它将多个类的通用行为封装成可重用的模块，该模块含有一组API提供横切功能。比如，一个日志模块可以被称作日志的AOP切面。根据需求的不同，一个应用程序可以有若干切面。在Spring AOP中，切面通过带有@Aspect注解的类实现。

#### 在Spring AOP 中，关注点和横切关注的区别是什么
- 关注点是应用中一个模块的行为，一个关注点可能会被定义成一个我们想实现的一个功能。
- 横切关注点是一个关注点，此关注点是整个应用都会使用的功能，并影响整个应用，比如日志，安全和数据传输，几乎应用的每个模块都需要的功能。因此这些都属于横切关注点。


#### 连接点、通知、切入点
- 连接点：代表一个应用程序的某个位置，在这个位置我们可以插入一个AOP切面，它实际上是个应用程序执行Spring AOP的位置。
- 通知：是个在方法执行前或执行后要做的动作，实际上是程序执行时要通过SpringAOP框架触发的代码段。
- 切入点：是一个或一组连接点，通知将在这些位置执行。可以通过表达式或匹配的方式指明切入点

#### Spring切面可以应用五种类型的通知

- before：前置通知，在一个方法执行前被调用。
- after: 在方法执行之后调用的通知，无论方法执行是否成功。
- after-returning: 仅当方法成功完成后执行的通知。
- after-throwing: 在方法抛出异常退出时执行的通知。
- around: 在方法执行之前和之后调用的通知。

#### 引入、目标对象、代理
- 引入：允许我们在已存在的类中增加新的方法和属性。
- 目标对象：被一个或者多个切面所通知的对象。它通常是一个代理对象。也指被通知（advised）对象。
- 代理：是通知目标对象后创建的对象。从客户端的角度看，代理对象和目标对象是一样的。

#### 有几种不同类型的自动代理
- BeanNameAutoProxyCreator
- DefaultAdvisorAutoProxyCreator
- Metadata autoproxying

#### 什么是织入。什么是织入应用的不同点
织入：把切面（aspect）连接到其它的应用程序类型或者对象上，并创建一个被通知（advised）的对象。

织入可以在编译时，加载时，或运行时完成。

#### 切面实现
- **基于XML Schema方式**的切面实现：在这种情况下，切面由常规类以及基于XML的配置实现。
- **基于注解**的切面实现：在这种情况下(基于@AspectJ的实现)，涉及到的切面声明的风格与带有java5标注的普通java类一致。


### Spring中用到哪些设计模式
- 工厂模式（BeanFactory中）
- 单例模式：Spring中默认bean为单例
- 适配器模式（HandlerAdater）
- 装饰者模式
- 代理模式：在AOP和remoting中被用的比较多，AOP中用到JDK动态代理
- 观察者模式（listener的实现，例如ApplicationListener）
- 策略模式（定义一系列的算法，把它们一个个的封装起来，并且使它们可以相互替换。在实例化对象时用到）
- 模板模式：用来解决代码重复的问题，jdbcTemplate




