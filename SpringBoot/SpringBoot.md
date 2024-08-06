## Maven多环境与SpringBoot多环境兼容问题

在Maven中可以在pom.xml文件中使用profile配置多环境，而在SpringBoot中也可以在application.xml 或 application.yaml 或 application.yml  中配置多环境，那最终使用的是什么环境呢？

其实，所谓环境其本质就是一组变量的值。而Maven是用来管理项目的，因此Maven的环境应该比SpringBoot的环境层级高，因此我们的处理办法是，在pom.xml中配置一组环境，每个环境中配置一个属性表明所采用的环境，然后设置默认启动的环境。在SpringBoot配置文件中，我们读取pom.xml中生效的那个环境属性，这样就可以保持二者的多环境是相同的。

>注意，这里的逻辑是虽然Maven的多环境中每个都定义了所采用的环境名称，但只有Maven配置所采用的环境中定义的名称才会生效，这个环境名称才会被SpringBoot的配置文件读取。

另外，在pom.xml中配置的属性默认只能在pom中使用，因此，还需要加载一个插件maven-resources-plugin。

注意，在SpringBoot项目中，spring-boot-starter-parent的pom文件已经设置在{basedir}/src/main/resources目录下的资源文件，并对application*.yml, yaml, properties格式的3种资源文件开启了过滤)，而且使用标签<resource.delimiter>将取值语法改为了 `@...@`，因为spring害怕和其他语法有冲突，所以使用了这个配置。

下面的代码中SpringBoot的资源文件中仍使用$占位符，所以我们可以通过maven-resources-plugin插件将占位符格式设置回来。

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-resources-plugin</artifactId>
            <version>3.2.0</version>
            <configuration>
                <encoding>UTF-8</encoding>
                <useDefaultDelimiters>true</useDefaultDelimiters>
            </configuration>
        </plugin>
    </plugins>
</build>

<profiles>
    <!--开发环境-->
    <profile>
        <id>dev</id>
        <properties>
            <profile.active>dev</profile.active>
        </properties>
    </profile>
    <!--生产环境-->
    <profile>
        <id>pro</id>
        <properties>
            <profile.active>pro</profile.active>
        </properties>
        <activation>
            <activeByDefault>true</activeByDefault>
        </activation>
    </profile>
    <!--测试环境-->
    <profile>
        <id>test</id>
        <properties>
            <profile.active>test</profile.active>
        </properties>
    </profile>
</profiles>
```

```yml
#设置启用的环境
spring:
  profiles:
    active: ${profile.active}

---
#开发
spring:
  profiles: dev
server:
  port: 80
---
#生产
spring:
  profiles: pro
server:
  port: 81
---
#测试
spring:
  profiles: test
server:
  port: 82
---
```



## Spring Boot starter

传统的 Spring 项目想要运行，不仅需要导入各种依赖，还要对各种 XML 配置文件进行配置，十分繁琐，但 Spring Boot 项目在创建完成后，即使不编写任何代码，不进行任何配置也能够直接运行，这都要归功于 Spring Boot 的 starter 机制。

Spring Boot 将日常企业应用研发中的各种场景都抽取出来，做成一个个的 starter（启动器），starter 中整合了该场景下各种可能用到的依赖，用户只需要在 Maven 中引入 starter 依赖，SpringBoot 就能自动扫描到要加载的信息并启动相应的默认配置。starter 提供了大量的自动配置，让用户摆脱了处理各种依赖和配置的困扰。所有这些 starter 都遵循着约定成俗的默认配置，并允许用户调整这些配置，即遵循“约定大于配置”的原则。

并不是所有的 starter 都是由 Spring Boot 官方提供的，也有部分 starter 是第三方技术厂商提供的，例如 druid-spring-boot-starter 和 mybatis-spring-boot-starter 等等，配置这种第三方的starter时需要填写版本号，而Spring Boot官方提供的则不需要(spring-boot-dependencies中的dependencyManagement已经制定了版本号)。当然也存在个别第三方技术，Spring Boot 官方没提供 starter，第三方技术厂商也没有提供 starter。





## SpringBoot 读取配置文件方式

1. 使用@Value注解。使用 @Value("表达式") 注解可以从配置文件中读取数据，注解中用于读取属性名引用方式是:${一级属性名.二级属性名......}。
2. 使用Environment对象。上面方式读取到的数据特别零散，SpringBoot 还可以使用 @Autowired 注解注入 Environment 对象的方式读取数据。这种方式 SpringBoot 会将配置文件中所有的数据封装到 Environment 对象中，如果需要使用哪个数据只需要通过调用
    Environment 对象的 getProperty(String name) 方法获取。
3. 自定义对象。SpringBoot 还提供了将配置文件中的数据封装到我们自定义的实体类对象中的方式。具体操作如下:
    1. 将实体类 bean 的创建交给 Spring 管理。
    2. 在类上添加 @Component 注解。
    3. 使用 @ConfigurationProperties 注解表示加载配置文件，在该注解中也可以使用 prefix 属性指定只加载指定前缀的数据。
    4. 在使用配置的类中进行注入。





## SpringBoot整合Mybatis读取DAO接口的方式

1. 在每个DAO接口上加@Mappe接口，并且确保DAO处在引导类所在包或其子包中。
2. 在引导类上加@MapperScan注解，其属性为所要扫描的DAO所在包。









