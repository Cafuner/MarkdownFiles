## 切换本机命令行的Java版本的办法：

用户的家目录下.zshrc中记录了Java的环境变量，更改即可。



## Idea中的Java项目如何切换JDK：

https://blog.csdn.net/Climbman/article/details/130859231





## java中char占用多少字节

> java中char占用的字节：1、java中内码中的char使用UTF16的方式编码，一个char占用两个字节；2、java中外码中char使用UTF8的方式编码，一个字符占用【1～6】个字节。

在讨论这个问题之前，我们需要先区分unicode和UTF。

- unicode ：统一的字符编号，仅仅提供字符与编号间映射。符号数量在不断增加，已超百万。详细：[https://zh.wikipedia.org/zh-cn/Unicode]
- UTF ：unicode转换格式 (unicode transformation format) 。定义unicode中编号的编码方式。utf8和utf16便是其中两种实现方式。其中utf8为变长表示，长度可能时1～6个字节；utf16为变长表示，长度可能是2或4个字节。详细：UTF8 [https://zh.wikipedia.org/zh-cn/UTF-8] UTF16 [https://zh.wikipedia.org/zh-cn/UTF-16]

接着，要分清内码（internal encoding）和外码（external encoding）。

- 内码 :某种语言运行时，其char和string在内存中的编码方式。
- 外码 :除了内码，皆是外码。

要注意的是，源代码编译产生的目标代码文件（可执行文件或class文件）中的编码方式属于外码。

先看一下内码

JVM中内码采用UTF16。早期，UTF16采用固定长度2字节的方式编码，两个字节可以表示65536种符号（其实真正能表示要比这个少），足以表示当时unicode中所有字符。但是随着unicode中字符的增加，2个字节无法表示所有的字符，UTF16采用了2字节或4字节的方式来完成编码。Java为应对这种情况，考虑到向前兼容的要求，Java用一对char来表示那些需要4字节的字符。所以，java中的char是占用两个字节，只不过有些字符需要两个char来表示。

外码

Java的class文件采用UTF8来存储字符，也就是说，class中字符占1～6个字节。

Java序列化时，字符也采用UTF8编码，占1～6个字符。

总结：

- java中内码（运行内存）中的char使用UTF16的方式编码，一个char占用两个字节，但是某些字符需要两个char来表示。所以，一个Unicode字符会占用2个或4个字节。
- java中外码中char使用UTF8的方式编码，一个字符占用1～6个字节。
- UTF16编码中，英文字符占两个字节；绝大多数汉字（尤其是常用汉字）占用两个字节，个别汉字（在后期加入unicode编码的汉字，一般是极少用到的生僻字）占用四个字节。
- UTF8编码中，英文字符占用一个字节；绝大多数汉字占用三个字节，个别汉字占用四个字节。





## 到底什么是classpath？

简而言之，就是JVM查找类文件的路径集合。下面具体解释。

>我们知道，Java是一门解释执行的语言，编译好的字节码文件被放在不同的操作系统平台上的jvm所解释执行。一个类如果要被JVM所调度执行，必须先把这个类加载到JVM内存里。例如，java.lang下有个很重要的类ClassLoader，这个类主要就是用来把指定名称(指定路径下)的类加载到JVM中。
>
>Java中的ClassLoader总共有以下4类：
>
>1. JVM类加载器：这个类加载器会加载 JAVA_HOME/lib 下的jar包。
>2. 扩展类加载器：会加载 JAVA_HOME/lib/ext 下的jar包。
>3. 系统类加载器：这个会去加载 classpath 参数指定的jar文件。
>4. 用户自定义类加载器：sun提供的ClassLoader是可以被继承的，允许用户自己实现类加载器
>
>前两个类加载器都是加载 JAVA_HOME 下的系统使用的jar包，此处不详细介绍。
>
>classpath 的设置原理是：
>
>1. 如果在启动程序时使用了 -cp 或 -classpath传递参数，那么classpath就使用传递的参数。
>2. 如果没有传递classpath参数，那么就查看是否有系统环境变量 CLASSPATH，如果有就使用它作为classpath。
>3. 如果也没有CLASSPATH，那么就使用当前目录 "." 作为 classpath。
>
>需要注意的是，某些情况下不需要我们设置classpath，比如在tomcat或者jetty中启动应用，亦或者是通过springboot可执行jar启动应用时，我们都未设置classpath，但这并不代表不用设置，而是框架或者容器替我们做好了这个事。
>
>另外，在Java程序中可以这样打印出classpath，打印出的结果是所有路径使用冒号拼接起来的字符串。
>
>```java
>System.out.println(System.getProperty("java.class.path"));
>```
>



我们在写Java程序时，经常用import来访问其他class文件：

```java
import com.heima.dao.MyClass;
```

之后当我们使用MyClass时，JVM去哪里查找MyClass呢？显然不可能遍历系统目录去查找 **/com/heima/dao/MyClass.class。

因此，我们需要为JVM提供一个路径列表，JVM会在这些列表中查找我们导入的这个类。这个路径列表就是 classpath。

在讨论如何设置 classpath 之前，我们首先来看看如何在一个目录和jar包中查找类文件。

假设我们的Myclass.class所在的Java项目把所有类文件都放在了target目录下。也就是说Myclass.class文件现在的位置是：

```
/somePath/MyProject/target/com/heima/dao/Myclass.class
```

于是，我们需要把前缀 "/somePath/MyProject/target" 加入到classpath，这样当JVM查找classpath中的这条路径时，就能够通过 import 后指定的 com.heima.dao.MyClass 来找到类文件。

现在如果要使用其他项目的jar包中的某个类，我们会这样导入它：

```java
import com.alibaba.druid.pool.DruidDataSource
```

假如这个jar包被放在了 "/anotherPath/repository/druid.jar" 。那么我们就需要把这个jar包的路径加入 classpath，这样JVM可以拼接出DruidDataSource类文件的位置："/anotherPath/repository/druid.jar/com.alibaba.druid.pool.DruidDataSource"。

这样看来，classpath中需要包括下面这两种路径：

1. 项目中顶层Package的路径。
2. 项目要使用的所有jar包的路径。

我们知道，可以通过 -cp 或 -classpath 选项来直接设置classpath，这也是推荐的方式。不建议使用系统环境变量 CLASSPATH，因为这会影响其他的Java程序。



























