# 主要功能

认证，授权

- 功能权限
- 访问权限
- 菜单权限

可以使用拦截器和过滤器实现，但是有大量的原生代码，使用框架简化开发

Spring Security是针对Spring项目的安全框架，也是Spring Boot底层安全模块默认的技术选型

**重点类**

- WebSecurityConfigurerAdapter：自定义Security策略
- AuthenticationManagerBuilder：自定义认证策略
- @EnableWebSecurity:开启WebSecurity模式

# 使用

直接导入依赖，不需要任何设置即可生效，此时默认用户名为user，密码会在控制台生成



```xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
```

## 自定义用户名密码

### 使用配置文件

在springboot的配置文件中

```properties
spring.security.user.name=user
spring.security.user.password=123456
```



### 使用配置类

新建一个类，继承WebSrcurityConfigurerAdapter

重写其中参数为AuthenticationManagerBuilder的configure方法（有三个Configure方法，功能不同）

```java
BCryptPasswordEncoder passwordEncoder=new BCryptPasswordEncoder();//密码加密对象，BCrypt强哈希方法 每次加密的结果都不一样。
        String passwprd = passwordEncoder.encode("123");
        auth.inMemoryAuthentication().withUser("root").password(passwprd).roles("admin");//将一个user添加到内存中，此处可更换为从数据库中获取
```

BCryptPasswordEncoder对象有可能出现问题导致登录不成功（不会报错）

可以在配置类中添加如下方法尝试解决

```java
@Bean
    PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }
```

### 自定义编写实现类

1. 创建配置类，设置使用哪个userDetailsService实现类
2. 编写接口实现类，返回User对象

配置类

```java
@Configuration
public class SecurityConfig1 extends WebSecurityConfigurerAdapter {

    //注入实现类
    @Autowired
    private UserDetailsService userDetailsService;

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        //使用实现类
        auth.userDetailsService(userDetailsService).passwordEncoder(passwordEncoder());
    }

    @Bean
    PasswordEncoder passwordEncoder(){
        return  new BCryptPasswordEncoder();
    }
}
```



接口实现类

```java
@Service("userDetailsService")
public class SecurityService implements UserDetailsService {
    @Override
    public UserDetails loadUserByUsername(String s) throws UsernameNotFoundException {

        //使用工具类创建一个权限列表，表示该角色的权限
        List<GrantedAuthority> auths = AuthorityUtils.commaSeparatedStringToAuthorityList("role");
        //返回角色
        return new User("user",new BCryptPasswordEncoder().encode("123"),auths);
    }
}
```



#### 使用数据库

1. 引入相关依赖（数据库，dao层框架）
2. 建库建表
3. 建实体类
4. 配置数据库信息
5. 创建数据库接口，继承BaseMapper接口（mybatis-plus）
6. 在自己写的实现类中调用mapper的方法查询数据库进行认证
7. 在启动类上添加MapperScan注解，扫描mapper

配置文件：application.properties

```properties
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/demo
spring.datasource.username=root
spring.datasource.password=123456
```



数据库接口

```java
@Repository
public interface UsersMapper extends BaseMapper<Users> {
}
```



实现类

```java
@Service("userDetailsService")
public class SecurityService implements UserDetailsService {

    @Autowired
    private UsersMapper userMapper;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        //调用usersMapper方法查询数据库
        QueryWrapper<Users> wrapper = new QueryWrapper<>();
        //where username = ?
        wrapper.eq("username",username);
        Users users = userMapper.selectOne(wrapper);
        //判断
        if (users==null){

            //抛异常，认证失败
            throw new UsernameNotFoundException("用户名不存在");
        }


        //使用工具类创建一个权限列表，表示该角色的权限
        List<GrantedAuthority> auths = AuthorityUtils.commaSeparatedStringToAuthorityList("role");
        //从返回结果中获取数据创建user并返回角色
        return new User(users.getUsername(),new BCryptPasswordEncoder().encode(users.getPassword()),auths);
    }
}
```



## 自定义登录页

首先自己编写一个登录页

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<form action="/login" method="post">
    用户名：
    <input type="text" name="username"><br>
    密码：
    <input type="password" name="password"><br>
    <input type="submit" value="login">
</form>
</body>
</html>
```

然后在配置类中重写参数为HttpSecurity的configure方法

```java
@Override
    protected void configure(HttpSecurity http) throws Exception {
        http.formLogin()//开启登录功能
                .loginProcessingUrl("/login")//自定义登录表单提交的url，与表单中action的属性值一致
                .usernameParameter("username").passwordParameter("password")//如input的name值不为username和password需要进行绑定，此处可以省略
                .successForwardUrl("/success")//设置登录成功时重定向的url
                .failureForwardUrl("/f")//设置登录失败时重定向的url，需要放行
                .loginPage("/login.html");//直接写登录页文件名称，或者跳转用的controiller的RequestMapping

        http.authorizeRequests()
                .antMatchers("/","/login.html","/f").permitAll()//放行登录页和失败时的请求路径，如果不放行则无法进入登录页
                .anyRequest().authenticated();//设置其他页面均需要登录再进入

        http.csrf().disable();//关闭csrf登录防护功能
    }
```

# 基于权限访问控制

## hasAuthority方法

如果当前主体具有指定的权限，则返回true，否则放回false

### 使用

在配置类添加相关配置，设置某个页面需要的访问权限

```java
http.suthorizeRequests().antMachers("/test/index").hasAuthority("admin");
//表示用户有admin权限才可以进/test/index
```

在实现类中添加角色的可以设置权限

## hasAnyAuthority方法

如果主体有任何提供的角色，就返回true

### 使用

同上，权限为一个列表，用逗号分割

## hasRole方法

如果用户具备给定角色就允许访问

## hasAnyRole方法



# 自定义403页面

新建403页面



配置类中做如下配置

```java
http.exceptionHandling().accessDeniedPage("/xxxx")
```

# 用户授权（注解使用）

## @Secured

判断是否具有角色，

要先在启动类上添加@EnableGlobalMethodSecurity(securedEnabled = true)注解，开启注解功能

在控制器方法上使用@Secured注解

```java
@Secured({"role1",role2})
```

## @PreAuthorize

进入方法前的权限验证

```java
@PreAuthorize("hasAnyAuthority('menu:system')")
```

## @PostAuthorize

在方法执行后进行权限验证，适合验证带有返回值的权限

## @PostFilter

权限验证之后对数据进行过滤，留下用户名是admin的数据

```java
@PostFilter("filterObject.username == 'admin'")
```

表达式中的filterObject引用的是方法返回值List的某一个元素

## @PreFilter

进入控制器之前对数据进行过滤



# 用户注销

配置类中添加退出映射地址

```java
http.logout().logoutUrl("/logout").logoutSuccessUrl("/index").permitAll();
```

前端访问/logout即可注销

# 自动登录

## 原理

认证成功后将Token写入cookie，再登录会从cookie中读取信息

## 使用

1. 开启功能
2. 进行配置



配置类设置

```java
http.rememberMe().tokenValiditySecond(60);//设置有效时长
```

前端添加复选框name="remember-me"即可

