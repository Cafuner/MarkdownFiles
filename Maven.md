## 关于资源文件的路径问题

在默认机制下，maven项目编译后的classpath是 target/classes。也就是说，我们在 src/main/java下实现的类在编译为class文件后，会被复制到target/classes目录下。复制时是保留目录结构的，即文件 src/main/java/com/zhangsan/Demo.java 在被编译后会被放在 target/classes/com/zhangsan/Demo.java 。而在src/main/java 下的其他文件(即非源代码文件)是不会被打包到classpath下的。

此外，src/main/resources 文件夹是默认的资源文件位置，这个文件夹下的文件会全部被复制到 target/classes 下。因此，如果我们在 Demo.java 中要加载某个资源文件，可以直接用资源文件名来访问该资源。

在pom.xml中我们可以使用resources标签来配置资源文件的位置。如果这样的话，就不再使用默认的 src/main/resources，而是使用pom文件中指定的资源文件。

resources 标签指定资源文件的方式有3种：

+ 子标签 directory 和 includes 一起使用，那么只有includes包含的文件会被复制到 classpath。
+ 子标签 directory 和 excludes 一起使用，那么只有不被excludes包含的文件会被复制到 classpath。
+ 子标签 directory 单独使用，那么整个directory下的文件都是资源文件，都会被复制到classpath下。

注意，includes 和 excludes 标签的子标签(include/exclude) 可以使用通配符。

### Maven替换资源文件中的占位符

如果资源文件中使用 ${...}的形式来设置内容，那么Maven需要对资源文件开启过滤，从而使用自己设置的properties属性来替换资源文件中的占位符。

通常，我们在父项目的pom文件中定义资源文件的位置并开启过滤，那么对于每个继承父项目的目录，资源文件的路径都是不同的，该怎么指定呢？可以使用Maven属性project.basedir，它表示当前项目的根目录路径。

```xml
<resources>
    <!--设置资源目录，并设置能够解析${}-->
    <resource>
        <directory>${project.basedir}/src/main/resources</directory>
        <filtering>true</filtering>
    </resource>
</resources>
```

另外，在SpringBoot项目中，spring-boot-starter-parent的pom文件已经设置了在{basedir}/src/main/resources目录下的资源文件，并对application*.yml, yaml, properties格式的3种资源文件开启了过滤)，而且使用标签<resource.delimiter>将取值语法改为了 `@...@`，因为spring害怕和其他语法有冲突，所以使用了这个配置。也就是说SpringBoot项目中资源文件读取pom属性的格式是 `@...@` 。如果想要修改回来，可以手动设置：

```xml
<properties>
	<resource.delimiter>${*}</resource.delimiter>
</properties>
```

也可以使用maven-resources-plugin插件，利用设置useDefaultDelimiters属性设置默认的占位符格式。

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-resources-plugin</artifactId>
    <version>3.2.0</version>
    <configuration>
        <encoding>UTF-8</encoding>
        <useDefaultDelimiters>true</useDefaultDelimiters>
    </configuration>
</plugin>
```



## 如何在Java代码中访问resources路径下的资源文件呢？整体的原则是，我们根据当前类文件的目录或者classpath来查找。

```java
InputStream is = this.getClass().getResourceAsStream(test.xml);
InputStream is = this.getClass().getResourceAsStream("/" +test.xml);
InputStream is = this.getClass().getClassLoader().getResourceAsStream(test.xml);
```

第一种方式会从当前类的目录下去找，这个文件如果不和该类在一个目录下，就找不到。 
第二种方式会从编译后的整个classes目录下去找，maven也会把资源文件打包进classes文件夹，所以可以找到。 
第三种方式中ClassLoader就是从整个classes目录找的，所以前面无需再加/。

