# 1、概念

JavaScript  Object  Notation   

JavaScript对象表示法

现在多用于存储和交换信息的语法

进行数据的传输，比XML更小更快更易解析

# 2、语法

## 2.1、基本规则

1. 数据在名称/值对中：JSON数据是由键值对构成的
   1. 键用引号(单双都行)引起来，也可以不使用引号
   2. 值可以是数字，字符串，逻辑值，数组(方括号)，对象(花括号)，null
2. 数据由括号分割：多个键值对由逗号分割
3. 花括号保存对象：使用{}定义JSON格式
4. 方括号保存数组：[ ]

## 2.2、获取数据

1. json对象.键名
2. json对象["键名"]
3. 数据对象[索引]

## 2.3、JSON数据和java对象的相互转换

JSON解析器：

​	常见的解析器：JsonLib、Gson、fastjson、jackson

### 1、JSON数据转java对象

使用步骤：

1. 导入jsckson的jar包
2. 创建Jackson核心对象ObjectMapper
3. 调用相关方法来转换
   1. readValue(Json数据，class类型)

### 2、java对象转JSON

1. 使用步骤：

   1. 导入jsckson的jar包

   2. 创建Jackson核心对象ObjectMapper

   3. 调用相关方法来转换

      1. 转换方法：

         1. ```
            writeValue(参数1，obj)
                参数1：
                    File：将obj对象转换为JSON字符串，并保存到指定的文件中
                    Write：将obj对象转换为JSON字符串，并将json数据填充到字符输出流中
                    OutputStream：将obj对象转换为JSON字符串，并将json数据填充到字节输出流中
            writeValueAsString(obj)：将对象转为字符串
            ```

         2. 注解

            1. @JsonIgnore：排除属性
            2. @JsonFormat：属性值格式化

         3. 复杂java对象转换

            1. 

         

### 3、js对象转json字符串

```javascript
JSON.stringify(对象);
//接收返回值即可，不用导包
```

### 4、将json字符串妆换为js对象

```javascript
JSON.parse(str);
```

# 3、后端使用

导入jackson的依赖

控制器的方法上使用注解@ResponseBody，将服务器返回发对象转换为json对象

使用

```java
@RequestMapping(value = "/json",produces = "application/json;charset=utf-8")//防止放回数据乱码
@ResponseBody
public String json(){
    //需要一个jackson的对象映射器，就是一个类，使用它可以直接将对象转成json字符串
    ObjectMapper mapper = new ObjectMapper();
    
    //创建一个对象
    User user = new User(....);
    
    //将java对象转换为json字符串，要抛出异常，这里没写
    String str = mapper.writeValueAsString(user);
    
    return str;
    
}
```

## 乱码处理

json返回中文乱码可以通过该SpringMVC的配置文件解决

```xml
<mvc:annotation-driven>
	<mvc:message-cnverters register-defaults="true">
    	<baen class="org.springframework.http.converter.StringHttpMessageConverter">
        <constructor-arg value="UTF-8"/>
        </baen>
        <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
        <property name="objectMapper">
            <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
            <property name="failOnEmptyBeams" value="false"/>
            </bean>
            </property>
        </bean>
    </mvc:message-cnverters>
</mvc:annotation-driven>
```

## 使用date

date转换后会变成时间戳，需要一个配置关闭时间戳功能

```java
ObjectMapper mapper = new ObjectMapper();
//关闭时间戳
mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS,false);
//自定日期格式化对象
SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
//让mapper指定时间日期格式为SimpleDateFormat
mapper.setDateFormat(sdf);

//写一个日期对象
Date date = new Date();

return mapper.writeValueAsString(d)
```

