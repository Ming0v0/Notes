整个Spring5框架的代码基于Java8,运行时兼容JDK9，许多不建议使用的类和方法在代码库中删除

## Spring5.0框架自带了通用的日志封装
（1）Spring5已经移除Log4jCongigListener，官方建议使用Log4j2
（2）Spring框架整合Log4j2
第一步：引入相关的依赖
第二步：创建 log4j2.xml 配置文件
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<!--日志级别以及优先级排序: OFF > FATAL > ERROR > WARN > INFO > DEBUG > TRACE >  ALL -->  
<!--Configuration 后面的status用于设置log4j2自身内部的信息输出，可以不设置，当设置成trace时，可以看到log4j2内部各种详细输出-->  
<configuration status="INFO">  
    <!--先定义所有的 appender-->    <appenders>  
        <!--输出日志信息到控制台-->  
        <console name="Console" target="SYSTEM_OUT">  
            <!--控制日志输出的格式-->  
            <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>  
        </console>  
    </appenders>  
    <!--然后定义 logger，只有定义 logger 并引入的 appender，appender 才会生效-->  
    <!--root：用于指定项目的根日志，如果没有单独指定 Logger，则会使用 root 作为 默认的日志输出-->  
    <loggers>  
        <root level="info">  
            <appender-ref ref="Console"/>  
        </root>  
    </loggers>  
</configuration>
```

![[attachment/Pasted image 20220908132000.png]]

## Spring5 框架核心容器支持@Nullable 注解

@Nullable 注解可以使用在方法上面，属性上面，参数上面，表示方法返回可以为空，属性值可以 为空，参数值可以为空

注解用在方法上面，方法返回值可以为空
```java
@Nullable  
Class<?> getType(String var1) throws NoSuchBeanDefinitionException;
```

注解使用在方法参数里面，方法参数可以为空
```java
public ClassPathXmlApplicationContext(String[] configLocations, @Nullable ApplicationContext parent) throws BeansException {  
    this(configLocations, true, parent);  
}
```

注解使用在属性上面，属性值可以为空
```java
@Nullable
String name;
```

## Spring5 核心容器支持函数式风格
GenericApplicationContext
```java
// 函数式风格创建对象，交给 spring 进行管理  
@Test  
public void testGenericApplicationContext() {  
    //1 创建 GenericApplicationContext 对象  
    GenericApplicationContext context = new GenericApplicationContext();  
    //2 调用 context 的方法对象注册  
    context.refresh();  
    context.registerBean("user1", User.class,() -> new User());  
    //3 获取在 spring 注册的对象  
    // User user = (User)context.getBean("com.atguigu.spring5.test.User");  
    User user = (User)context.getBean("user1");  
    System.out.println(user);  
}
```

## Spring5 支持整合 JUnit
### Spring5 整合 JUnit4
第一步：引入 Spring 相关针对测试依赖
![[attachment/Pasted image 20220908132548.png]]
第二步：创建测试类，使用注解方式完成
```java
@RunWith(SpringJUnit4ClassRunner.class) //单元测试框架  
@ContextConfiguration("classpath:bean.xml")//加载配置文件  
public class JTest4 {  
    @Autowired  
    private UserService userService;  
  
    @Test  
    public void test1() {  
        userService.accountMoney(100, 1, 2);  
    }  
}
```

### Spring5 整合 JUnit5
第一步：引入 JUnit5 的 jar 包
第二步：创建测试类，使用注解完成
```java
// ExtendWith(SpringExtension.class)
// ContextConfiguration("classpath:bean.xml")
// 使用一个复合注解替代上面两个注解完成整合  
@SpringJUnitConfig(locations = "classpath:bean.xml")  
public class JTest5 {  
    @Autowired  
    private UserService userService;  
  
    @Test  
    public void test1() {  
        userService.accountMoney(100, 1, 2);  
    }  
}
```

# Spring5 框架新功能（Webflux）