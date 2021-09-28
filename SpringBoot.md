# HelloWorld

## maven设置

使用阿里云镜像和默认使用jdk8进行编译

```xml
<mirrors>
      <mirror>
        <id>nexus-aliyun</id>
        <mirrorOf>central</mirrorOf>
        <name>Nexus aliyun</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public</url>
      </mirror>
  </mirrors>
 
  <profiles>
         <profile>
              <id>jdk-1.8</id>
              <activation>
                <activeByDefault>true</activeByDefault>
                <jdk>1.8</jdk>
              </activation>
              <properties>
                <maven.compiler.source>1.8</maven.compiler.source>
                <maven.compiler.target>1.8</maven.compiler.target>
                <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
              </properties>
         </profile>
  </profiles>
```

## HelloWorld

### 引入依赖

```xml
<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.4.RELEASE</version>
    </parent>


    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

    </dependencies>
```

### 写主程序

```java
@SpringBootApplication
public class MainApplication {
    public static void main(String[] args) {
        SpringApplication.run(MainApplication.class,args);
    }

}
```



### 编写业务



### 测试

直接运行main方法



### 配置

配置写在application.properties中



### 简化部署

```xml
 <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
```

将项目打成jar包，直接在服务器执行

# 自动装配

## 依赖管理

- 父项目做依赖管理
- 开发导入starter场景启动器
- 无需关注版本号，自动版本仲裁
- 可以修改版本号

````
查看Spring-boot-dependencies里有规定当前依赖版本用的key
在当前项目里重写配置
<properties>
<mysql-version>5.1.43</mysql-version>
</properties>
````



## 自动配置

先引入依赖

再配置

**默认的包结构**

- 主程序所在包及其下面所有包里面的组件都会被默认扫描进来
- 无需以前的包扫描配置
- 如果要改变，可以使用@SpringBootApplication(scanBasePackages ="  ")

# 容器功能

## 组件添加

### @Configuration

用于将类标注为配置类（等于配置文件）

在该类中使用@Bean注解给容器中添加组件

```java
/**
 * 配置类里面使用@Bean标注在方法上给容器注册组件，默认也是单例
 * 配置类本身也是组件
 * proxyBeanMethods：代理bean的方法
 *      Full：为true
 *      Lite：为false
 *      组件依赖
 */
//标注配置类 == 配置文件
@Configuration(proxyBeanMethods = true)
public class MyConfig {
    //单例模式
    @Bean //给容器中添加组件，方法名为id，返回类型为组件类型，返回的值就是实例
    public User user01(){
        return new User("zhangsan",18);
    }

    @Bean
    public Pet tomcatPet(){
        return new Pet("tomcat");
    }
}

```

### @Import

写在容器上，参数为想要导入类的class

### @Conditional

条件装配：满足conditional指定的条件则进行组件装配

举例：

@ConditionalOnBean(name = "tom")

容器中有名为tom的组件时才会加载，否则不加载

## 原生配置文件引入

@ImportResource("classpath:beans.xml")

写在配置类上，引入别的配置文件

## 配置绑定

将值写在SpringBoot的配置文件中，实体类通过注解获取（注解参数为配置文件中数据的前缀）

配置文件格式

```properties
mycar.brand=BYD
mycar.price=100000
```

实体类

```java
@Component //添加到容器
@ConfigurationProperties(prefix = "mycar") //配置绑定，在配置文件中赋值
public class Car {

    private String brand;
    private Integer price;

    public Car(String brand, Integer price) {
        this.brand = brand;
        this.price = price;
    }

    public Car() {
    }

    public String getBrand() {
        return brand;
    }

    public void setBrand(String brand) {
        this.brand = brand;
    }

    public Integer getPrice() {
        return price;
    }

    public void setPrice(Integer price) {
        this.price = price;
    }
}
```

**配置绑定实现方法二**

在配置类使用注解

开启Car配置绑定功能

把Car自动注册到容器中

```java
@EnableConfigurationProperties(Car.class)
```

实体类不用再使用@Component注解

# 开发技巧

## 步骤

1. 引入场景依赖

   1. 

   2. - https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-starter

2. 查看自动配置了哪些

   1. 配置文件中debug=true，开启自动配置报告

3. 是否需要修改

   1. 参照文档修改配置项
   2. 自定义加入或替换组件

## Lombok

简化javaBean的开发

```xml
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>


idea中搜索安装lombok插件
```



```java
===============================简化JavaBean开发===================================
@NoArgsConstructor
//@AllArgsConstructor
@Data
@ToString
@EqualsAndHashCode
public class User {

    private String name;
    private Integer age;

    private Pet pet;

    public User(String name,Integer age){
        this.name = name;
        this.age = age;
    }


}



================================简化日志开发===================================
@Slf4j
@RestController
public class HelloController {
    @RequestMapping("/hello")
    public String handle01(@RequestParam("name") String name){
        
        log.info("请求进来了....");
        
        return "Hello, Spring Boot 2!"+"你好："+name;
    }
}
```

## dev-tools

热部署插件

开发时不用重复启动项目

```xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <version>2.5.4</version>
        </dependency>
```

Ctrl+F9



或者使用JRebel（付费）

## Spring Initailizr（项目初始化向导）

快速创建项目

# Yaml

yaml文件适合做以数据为中心的配置文件

## 语法

- key: value kv之间有空格
- 大小写敏感
- 使用缩进表示层级关系
- 缩进不允许使用tab，只允许空格
- 缩进的空格数不重要，只要相同层级的元素左对齐
- '#' 表示注释
- 字符串无需加引号，如果要加，"与"表示字符串内容会被转义/不转义

## 数据类型

自变量：单个的，不可分割的值。date、boolean，String，number，null

```yaml
k: v
```

对象：键值对的集合，map，hash，set，object

```yaml
行内写法：
k: {k1: v1,k2: v2,k3: v3}
或
k:
  k1:v1
  k2:v2
  k3:v3
```

数组：一组按次序排列的值，array，list

```yaml
行内写法
k: {v1,v2,v3}
或
k:
  - v1
  - v2
  - v3
```

示例

```yaml
# yaml表示以上对象
person:
  userName: zhangsan
  boss: false
  birth: 2019/12/12 20:12:33
  age: 18
  pet: 
    name: tomcat
    weight: 23.4
  interests: [篮球,游泳]
  animal: 
    - jerry
    - mario
  score:
    english: 
      first: 30
      second: 40
      third: 50
    math: [131,140,148]
    chinese: {first: 128,second: 136}
  salarys: [3999,4999.98,5999.99]
  allPets:
    sick:
      - {name: tom}
      - {name: jerry,weight: 47}
    health: [{name: mario,weight: 47}]
```

## 自定义类的代码提示

当在配置文件中使用自定义类时一般没有代码提示，需要使用一个额外的插件

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>


 <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <excludes>
                        <exclude>
                            <groupId>org.springframework.boot</groupId>
                            <artifactId>spring-boot-configuration-processor</artifactId>
                        </exclude>
                    </excludes>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

# Web开发

## 简单功能

### 静态资源目录

只要静态资源放在类路径下： called /static (or /public or /resources or /META-INF/resources
访问 ： 当前项目根路径/ + 静态资源名 

### 静态资源访问前缀

```yaml
spring:
  mvc:
    static-path-pattern: /res/**
```

当前项目 + static-path-pattern + 静态资源名 = 静态资源文件夹下找

### webjar

自动映射 /webjars/**

https://www.webjar.org/

```xml

        <dependency>
            <groupId>org.webjars</groupId>
            <artifactId>jquery</artifactId>
            <version>3.5.1</version>
        </dependency>
```

访问地址：[http://localhost:8080/webjars/**jquery/3.5.1/jquery.js**](http://localhost:8080/webjars/jquery/3.5.1/jquery.js)   后面地址要按照依赖里面的包路径

### 欢迎页

静态资源路径下的index.html或controller能处理/index的都会被当成欢迎页

使用index.html注意不能配置静态资源访问前缀否则会访问不到

### favicon

图片命名为favicon.ico 放在静态资源目录下

自定义静态资源访问前缀也会影响

## 请求参数处理

可以在方法上使用@GetMapping注解获取多个参数，在参数列表中使用@PatchVariable获取

可以在参数列表使用@RequestHeader获取请求头

```java
@ResponseBody
    @GetMapping("/car/{id}/owner/{username}")
    public Map<String,Object> getCar(@PathVariable("id") Integer id,
                                     @PathVariable("username") String name,
                                     @PathVariable Map<String,String> pv,
                                     @RequestHeader("User-Agent") String userAgent
                                     ){
        Map<String,Object> map = new HashMap<>();

        map.put("id",id);
        map.put("name",name);
        map.put("pv",pv);
        map.put("userAgent",userAgent);
        return map;
    }
```

还可以获取cookie等的数据

### @RequestAttrbute

用于获取在请求域中的值

一个控制器在请求域存放数据并跳转至另一个请求域时可通过此注解获取

### 矩阵变量

参数格式:

```
/car/{path;low=34;brand=byd,audi,yd}
```

cookie被禁用后jsessionid不能传输，导致session不能使用，此时可以使用矩阵变量

SpringBoot默认禁用此功能

示例：

```java
//1、语法： 请求路径：/cars/sell;low=34;brand=byd,audi,yd
    //2、SpringBoot默认是禁用了矩阵变量的功能
    //      手动开启：原理。对于路径的处理。UrlPathHelper进行解析。
    //              removeSemicolonContent（移除分号内容）支持矩阵变量的
    //3、矩阵变量必须有url路径变量才能被解析
    @GetMapping("/cars/{path}")
    public Map carsSell(@MatrixVariable("low") Integer low,
                        @MatrixVariable("brand") List<String> brand,
                        @PathVariable("path") String path){
        Map<String,Object> map = new HashMap<>();

        map.put("low",low);
        map.put("brand",brand);
        map.put("path",path);
        return map;
    }

```

 使用此功能需要实现接口WebMvcConfigurer

并重写configurePathMatch方法，在其中创建新的UrlPathHelper

## 上传文件功能

### 页面表单

```html
<form method="post" action="/upload" enctype="multipart/form-data">
    <input type="file" name="file"><br>
    <input type="submit" value="提交">
</form>
```

### 文件上传代码

```java
    /**
     * MultipartFile 自动封装上传过来的文件
     * @param email
     * @param username
     * @param headerImg
     * @param photos
     * @return
     */
    @PostMapping("/upload")
    public String upload(@RequestParam("email") String email,
                         @RequestParam("username") String username,
                         @RequestPart("headerImg") MultipartFile headerImg,
                         @RequestPart("photos") MultipartFile[] photos) throws IOException {

        log.info("上传的信息：email={}，username={}，headerImg={}，photos={}",
                email,username,headerImg.getSize(),photos.length);

        if(!headerImg.isEmpty()){
            //保存到文件服务器，OSS服务器
            String originalFilename = headerImg.getOriginalFilename();
            headerImg.transferTo(new File("H:\\cache\\"+originalFilename));
        }

        if(photos.length > 0){
            for (MultipartFile photo : photos) {
                if(!photo.isEmpty()){
                    String originalFilename = photo.getOriginalFilename();
                    photo.transferTo(new File("H:\\cache\\"+originalFilename));
                }
            }
        }


        return "main";
    }
```

# 异常处理

1. 默认规则
   1. 默认情况下，Spring Boot提供`/error`处理所有错误的映射
   2. 对于机器客户端，会生成json响应，http状态码和异常消息的详细信息，对于浏览器，响应一个`whitellabel`错误视图，以html格式呈现相同的数据
   3. 自定义需要添加view解析为error
   4. 要完全替换默认行为，可以实现`ErrorController`并注册该类型的Bean定义，或添加ErrorAttribute类型的组件以使用现有机制替换
2. 定制错误处理逻辑
   1. 自定义错误页
      1. error/404.html   error/5xx.html
   2. @ControllerAdvice,@ExceptionHandle处理异常
   3. 实现HandleExceptionResolver处理异常

# 原生组件注入（Servler，Filter，Listener）

## 使用Servlet API

@ServletComponentScan(basePackages = **"com.atguigu.admin"**) :指定原生Servlet组件都放在那里



@WebServlet(urlPatterns = **"/my"**)：效果：直接响应，**没有经过Spring的拦截器？**

@WebFilter(urlPatterns={**"/css/\*"**,**"/images/\*"**})

@WebListener

# 定制化

