## 什么是Spring

Spring 是一款开源的轻量级 Java 开发框架，旨在提高开发人员的开发效率以及系统的可维护性。

通常我们说的 Spring框架 指的是 Spring Framework，它是很多模块的集合，使用这些模块可以很方便地协助我们进行开发，比如说 Spring 支持 IoC控制反转 和 AOP面向切面编程；可以很方便地集成第三方组件，例如整合MyBatis方便地对数据库进行操作，SpringAMQP整合RabbitMQ可以方便地实现消息队列，整合Redis可以方便的实现缓存，SpringCloud整合Eureka、Feign、Ribbon、Hystrix、Gateway等组件方便的实现微服务等等；对单元测试支持比较好；支持 RESTful Java 应用程序的开发等。



## 谈谈你对IoC控制反转的理解

IoC（Inversion of Control:控制反转） 是一种设计思想，就是将程序中手动创建对象的控制权交给外部环境来管理，由外部环境来处理对象直接的依赖关系，自动注入成员对象。在Spring框架中有专门负责就是IoC容器的模块。在Spring时代通常使用xml文件来配置依赖关系的，在SpringBoot中则通常使用注解来配置。



## 什么是Spring Bean？

简单来说，Bean 代指的就是那些被 IoC 容器所管理的对象。

我们需要告诉 IoC 容器帮助我们管理哪些对象，这个是通过配置元数据来定义的。配置元数据可以是 XML 文件、注解或者 Java 配置类。



## 将一个类声明为 Bean 的注解有哪些?

- `@Component`：通用的注解，可标注任意类为 `Spring` 组件。如果一个 Bean 不知道属于哪个层，可以使用`@Component` 注解标注。
- `@Repository` : 对应持久层即 Dao 层，主要用于数据库相关操作。
- `@Service` : 对应服务层，主要涉及一些复杂的逻辑，需要用到 Dao 层。
- `@Controller` : 对应 Spring MVC 控制层，主要用于接受用户请求并调用 `Service` 层返回数据给前端页面。



## @Component 和 @Bean 的区别是什么？

- `@Component` 注解作用于类，而`@Bean`注解作用于方法。
- `@Component`通常是通过类路径扫描来自动侦测以及自动装配到 Spring 容器中。`@Bean` 注解通常是我们在标有该注解的方法中定义产生这个 bean,`@Bean`告诉了 Spring 这个方法的返回值是某个类的实例，需交由IoC容器管理。
- `@Bean` 注解比 `@Component` 注解的自定义性更强，而且很多地方我们只能通过 `@Bean` 注解来注册 bean。比如当我们引用第三方库中的类需要装配到 `Spring`容器时，则只能通过 `@Bean`来实现。



## @Autowired 和 @Resource 的区别是什么？

`Autowired` 属于 Spring 内置的注解，默认的注入方式为`byType`（根据类型进行匹配），也就是说会优先根据接口类型去匹配并注入 Bean （接口的实现类）。

**这会有什么问题呢？** 当一个接口存在多个实现类的话，`byType`这种方式就无法正确注入对象了，因为这个时候 Spring 会同时找到多个满足条件的选择，默认情况下它自己不知道选择哪一个。这种情况下，注入方式会变为 `byName`（根据名称进行匹配），这个名称通常就是类名（首字母小写）。通过 `@Qualifier` 注解可以显式指定名称而不是依赖变量的名称。

`@Resource`属于 JDK 提供的注解，默认注入方式为 `byName`。如果无法通过名称匹配到对应的 Bean 的话，注入方式会变为`byType`。`@Resource` 有两个比较重要且日常开发常用的属性：`name`（名称）、`type`（类型）。如果仅指定 `name` 属性则注入方式为`byName`，如果仅指定`type`属性则注入方式为`byType`，如果同时指定`name` 和`type`属性（不建议这么做）则注入方式为`byType`+`byName`。

简单总结一下：

- `@Autowired` 是 Spring 提供的注解，`@Resource` 是 JDK 提供的注解。
- `Autowired` 默认的注入方式为`byType`（根据类型进行匹配），`@Resource`默认注入方式为 `byName`（根据名称进行匹配）。
- 当一个接口存在多个实现类的情况下，`@Autowired` 和`@Resource`都需要通过名称才能正确匹配到对应的 Bean。`Autowired` 可以通过 `@Qualifier` 注解来显式指定名称，`@Resource`可以通过 `name` 属性来显式指定名称。
- `@Autowired` 支持在构造函数、方法、字段和参数上使用。`@Resource` 主要用于字段和方法上的注入，不支持在构造函数或参数上使用。



## Bean的作用域有哪些？

Spring 中 Bean 的作用域通常有下面几种：

- **singleton** : IoC 容器中只有唯一的 bean 实例。Spring 中的 bean 默认都是单例的，是对单例设计模式的应用。
- **prototype** : 每次获取都会创建一个新的 bean 实例。也就是说，连续 `getBean()` 两次，得到的是不同的 Bean 实例。
- **request** （仅 Web 应用可用）: 每一次 HTTP 请求都会产生一个新的 bean（请求 bean），该 bean 仅在当前 HTTP request 内有效。
- **session** （仅 Web 应用可用） : 每一次来自新 session 的 HTTP 请求都会产生一个新的 bean（会话 bean），该 bean 仅在当前 HTTP session 内有效。
- **application/global-session** （仅 Web 应用可用）：每个 Web 应用在启动时创建一个 Bean（应用 Bean），该 bean 仅在当前应用启动时间内有效。
- **websocket** （仅 Web 应用可用）：每一次 WebSocket 会话产生一个新的 bean。

在普通的Spring项目中，只有前两种作用域，后四种状态的作用域是SpringMVC中使用的。

除此之外，singleton（单例作用域）和application（全局作用域）看似都是差不多的，那么它们到底有什么区别呢？

singleton是Spring Core的作用域；application是Spring Web中的作用域。singleton作用于IoC容器，而application作用于Servlet容器。




**如何配置 bean 的作用域呢？**

1. xml方式，scope属性
2. 注解方式 @scope



## Bean是线程安全的吗？

Spring 框架中的 Bean 是否线程安全，取决于其作用域和状态。

我们这里以最常用的两种作用域 prototype 和 singleton 为例介绍。几乎所有场景的 Bean 作用域都是使用默认的 singleton ，重点关注 singleton 作用域即可。

prototype 作用域下，每次获取都会创建一个新的 bean 实例，不存在资源竞争问题，所以不存在线程安全问题。

singleton 作用域下，IoC 容器中只有唯一的 bean 实例，可能会存在资源竞争问题（取决于 Bean 是否有状态）。如果这个 bean 是有状态的话，那就存在线程安全问题（有状态 Bean 是指包含可变的成员变量的对象）。

不过，大部分 Bean 实际都是无状态（没有定义可变的成员变量）的（比如 Dao、Service），这种情况下， Bean 是线程安全的。

对于有状态单例 Bean 的线程安全问题，常见的有两种解决办法：

1. 在 Bean 中尽量避免定义可变的成员变量。
2. 在类中定义一个 `ThreadLocal` 成员变量，将需要的可变成员变量保存在 `ThreadLocal` 中（推荐的一种方式）。



## 谈谈你对AOP的理解

AOP(Aspect-Oriented Programming:面向切面编程)能够将那些与业务无关，却为业务模块所共同调用的逻辑或责任（例如事务处理、日志管理、权限控制等）封装起来，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可拓展性和可维护性。AOP可以在不改变源代码的基础上对代码功能进行增强。

Spring AOP 就是基于动态代理的，如果要代理的对象，实现了某个接口，那么 Spring AOP 会使用 **JDK Proxy**，去创建代理对象，而对于没有实现接口的对象，就无法使用 JDK Proxy 去进行代理了，这时候 Spring AOP 会使用 **Cglib** 生成一个被代理对象的子类来作为代理。

AOP 切面编程涉及到的一些专业术语：

|       术语        | 含义                                                         |
| :---------------: | :----------------------------------------------------------- |
|   目标(Target)    | 被通知的对象                                                 |
|    代理(Proxy)    | 向目标对象应用通知之后创建的代理对象                         |
| 连接点(JoinPoint) | 目标对象的所属类中，定义的所有方法均为连接点                 |
| 切入点(Pointcut)  | 被切面拦截 / 增强的连接点（切入点一定是连接点，连接点不一定是切入点） |
|   通知(Advice)    | 增强的逻辑 / 代码，也即拦截到目标对象的连接点之后要做的事情  |
|   切面(Aspect)    | 切入点(Pointcut)+通知(Advice)                                |
|   Weaving(织入)   | 将通知应用到目标对象，进而生成代理对象的过程动作             |



## Spring AOP 和 AspectJ AOP 有什么区别？

**Spring AOP 属于运行时增强，而 AspectJ 是编译时增强。** Spring AOP 基于代理(Proxying)，而 AspectJ 基于字节码操作(Bytecode Manipulation)。

Spring AOP 已经集成了 AspectJ ，AspectJ 应该算的上是 Java 生态系统中最完整的 AOP 框架了。AspectJ 相比于 Spring AOP 功能更加强大，但是 Spring AOP 相对来说更简单，

如果我们的切面比较少，那么两者性能差异不大。但是，当切面太多的话，最好选择 AspectJ ，它比 Spring AOP 快很多。





## AspectJ 定义的通知类型有哪些？

- **Before**（前置通知）：目标对象的方法调用之前触发
- **After** （后置通知）：目标对象的方法调用之后触发
- **AfterReturning**（返回通知）：目标对象的方法调用完成，在返回结果值之后触发
- **AfterThrowing**（异常通知）：目标对象的方法运行中抛出 / 触发异常后触发。AfterReturning 和 AfterThrowing 两者互斥。如果方法调用成功无异常，则会有返回值；如果方法抛出了异常，则不会有返回值。
- **Around** （环绕通知）：编程式控制目标对象的方法调用。环绕通知是所有通知类型中可操作范围最大的一种，因为它可以直接拿到目标对象，以及要执行的方法，所以环绕通知可以任意的在目标对象的方法调用前后搞事，甚至不调用目标对象的方法



## 多个切面的执行顺序如何控制？

1. 通常使用`@Order` 注解直接定义切面顺序

2. 实现`Ordered` 接口重写 `getOrder` 方法。




## Spring 创建对象的时机

### 单例管理的对象 scope="singleton"

1. 默认情况下，Bean的作用域默认为单例类型。spring在读取xml文件的时候,就会创建对象。
2. 在创建的对象的时候(先调用构造器),会去调用init-method=".."属性值中所指定的方法.
3. 对象在被销毁的时候,会调用destroy-method="..."属性值中所指定的方法.(例如调用container.destroy()方法的时候)
4. lazy-init="true",可以让这个对象在第一次被访问的时候创建

### 非单例管理的对象 scope="prototype"

1. spring读取xml文件的时候,不会创建对象.
2. 在每一次访问这个对象的时候,spring容器都会创建这个对象,并且调用init-method=".."属性值中所指定的方法.
3. 对象销毁的时候,spring容器不会帮我们调用任何方法。因为是非单例,这个类型的对象有很多个,spring容器一旦把这个对象交给你之后,就不再管理这个对象了。





## Spring 创建对象(注入)的方式

### 1. 构造方法实例化

bean标签的class属性为实例类的全限定名。

有3种依赖注入方式。

+ Setter注入。使用property标签。

+ 构造器注入。
    + 对于无参数的(默认使用的构造器方法)，不能用构造器注入，只能用setter方法。
    + 对于有参数的，使用 constructor-arg 标签注入。
+ 自动装配。使用autowire标签，只能注入对象引用。



### 2. 静态工厂实例化

bean标签的class属性为工厂名，factory-method属性为工厂获取实例的静态方法。

两种注入方式：

+ Setter注入。使用property标签。另外注意，此时我们不光要在实现类中提供setter，还需要在工厂返回的接口类型中声明对应的setter方法。
+ 构造器注入。对于静态工厂实例化的对象，同样可以指定构造器参数，使用 constructor-arg 标签提供参数，此时，这些实参将被传递到factory-method的形参。然后，factory-method 使用这几个参数来调用实例类的构造器。
+ 自动装配。autowire标签，只能注入对象引用。



### 3.实例工厂实例化

bean标签的factory-bean属性是工厂类的bean标签的id，factory-method是获取实例的非静态方法。

两种注入方式：

+ Setter注入，同静态工厂。
+ 构造器注入，同静态工厂。
+ 自动装配。autowire标签，只能注入对象引用。



### 4.FactoryBean 实例化

这是第3种实例工厂实例化的变形。

实例工厂类实现 FactoryBean\<T\> 接口。

接口中的方法：

+ getObject ：代替原始实例工厂的创建对象的方法。这里getObject方法没有参数。初始化工作可以用Java代码在方法中实现。
+ getObjectType：返回范型T的class对象。
+ isSingleton()：返回是否使用单例模式。

**疑惑：使用该方法是不是就不能使用setter、构造器和自动装配进行注入了？**





## Spring整合Mybatis(配置文件版本)

要在Spring中整合Mybatis，首先来看使用配置文件方式：

使用配置文件又可以根据是否使用Mapper接口代理分为两类：

### 1. 不使用Mapper代理

在原始的Mybatis开发中，不使用Mapper代理的流程是：

1. 创建SqlSessionFactory。这一步包括配置enviroment等和配置mapper文件。
2. 创建SqlSession。
3. 在SqlSession上使用Mapper的namespace+id调用对应的SQL语句。

因此，我们只需要将SqlSession交由IoC容器管理，然后将SqlSession作为DAO注入service即可，但创建SqlSession需要SqlSessionFactory，我们将它也交给IoC容器。

SqlSessionFactory可以用org.mybatis.spring.SqlSessionFactoryBean创建。它需要注入一个 DataSource。此外，需要使用 mapperLocations 属性来配置mapper文件的位置。

```xml
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource" />
    <property name="mapperLocations" value="classpath*:com/itheima/dao/AccountDao.xml" />
</bean>
```

SqlSession 可以用 org.mybatis.spring.SqlSessionTemplate 来创建。SqlSessionTemplate 是 MyBatis-Spring 的核心，是 SqlSession 的一个实现类。在创建时，需要使用构造器注入SqlSessionFactory。

```xml
<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
    <constructor-arg name="sqlSessionFactory" ref="sqlSessionFactory"/>
</bean>
```

现在，可以把这个sqlSession注入任意一个ServiceImpl(可以使用类型自动装配)，然后在ServiceImpl中调用sqlSession.selectOne(namespace+id)等方法来执行SQL。



### 2. 使用Mapper代理

如果使用Mapper代理，就不需要使用SqlSession来完成SQL语句了，这样我们的Service就可以使用自己定义的DAO。这个DAO就是我们的Mapper代理接口。

此时我们需要将代理类交给IoC容器管理，然后将代理类注入给service即可。注册代理类有3种方式：

#### 2.1 使用 MapperFactoryBean 注册

使用 MapperFactoryBean 注册一个Mapper代理类，需要提供代理接口全限定名，以及创建它们的SqlSessionFactory。

注意，这里代理接口的路径由专门注册它的MapperFactoryBean来指定，所以SqlSessionFactoryBean就只需要配置数据源。另外，如果代理接口中有方法不是通过注解实现，而是通过xml配置文件实现的，那么只要配置文件和代理接口在同一目录下，也无需指定。但如果配置文件和代理接口不在同一目录下， 仍需要在SqlSessionFactoryBean中指定mapperLocations。

```xml
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource" />
</bean>

<bean id="accountDao" class="org.mybatis.spring.mapper.MapperFactoryBean">
    <property name="mapperInterface" value="com.itheima.dao.AccountDao" />
    <property name="sqlSessionFactory" ref="sqlSessionFactory" />
</bean>
```

然后，这个注册的代理类将具有mapperInterface指定的类型，从而可以作为DAO自动装配到某个service中。

注意，这种方式的特点是，每次只能使用一个MapperFactoryBean注册一个指定的代理类。

使用 MapperFactoryBean 注册只能逐个进行，这太麻烦了，下面的2种方式可以批量注册。



#### 2.2 使用 \<mybatis:scan\> 注册

`<mybatis:scan/>` 标签会发现映射器，它发现映射器的方法与 Spring 内建的 `<context:component-scan/>` 发现 bean 的方法非常类似。 在使用这个标签时，需要加载对应的名称空间。

可以使用这个标签的`base-package` 属性设置映射器接口文件的基础包。通过使用逗号或分号分隔，可以设置多个包，并且会在所指定的包中递归搜索映射器。

注意，虽然这个标签没有显式要求配置SqlSessionFactory，但创建它还是不可缺少的。

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:mybatis="http://mybatis.org/schema/mybatis-spring"
       xsi:schemaLocation="
           http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd
           http://mybatis.org/schema/mybatis-spring
           http://mybatis.org/schema/mybatis-spring.xsd">

    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource" />
    </bean>
  	<mybatis:scan base-package="com.itheima.dao" />

  	<!-- ... -->
</beans>
```

被发现的映射器会按照 Spring 对自动发现组件的默认命名策略进行命名。也就是说，如果没有使用注解显式指定名称，将会使用映射器的首字母小写非全限定类名作为名称。但如果发现映射器具有 `@Component` 或 JSR-330 标准中 `@Named` 注解，会使用注解中的名称作为名称。

本质上，发现器会为基础包下的每个接口创建一个 MapperFactoryBean 。



#### 2.3 使用 MapperScannerConfigurer 注册

使用它来注册时，同样只需要指定Mapper接口所在的包，它将对这个包中的所有接口注册代理类，注册后的名字与mybatis-scan相同。

```xml
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource" />
</bean>

<bean id="mapperScannerConfigurer" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <property name="basePackage" value="com.itheima.dao" />
</bean>
```

>关于要不要在Mapper接口上加@Mapper注解的问题，取决于mapperScannerConfigurer有没有扫描的目标。
>
>在SSM项目中，MapperScannerConfigurer 的 basePackage 是不可缺少的属性，它会扫描这个包下的所有接口，所以它注册代理类的机制和Mapper注解无关。
>
>在SpringBoot项目中，由于有自动配置，所以容器中会被自动注册MapperScannerConfigurer，但是这种自动注册的情况并未指定Mapper接口所在的包，所以需要为Mapper接口加@Mapper注解以让MapperScannerConfigurer找到它们来解析（前提：Mapper接口所在的包在@ComponentScan扫描的范围内）



## 纯注解 Mybatis-Spring

需要注册的对象见配置文件Mybatis-Spring。

### 1.不使用Mapper代理

只需要在MybatisConfig配置类中注册SqlSessionFactory和SqlSession即可。

SqlSessionFactory可以通过SqlSessionFactoryBean的getObject获取。在获取之前，需要配置数据源和SQL映射文件。

```java
@Bean
public SqlSessionFactory sqlSessionFactory(DataSource dataSource) throws Exception {
    SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
    sqlSessionFactoryBean.setDataSource(dataSource);
    sqlSessionFactoryBean.setMapperLocations(resolveMapperLocations());
    return sqlSessionFactoryBean.getObject();
}

public Resource[] resolveMapperLocations() throws IOException {
    ResourcePatternResolver resourceResolver = new PathMatchingResourcePatternResolver();
    List<String> mapperLocations = new ArrayList<>();
    mapperLocations.add("classpath*:com/itheima/dao/*.xml");
    List<Resource> resources = new ArrayList();
    for (String mapperLocation : mapperLocations) {
        Resource[] mappers = resourceResolver.getResources(mapperLocation);
        resources.addAll(Arrays.asList(mappers));
    }
    System.out.println(resources.size());
    return resources.toArray(new Resource[resources.size()]);
}
```

SqlSession可以通过直接new一个sqlSessionTemplate获得，构造器中需要注入SqlSessionFactory。

```java
@Bean
public SqlSession sqlSession(SqlSessionFactory sqlSessionFactory) {
    return new SqlSessionTemplate(sqlSessionFactory);
}
```



### 2.使用Mapper代理

此时我们需要将代理类交给IoC容器管理，然后将代理类注入给service即可。注册代理类有3种方式：

#### 2.1 使用 MapperFactoryBean 注册

这里有一个需要注意的问题。这里不能返回MapperFactoryBean.getObject后的结果了，只能返回MapperFactoryBean自身。不过没关系，由于FactoryBean这类Bean很特殊，当使用ctx.getBean获取它时，不会返回FactoryBean，而是返回它封装的泛型类型。

至于为什么不能返回getObject后的结果，猜测是因为这个结果是一个接口，而IoC中不能直接注册一个接口，因此必须先注册FactoryBean。

为了统一起见，这里SqlSessionFactoryBean也返回了自身。

```java
@Bean
public SqlSessionFactoryBean sqlSessionFactory(DataSource dataSource) throws Exception {
    SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
    sqlSessionFactoryBean.setDataSource(dataSource);
    return sqlSessionFactoryBean;
}

@Bean
public MapperFactoryBean<AccountDao> accountDao(SqlSessionFactory sqlSessionFactory) throws Exception {
    MapperFactoryBean<AccountDao> mapperFactoryBean = new MapperFactoryBean<>();
    mapperFactoryBean.setSqlSessionFactory(sqlSessionFactory);
    mapperFactoryBean.setMapperInterface(com.itheima.dao.AccountDao.class);
    return mapperFactoryBean;
}
```



#### 2.2 使用 @MapperScan 注册

@MapperScan 注解是 \<mybatis:scan\> 从配置文件变成纯注解的形式。

只需要在配置类上添加@MapperScan注解，在其中写上base-package即可。可以写在@Configuration类上，也可以写在单独的配置类上后Import。

```java
@MapperScan("com.itheima.dao")
public class MybatisConfig {
    @Bean
    public SqlSessionFactoryBean sqlSessionFactory(DataSource dataSource) throws Exception {
        SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
        sqlSessionFactoryBean.setDataSource(dataSource);
        return sqlSessionFactoryBean;
    }
}
```



#### 2.3 使用 MapperScannerConfigurer 注册

可以直接new一个MapperScannerConfigurer对象，然后配置basePackage即可。

```java
@Bean
public SqlSessionFactoryBean sqlSessionFactory(DataSource dataSource) throws Exception {
    SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
    sqlSessionFactoryBean.setDataSource(dataSource);
    return sqlSessionFactoryBean;
}

@Bean
public MapperScannerConfigurer mapperScannerConfigurer(){
    MapperScannerConfigurer msc = new MapperScannerConfigurer();
    msc.setBasePackage("com.itheima.dao");
    return msc;
}
```



综上，spring整合mybatis可以分为两大类，使用配置文件和使用纯注解。二者又分别具有两种类型，即不使用Mapper代理类和使用Mapper代理类。对于不使用Mapper代理类，配置文件方式和纯注解都是要创建SqlSessionFactory和SqlSession。对于使用Mapper代理类，配置文件和纯注解都有3种方式：

+ 每个代理类创建一个MapperFactoryBean；
+ 使用mybatis:scan/MapperScan()批量创建MapperFactoryBean；
+ 使用MapperScannerConfigurer批量创建MapperFactoryBean。



## Spring配置文件和纯注解扫描Bean的区别

配置文件中使用 \<context:component-scan base-packet="..."\> 标签来配置扫描的基础包。

纯注解中使用 @ComponentScan("...") 注解来配置扫描的基础包。多个基础包则用列表包装起来。



## Spring配置文件和纯注解加载Properties的区别

配置文件中使用 \<context:property-placeholder location="..."\> 标签来加载properties文件，可以使用通配符。

纯注解中使用 @PropertySource("...") 注解来配置。不可以使用通配符。多个配置文件可以使用列表包装起来。



## Spring配置文件和纯注解的自动装配的区别

配置文件自动装配是在bean标签上加属性 autowire="byType/byName"。这种方法要求要装配的成员有setter方法。

纯注解自动装配是在值类型成员上添加注解@Value(...)，在引用类型成员上添加注解@Autowired。内部使用反射暴力填充，不需要有setter方法。默认使用按类型装配，如果IoC容器中有名字与成员变量相同的Bean，则按照名字装配。另外，可以增加@Qualifier("beanName")注解来强制装配指定名称的Bean。





## 什么是classpath，classpath: 和 classpath*: 的区别

**classpath意为类路径，是Java程序的一个环境变量，JVM需要加载类文件时在classpath所指定的目录下搜索。通常来说classpath是当前项目的顶级package目录，也可以包含一些自定义的jar包目录，用于导入一些第三方jar包。**

"classpath:"或者"classpath*:"都不是Java语言自带的东西，它只是Spring自定义的一种路径格式前缀而己，意思是以classpath作为根目录的指定位置。

首先需要说明的是，"classpath:"与"classpath:/"对于Spring来说是没有区别的，Spring框架在处理这个路径的时候，会将开头的"/"符号去掉，这点对于"classpath*:/" 也是一样。

在说明 "classpath:" 和 "classpath*:" 的区别之前，我们先将 classpath 中的类路径分个类。

我们知道，classpath 包含两类路径：

1. 项目中顶层Package的路径和其他自定义的路径。该路径的子目录中包括了class文件的基目录，如com、org等包，也可能包括了一些资源文件目录等。为方便起见，我们统一称为**扩展路径**。
2. 项目要使用的所有JAR包的路径。为方便起见，我们称为**JAR包路径**。

Spring在使用这两个前缀搜索资源时，可能会在它们的后面加通配符，下面进行分类讨论。

### 不包含通配符的路径

所谓不包含通配符的路径，指的是"classpath:"以及"classpath\*:"后面资源文件的路径（含文件名）不包含通配符"*"、"?"等。Spring在进一步处理路径时，首先会判断路径是否包含通配符，有和没有通配符的处理方式是完全不同的，这也是我们分开讨论的原因。

1. classpath: 
    + 既可以查找扩展路径下的资源，也可以查找JAR包路径下的资源。
    + 查找顺序是按照类路径定义的顺序逐个查找(并不一定先查找扩展路径再查找JAR包路径），并返回查找到的第一个资源。
    + 返回的Resource都是ClasspathResource。
    + 如果资源位于扩展路径中，从ClasspathResource中获取到的是BufferedlnputStream。如果位于JAR包路径中，获取到的则是JarURLInputStream。

2. classpath*:
    + 既可以查找扩展路径下的资源，也可以查找JAR包路径下的资源。
    + 返回所有匹配的资源，所以查找顺序无关紧要。
    + 返回的Resource都是URLResource。
    + 如果资源位于扩展路径中，从URLResource获取到的是BufferedlnputStream；如果位于JAR包路径中，获取到的则是JarURLInputstream。



### 包含通配符的路径

包含通配符的路径指的是"classpath:"以及"classpath\*:" 后面资源文件的路径（含文件名）包含通配符"\*", "?"等。这种情况比较复杂，对于classpath尤其如此。

Spring在查找包含通配符的路径时，首先会从路径中提取出一个不包含通配符的"根目录"，它是从根路径开始的、一个没有通配符的最长路径”。例如，"classpath:static/image/\*\*/icon.png"的根目录为"static/image"，而"classpath:\*\*/image/first/icon.png" 的根目录为""（ 后面我们称为空目录），另外"classpath:i\*on.png"的根目录也是空目录。

可以看到，包含通配符的路径，其提取出的根目录有两种情况，一种是空目录，一种是包含了有效路径的目录，"classpath:"在这两种不同的根目录下查找行为有所区别，而"classpath*:"保持了一致。

1. classpath:
    1. 当根目录是空目录时：
        + 只能在扩展路径下查找资源，无法在JAR包路径内查找。
        + 查找过程：step1.首先过滤掉类路径中的JAR包路径，剩下的扩展路径保持定义顺序不变；step2.按照类路径定义的顺序逐个查找扩展路径，如果在某个扩展目录下查找到匹配的资源文件，则将查找范围锁定在该扩展目录下，并返回该扩展目录下所有匹配的资源文件。
        + 返回的Resource都是 FileSystemResource，从中获取FilelnputStream。
        + 实际上，之所以无法从JAR包路径中查找，是因为ClassLoader的getResources方法在传入""时，只能返回扩展路径资源。
    2. 当根目录是有效目录时：
        + 既可以查找扩展路径下的资源，也可以查找JAR包路径下的资源。
        + 查找过程：step1.直接按照类路径定义的顺序逐个查找扩展目录或jar包，如果查找到包含"根目录"的某个类路径，则将查找范围锁定在此类路径下，并返回此类路径下所有匹配的资源文件，如果没有匹配的就返回空。
        + 如果资源位于扩展路径中，返回的Resource是FileSystemResource, 从中获取到FilelnputStream。如果资源位于jar包中返回的Resource是 ClasspathResource，从中获取到JarURLInputStream。
2. classpath*:
    + 既可以查找扩展路径下的资源，也可以查找JAR包路径下的资源。
    + 返回查找到的所有的匹配文件资源，因此可以不考虑查找顺序。
    + 如果资源位于扩展路径中，返回的Resource是FileSystemResource，从中获取到FilelnputStream。如果资源位于jar包中，返回的Resource是URLResource，从中获取到 JarURLInputStream。



总结如下：

+ "classpath*:"  总是能在类路径的扩展目录和jar包中查找，并且返回所有的匹配资源。
+ "classpath:"（不含通配符）：总是能按照类路径定义的顺序逐个查找，并返回第一个匹配的资源。
+ "classpath:"（含通配符）：首先判断根目录是否为空，来决定查找的类路径范围是否需要过滤掉JAR包路径。在处理后的类路径中按照定义的顺序逐一查找，直到查找出第一个匹配的资源文件，同时锁定该资源文件所在的类路径。之后查找并返回该锁定的类路径中所有匹配的资源文件。





## Spring被@Bean修饰的方法 的参数注入方法

@Bean注解修饰的方法的参数默认注入方式为Autowired，先根据类型匹配，若有多个该类型的Bean则根据名称进行匹配。

这里需要注意，@Bean修饰的方法返回的Bean的名称默认为方法名。

当形式参数的类型有多个Bean时，按名称进行匹配默认使用参数名作为匹配的Bean名称，如果有@Qualifier注解则使用指定的名称。

另外，对于普通方法，其参数可以显式加@Autowired注解来注入。



## Spring的AOP 实现原理

SpringAOP是基于动态代理实现的。Spring在创建Bean前，会先读取所有的切面类，之后在创建Bean时，会将类的方法与所有切面类中的切入点进行匹配。如果一个类的方法有和切入点匹配成功的，那么就创建这个目标类的代理类对象，然后加入IOC容器，如果没有匹配成功的方法，那么就创建这个类本身的对象。

>**这里SpringBoot的机制存疑。**
>
>注意，这与SpringBoot不同，SpringBoot的依赖注入使用的是动态代理技术，因此在实例化接口时，SpringBoot会自动为这个接口创建一个代理对象，也就是说Spring Boot 会为每个使用了 @Service、@Component 等注解的类创建一个代理对象。当我们在其他类中使用 @Autowired 注解注入某个接口的时候，实际上注入的是这个接口的代理对象，而不是直接的实现类。

创建代理类对象时采用的方法是动态代理。Spring对不同的对象采用不同的动态代理机制：

1. 对于实现了接口的类，默认使用JDK动态代理，也可以强制使用CGLIB动态代理。
2. 对于没有实现接口的类，必须使用CGLIB动态代理。

JDK动态代理是利用反射机制生成一个实现代理接口的匿名类，在调用具体方法前调用InvokeHandler来处理。而CGLIB动态代理是利用asm开源包，对代理对象类的class文件加载进来，通过修改其字节码生成目标类的子类来进行实例化。

Spring的事务管理中，@Transactional注解是基于AOP实现的，因此加了该注解的类或方法相当于使用AOP进行了增强，于是Spring会通过生成他们的代理类来实现事务功能。



## Spring的事务管理

### Spring中提供了两种事务管理机制：

+ 编程式事务：是指在代码中手动的管理事务的提交、回滚等操作，代码侵入性比较强。（Spring提供了TransactionTemplate模板，利用该模板我们可以通过编程的方式实现事务管理，而无需关注资源获取、复用、释放、事务同步及异常处理等操作。相对于声明式事务来说，这种方式相对麻烦一些，但是好在更为灵活，我们可以将事务管理的范围控制的更为精确）
+ 声明式事务：基于AOP面向切面的，它将具体业务与事务处理部分解耦，代码侵入性很低。而声明式事务有两种方式实现，方式一是基于@Transactional注解实现，方式二是基于XML实现。（Spring事务管理的亮点在于声明式事务管理，它允许我们通过声明的方式，在IoC配置中指定事务的边界和事务属性，Spring会自动在指定的事务边界上应用事务属性。相对于编程式事务来说，这种方式十分的方便，只需要在需要做事务管理的方法上，增加@Transactional注解，以声明事务特征即可）

@Transactional 可以作用在接口、类、方法：

1. 作用于接口：不推荐这种使用方法，因为一旦标注在Interface上并且配置了Spring AOP 使用CGLib动态代理，那么Spring将通过创建子类对象来动态代理实现类，这样就无法解析到@Transactional注解，从而导致@Transactional注解失效。因此，只有使用JDK代理时，接口上的注解才会生效。
2. 作用于类：当把@Transactional 注解放在类上时，代表这个类所有公共（public）非静态（static）非final的方法都将启用事务功能，且都会被 Spring 的事务管理器进行管理。
3. 作用于方法：当把@Transactional配置在方法上，该方法被当成一个独立的事务，且被事务管理器管理。当类配置了@Transactional，方法也配置了@Transactional，此时方法的事务会覆盖类的事务配置信息。
   

### @Transactional注解的实现原理

在方法上使用 @Transactional 注解时，Spring 将会创建一个代理对象来包装该方法。该代理对象将在该方法执行之前和之后添加一些代码，以启动和提交/回滚事务。

代理对象的创建和配置是由 Spring 的事务管理器完成的。Spring 支持多种事务管理器，例如 JDBC 事务管理器、Hibernate 事务管理器和 JTA 事务管理器。这些事务管理器负责管理事务的生命周期，并确保事务的一致性和隔离性。

在执行带有 @Transactional 注解的方法时，代理对象将首先尝试获取一个数据库连接。如果该方法已经在另一个事务中执行，则代理对象将重用该连接。否则，代理对象将从事务管理器中获取一个新的连接。然后，代理对象将启动事务，并将该连接与该事务相关联。

一旦方法执行完成，代理对象将检查方法是否抛出了异常。如果没有异常，则代理对象将提交事务，并将连接释放回事务管理器。如果方法抛出异常，则代理对象将回滚事务，并将连接释放回事务管理器。

需要注意的是，@Transactional 的默认行为是将 RuntimeException 和 Error 视为需要回滚事务的异常。这意味着如果您的方法抛出这些异常之一，则事务将被回滚。如果您想将其他异常也视为需要回滚事务的异常，则可以通过在 @Transactional 注解中添加 rollbackFor 属性来指定。



### @Transactional注解的失效场景：

### 1. @Transactional 应用在 非public 或 static 或 final方法上

@Transactional注解修饰的方法必须是public修饰的，同样的@Transactional修饰类时，也只有类中使用pulbic修饰的方法才能成为事务。
须知：使用@Transactional修饰的方法，必须是public修饰、非static修饰、非final修饰的，一个不满足就会导致事务失效。

这是由于Spring的事务是通过AOP实现的，在AOP增强时只能增强public、非static、非final的方法，因此事务注解也只能作用在此类方法上。AOP只能增强public，非static，非final方法的原因是，AOP是基于动态代理实现的，而无论是JDK动态代理还是CGLIB动态代理，他们都只支持代理上述的方法。JDK动态代理只能代理接口实现类，它创建了一个实现目标接口的匿名类，重写了要代理的方法，所以它只支持能够被重写的方法。CGLIB是通过创建修改目标类的子节码，创建目标类的子类来重写方法，因此它也只能代理子类可以重写的方法。从原理上来讲，CGLIB可以通过反射来访问父类的私有方法，但它没有这么做，也许是为了不破坏类设计者的初衷吧。



### 2. propagation 属性设置错误

当我们将 propagation 属性的值设置为一下几种取值就会导致事务失效：

1. Propagation.NOT_SUPPORTED：以非事务的方式运行，如果当前存在事务，暂停当前的事务
2. Propagation.NEVER：以非事务的方式运行，如果当前存在事务，则抛出异常



### 3. rollbackFor 属性设置错误

使用spring的@Transactiona开启事务,默认Error和RuntimeException及其子类才会回滚。如果在事务中抛出其他类型的异常，但却期望 Spring 能够回滚事务，就需要指定 rollbackFor属性；若在目标方法中抛出的异常是 rollbackFor 指定的异常的子类，事务同样会回滚。





### 4. 方法调用导致 @Transactional 失效

同一个类中，A方法是事务性方法，但是B方法是非事务性方法，如果使用B方法直接调用A，此时A方法的事务会失效。

这里的原因是，要使用经过AOP增强的A方法，就必须使用代理对象调用经过重写的A方法，但是如果直接从B方法中调用，那么调用的是未经过增强的A方法，因此不会有事务功能。解决办法是通过 AopContext.currentProxy() 这个API获取当前类的代理对象，在代理对象上调用A方法即可。



### 5. 异常捕获导致 @Transactional 失效

当一个事务方法中抛出了异常，此时该异常通过 try...catch 进行了捕获，导致异常被处理，那么事务将不会回滚，那么该方法的事务注解 @Transactional 失效。



### 6. 数据库引擎不支持事务

Spring的事务本质还是得靠数据库引擎的支持，如果数据库引擎不支持事务，那么Spring就算使用事务也是白搭。常用的MySQL数据库默认使用支持事务的innodb引擎。一旦数据库引擎切换成不支持事务的myisam，那事务就从根本上失效了。



### 7. 未启用事务

想要 @Transactional 注解实现声明式事务，首先就需要开启事务，开启事务就三步：

1. 配置事务管理器
2. 开启事务的注解驱动
3. 使用@Transactional



### 8. 事务方法启动新线程进行异步操作

Spring 的事务是通过LocalThread来保证线程安全的，事务和当前线程绑定，此时开启新线程执行业务，这个新线程的业务就会事务失效，因为事务是基于动态代理的，要想有事务，需要被动态代理。这里提供一种解决方法，可以将新的业务单独封装成一个方法，然后改方法上添加一个@Transactional，或者将这个方法单独抽取到一个类中，将该类交给IOC容器进行管理，这样就能让新线程的业务具有事务了。











