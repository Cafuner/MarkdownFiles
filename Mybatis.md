## 简化JDBC开发。使用时，需要安装jar包，或通过maven添加依赖。

在使用时，通过mybatis-config.xml配置文件，创建一个 SqlSessionFactory，然后通过它创建一个SqlSession，用于操作数据库。

有两种方式操作数据库：

1. 通过配置文件
2. 通过注解

首先，通过配置文件操作数据库，又分为两种。

1. 将sql语句放在mapper配置文件中，然后通过在SqlSession上调用selectAll、selectOne等方法执行sql语句。这些方法需要把mapper文件中sql语句的namespace和id作为参数传入。
2. 使用Mapper代理开发。使用代理的原因是上一种方法还是有很多硬编码问题。我们可以定义一个Mapper接口，mybatis会帮我们实现这个接口，我们只需要在这个接口上调用sql语句即可。具体的步骤如下：
    1. 定义与SQL映射文件同名的Mapper接口，并且将Mapper接口和SQL映射文件放置在同一目录下
    2. 设置SQL映射文件的namespace属性为Mapper接口全限定名
    3. 在Mapper 接口中定义方法，方法名就是SQL映射文件中sql语句的id，并保持参数类型和返回值类型一致
    4. 编码
        1. 通过 SqlSession 的 getMapper方法获取 Mapper接口的代理对象
        2. 调用对应方法完成sql的执行

>细节：如果Mapper接口名称和SQL映射文件名称相同，并在同一目录下，则可以使用包扫描的方式简化SQL映射文件的加载
>
><mappers>
>
>​	<package name="com.zhanghao.mapper"/>
>
></mappers>





## Mybatis核心配置文件

mybatis-config.xml 文件的名字不是固定的，但大家都使用这个名字。

该文件中的各个标签是有顺序的，不可乱序。





## 查询结果的封装

接口示例：

```java
public List<Members> selectMembersListByName(String name);
```

配置文件示例：

```xml
<select id="selectMembersListByName" resultType="members">
    select * from members where member_name like #{member_name}
</select>
```

运行结果；

```
[Member [id=3, member_name=关云长, password=123456, age=54], Member [id=4, member_name=关云长, password=123456, age=54]]
```

这里要注意，虽然返回的类型是List，但是配置文件里resultType还是填写members，mybatis会自动将对象封装成list集合。

​	



## mybatis 传递参数：

### 单参数

根据参数的类型，有以下几种情况：

1. P0J0类型：直接使用，属性名 和 参数占位符名称一致即可。

2. Map集合：直接使用，键名 和 参数占位符名称一致即可。

3. Collection集合：封装为Map集合，可以使用@Param注解，替换Map集合中默认的arg键名。

  map.put("arg0", collection集合);

  map.put("collection", collection集合);

  此外，如果它还是List：那么封装为Map集合，那么会比其他Collection集合多一个“list”键。

  map.put("List",List集合);

4. Array：封装为Map集合，可以使用@Param注解，替换Map集合中默认的arg键名

  map.put("arg0”，数组);

  map.put("array"，数组);

5. 其他类型：直接使用, 占位符名称可以任意取，总是那个参数的值。



### 多参数

当有多个参数时，有两种方式：

#### 1.使用@Param注解

在Mapper接口中申明每个参数，并使用 @Param("xxx") 来注解每个参数。例如：

```java
List< Brand> selectByCondition(@Param("status")int status, @Param("companyName")String companyName);
```

然后，在SQL语句中，在每个要填充参数的地方使用注解中的字符串即可。

```java
<select id="selectByCondition" ResultMap="brandResultMap">
    select * 
    from tb_brand
    where
    	status = #{status}
		and company_name like #{companyName}
</select>
```

#### 2.不使用注解

其实，如果不使用 @Param 注解，也可以完成传递散装参数。Mybatis默认会将所有参数封装为一个map，每一个参数会按照它在参数列表中的位置分配一个键，然后在map中插入键值对。最后，在SQL语句中可以使用参数的键来获取它的值。

在mybatis 3.4.2及之后的版本，每个参数有两个默认的键，可以通过任意一个来访问值，即可以在SQL语句中使用下面两种方法表示按顺序传入的参数。

+ #{arg0},#{arg1}...
+ #{param1}, #{param2}...

注意，这两种方式的下标起点不同，arg从0开始，param从1开始。

在mybatis 3.4.2及之前的版本，有下面两种方式：

- #{0}, #{1}...
- #{param1}, #{param2}...

实际上，当我们使用 @Param 注解时，只是替换了 arg 系列的键。即如果使用了@Param，那么每个参数的键将会是注解指定的名称和param系列的名称（注意，arg系列的不再可用）。

>疑惑：如果使用Map来传递参数，而Map中键对应的值是一个实体对象，那么在SQL语句中如何获得这个实体对象的成员呢？



## 动态条件查询 where 标签

where标签的工作原理是，如果它包含的标签中有返回值的话，它就会插入在SQL语句中插入一个“where”。此外，如果标签返回的内容是以 AND 或OR 开头的，则把它去除掉。





## 插入数据时主键返回

在使用 insert 标签时，我们插入的数据的主键可能是自动分配的，那么如何在让 insert 语句返回这个主键呢？

首先，在 insert 标签中加入 useGeneratedKeys="true" 属性，这表示插入数据之后返回一个自增的主键给你对应实体类中的主键属性。那么具体赋值给实体类对象的哪个属性呢？需要使用 keyproperty="xxx" 属性来指定。

例如，我们要插入的 Brand 对象，有id，name等字段，其中id是主键。我们在构造需要插入的数据时，构造一个Brand对象，将数据赋值给该对象，主键如果要数据库分配，那么就不需要指定，然后将这个对象传递给调用方法。

SQL语句是可能是这样的：

```java
<insert id="add" useGeneratedKeys="true" keyProperty="id">
    ...
```

这样以来，当调用Mapper接口方法后，Brand对象的id字段就被填充为分配的主键，我们就可以使用对象的getter方法获得主键。

```java
brandMapper.add(brand);
Integer id = brand.getId();
```



## 更新数据时的set标签

当set标签中有条件成立时就会附加set关键字，字段为null时该列不会被更新。set可以忽略与sql无关的逗号。通常和if标签一起使用。











