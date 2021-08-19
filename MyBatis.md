# 简介

## 什么是MyBatis

- mybatis是一款优秀的持久层框架
- 支持定制化sql，存储过程以及高级映射
- mybatis避免了几乎所有的jdbc代码和手动设置参数以及获取结果集
- mybatis可以使用简单的xml或注解来配置和映射原生类型，接口和pojo，为数据库中的记录

## 持久化

数据持久化

持久化就是将程序的数据在持久状态和瞬时状态转化的过程

## 持久层

完成持久化工作的代码块

# 第一个mybatis程序

**思路**：搭建环境-->导入jar包-->编写代码-->测试

## 搭建数据库



## 新建项目

1. 新建普通的maven项目
2. 删除src，变成父工程
3. 导入maven依赖，（mysql驱动，mybatis，junit）

## 新建模块

编写mybatis的核心配置文件

​	在resources文件夹下新建mybatis-config.xml

模板为：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <property name="driver" value="${driver}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${username}"/>
        <property name="password" value="${password}"/>
      </dataSource>
    </environment>
  </environments>
  <mappers>
    <mapper resource="org/mybatis/example/BlogMapper.xml"/>
      
  </mappers>
</configuration>
```



编写mybatis工具类

```java
private static SqlSessionFactory sqlSessionFactory;
static{
    //使用mybatis获取sqlsessionFactory对象
	String resource = "org/mybatis/example/mybatis-config.xml";
InputStream inputStream = Resources.getResourceAsStream(resource);
sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
}
```

(需要处理异常)

写一个方法，使用工厂类获取sqlsession并返回

## 写代码

实体类



dao层接口



建立mapper映射文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--namespace要绑定一个对应的dao/mapper接口 -->
<mapper namespace="org.mybatis.example.BlogMapper">
  <select id="selectBlog" resultType="Blog">
    select * from Blog where id = #{id}
  </select>
</mapper>
```

## 测试

1、获取SqlSession对象，（使用之前建的工具类）

2、使用getmapper获取mapper文件

```
UserDao userdao = sqlsession.getMapper(mapper文件名)
```

3、执行

```
userdao.mapper文件中的id
```

4、关闭sqlsession



## 注意

1. 创建IUSerDao.xml和IUserDao.java时名称时为了与之前学的一致，在Mybatis中把持久层的操作接口名称和映射文件也叫做Mapper
2. 在IDEA中创建目录时，它和包不一样
3. mybatis的映射配置文件位置必须和dao接口的包结构相同
4. 映射配置文件的mapper标签namespace属性的取值必须是dao接口的全限定类名
5. 映射配置文件的操作配置，id属性的取值必须是dao接口的方法名

当遵从了3,4,5点后，在开发中就无需再写dao的实现类

# CRUD

## namespace

namespace中的包名要和dao/mapper接口的包名一致

## select

查询语句

- id就是对应的namespace中的方法名
- resultType：sql语句返回值
- parameterType：参数类型



1. 编写接口
2. 编写对应的mapper中的sql语句
3. 测试

## insert



## update



## delete



**注意**：增删改需要提交事务

# 配置解析

## 核心配置文件

mybatis-config.xml



```
configuration（配置）
properties（属性）
settings（设置）
typeAliases（类型别名）
typeHandlers（类型处理器）
objectFactory（对象工厂）
plugins（插件）
environments（环境配置）
environment（环境变量）
transactionManager（事务管理器）
dataSource（数据源）
databaseIdProvider（数据库厂商标识）
mappers（映射器）
```

## 环境配置

mybatis可以配置成适应多种环境

**尽管可以配置多个环境，但每个 SqlSessionFactory 实例只能选择一种环境。**

```xml
<environments default="development">
  <environment id="development">
    <transactionManager type="JDBC">
      <property name="..." value="..."/>
    </transactionManager>
    <dataSource type="POOLED">
      <property name="driver" value="${driver}"/>
      <property name="url" value="${url}"/>
      <property name="username" value="${username}"/>
      <property name="password" value="${password}"/>
    </dataSource>
  </environment>
</environments>
```

## 属性

可以使用外部properties

```xml
<properties resource="org/mybatis/example/config.properties"/>
```

**注意**：引入到主配置文件后可以在主配置文件中添加或覆盖

后面在配置环境的时候可以直接调用外部配置文件的内容

```xml
<dataSource type="POOLED">
  <property name="driver" value="${driver}"/>
  <property name="url" value="${url}"/>
  <property name="username" value="${username}"/>
  <property name="password" value="${password}"/>
</dataSource>
```

## 别名

类型别名可为 Java 类型设置一个缩写名字。 它仅用于 XML 配置，意在降低冗余的全限定类名书写。例如：

```xml
<typeAliases>
  <typeAlias alias="Author" type="domain.blog.Author"/>
  <typeAlias alias="Blog" type="domain.blog.Blog"/>
  <typeAlias alias="Comment" type="domain.blog.Comment"/>
  <typeAlias alias="Post" type="domain.blog.Post"/>
  <typeAlias alias="Section" type="domain.blog.Section"/>
  <typeAlias alias="Tag" type="domain.blog.Tag"/>
</typeAliases>
```

也可以指定一个包名，MyBatis 会在包名下面搜索需要的 Java Bean

```xml
<typeAliases>
  <package name="domain.blog"/>
</typeAliases>
```

第一种可以DIY别名，第二种如果要改就只能在实体类上使用注解

## 设置

logImpl，指定 MyBatis 所用日志的具体实现，未指定时将自动查找。

## 映射器

MapperRegistry：注册绑定我们的Mapper文件

方式一：

```xml
<mappers>
  <mapper resource="org/mybatis/builder/AuthorMapper.xml"/>
  <mapper resource="org/mybatis/builder/BlogMapper.xml"/>
  <mapper resource="org/mybatis/builder/PostMapper.xml"/>
</mappers>
```

方式二：

使用class文件绑定注册

```xml
<mappers>
  <mapper class="org.mybatis.builder.AuthorMapper"/>
  <mapper class="org.mybatis.builder.BlogMapper"/>
  <mapper class="org.mybatis.builder.PostMapper"/>
</mappers>
```

注意点：

- 接口和他的mapper文件必须同名
- 接口和mapper文件必须在同一个包下

方式三：扫描包

```xml
<!-- 将包内的映射器接口实现全部注册为映射器 -->
<mappers>
  <package name="org.mybatis.builder"/>
</mappers>
```

## 结果集映射

当表中字段和实体类属性名称不同时无法自动映射，可以通过配置结果集映射的方式实现

```xml
<resultMap id="test" type="实体类">
	<result column="表中字段" property="实体类属性名"
</resultMap>
    
    <select id = "xxx" resultMap="test">
    xxxxx
    </select>
```

# 日志

## 日志工厂

如果数据库出现了异常，需要排错，日志就是最好的助手

STDOUT_LOGGING标准日志输出

**使用**

在mybatis的主配置文件中使用settings设置

```xml
<settings>
 
 <setting name="logImpl" value=" "/>
</settings>
```

## Log4J

先导入log4j的maven依赖

建立log4j的配置文件

**使用**

1. 在要使用日志的类中使用apache.log4j.Logger
2. 日志对象，参数为当前类的class
3. 调用Logger对象的方法输出日志

# 分页

## 使用limit分页

```sql
select * from xxx limit startIndex,pageSize
```

只给一个参数即默认startIndex为0

## RowBound分页

不建议使用



## 分页插件

pagehelper

# 使用注解开发

1、在主配置文件中绑定接口

```xml
<mappers>
	<mapper class="xxx"/>
</mappers>
```

2、在接口文件的方法上使用注解写sql语句

注解使用复杂sql语句会很复杂，还是推荐使用xml

## 注解CRUD

可以在工具类创建的时候实现自动提交事务（开启session时）

参数对应sql语句参数的方式：

```java
@Select("select * from xxx where name = #{name}")
User get(@Param("name") String name)
```

# Lombok

自动构建插件，很多公司不让用

# 动态sql

动态sql就是指根据不同的条件生成不同的sql语句

```
如果你之前用过 JSTL 或任何基于类 XML 语言的文本处理器，你对动态 SQL 元素可能会感觉似曾相识。在 MyBatis 之前的版本中，需要花时间了解大量的元素。借助功能强大的基于 OGNL 的表达式，MyBatis 3 替换了之前的大部分元素，大大精简了元素种类，现在要学习的元素种类比原来的一半还要少。

if
choose (when, otherwise)
trim (where, set)
foreach
```

## 搭建环境

创建一个基础工程

1. 导包
2. 编写配置文件
3. 编写实体类
4. 编写实体列对应Mapper接口和Mapper.xml文件

## if

当条件符合时拼接一段sql

示例

```xml
<select id="findActiveBlogWithTitleLike"
     resultType="Blog">
  SELECT * FROM BLOG
  WHERE state = ‘ACTIVE’
  <if test="title != null">
    AND title like #{title}
  </if>
</select>
```

## choose (when, otherwise)

类似于java中的switch

示例：

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <choose>
    <when test="title != null">
      AND title like #{title}
    </when>
    <when test="author != null and author.name != null">
      AND author_name like #{author.name}
    </when>
    <otherwise>
      AND featured = 1
    </otherwise>
  </choose>
</select>
```



## trim (where, set)

用于解决if标签或者choose标签没有条件成立时的情况，根据有无返回sql子句自动添加where标签保持sql语句正常

示例

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG
  <where>
    <if test="state != null">
         state = #{state}
    </if>
    <if test="title != null">
        AND title like #{title}
    </if>
    <if test="author != null and author.name != null">
        AND author_name like #{author.name}
    </if>
  </where>
</select>
```

在有子句返回的情况就会添加where，如果没有子句返回，sql语句就是SELECT * FROM BLOG

*where* 元素只会在子元素返回任何内容的情况下才插入 “WHERE” 子句。而且，若子句的开头为 “AND” 或 “OR”，*where* 元素也会将它们去除。

如果 *where* 元素与你期望的不太一样，你也可以通过自定义 trim 元素来定制 *where* 元素的功能。比如，和 *where* 元素等价的自定义 trim 元素为：

```xml
<trim prefix="WHERE" prefixOverrides="AND |OR ">
  ...
</trim>
```

用于动态更新语句的类似解决方案叫做 *set*。*set* 元素可以用于动态包含需要更新的列，忽略其它不更新的列。比如：

```xml
<update id="updateAuthorIfNecessary">
  update Author
    <set>
      <if test="username != null">username=#{username},</if>
      <if test="password != null">password=#{password},</if>
      <if test="email != null">email=#{email},</if>
      <if test="bio != null">bio=#{bio}</if>
    </set>
  where id=#{id}
</update>
```

与where一样，set负责在有子句返回的时候自动添加set关键字，并且会自动删除多余的逗号

这个例子中，*set* 元素会动态地在行首插入 SET 关键字，并会删掉额外的逗号（这些逗号是在使用条件语句给列赋值时引入的）。

来看看与 *set* 元素等价的自定义 *trim* 元素吧：

```xml
<trim prefix="SET" suffixOverrides=",">
  ...
</trim>
```

## sql片段

可以使用sql标签定义sql片段，再通过片段的id插入到动态sql中，可以实现sql代码的复用

```xml
<sql id="xxx">
	xxxx
</sql>
<select id="xxx" parameterType="xxx">
	select * from xxx
    <where>
    	<include refid="sqlid"></include>
    </where>
</select>
```

## foreach

用于遍历，在sql语句中插入一个集合中所有元素

动态 SQL 的另一个常见使用场景是对集合进行遍历（尤其是在构建 IN 条件语句的时候）。

```xml
<select id="selectPostIn" resultType="domain.blog.Post">
  SELECT *
  FROM POST P
  WHERE ID in
  <foreach item="item" index="index" collection="list"
      open="(" separator="," close=")">
        #{item}
  </foreach>
</select>
```

