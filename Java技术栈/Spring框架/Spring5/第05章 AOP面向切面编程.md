# 概念
## 什么是 AOP
面向切面编程（方面），利用 AOP 可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

通俗描述：**不通过修改源代码方式，在主干功能里面添加新功能**

使用登录例子说明 AOP
![[attachment/Pasted image 20220907182022.png]]

## AOP（底层原理）
AOP 底层使用动态代理，有两种情况动态代理

- 第一种：有接口情况，使用 JDK 动态代理
创建接口实现类代理对象，增强类的方法
![[attachment/Pasted image 20220907182123.png]]

- 第二种：没有接口情况，使用 CGLIB 动态代理
创建子类的代理对象，增强类的方法
![[attachment/Pasted image 20220907182143.png]]

## 术语

| 术语   | 含义                                         |
| ------ | -------------------------------------------- |
| 连接点 | 类里面哪些方法可以被增强，这些方法称为连接点 |
| 切入点 | 实际被真正增强的方法，称为切入点             |
| 通知   | 实际增强的逻辑部分称为通知                   |
| 切面   | 把通知应用到切入点过程，是一个动作           |
| 目标   | 被通知的对象                                 |
| 代理   | 向目标对象应用通知之后创建的代理对象                                             |

通知有多种类型
- 前置通知：当方法增强之前执行
- 后置通知：当方法增强之后执行
- 环绕通知：当方法增强前后都执行
- 异常通知：当方法抛出异常执行
- 最终通知：类似finally，不管怎么都会执行

## 准备
Spring框架一般都是基于AspectJ实现AOP操作
什么是AspectJ
- AspectJ不是Spring组成部分，独立AOP框架，一般把AspectJ和Spring框架一起使用，进行AOP操作

## 切入点表达式
- 切入点表达式作用：知道对哪个类里面的哪个方法进行增强
- 语法结构：`execution([权限修饰符][返回类型][类全路径][方法名称][(参数列表)])`

权限修饰符可以省略，  *   表示全部

举例 1：对 dao.BookDao 类里面的 add方法进行增强
-   `execution(* dao.BookDao.add(..))`

举例 2：对dao.BookDao 类里面的所有的方法进行增强
-   `execution(* dao.BookDao.* (..))`

举例 3：对 dao 包里面所有类，类里面所有方法进行增强
-   `execution(* dao.*.* (..))`

# 基于AspectJ实现AOP操作
## AspectJ 注解
### 创建类，在类里面定义方法
```java
public class User {  
    public void add() {  
        System.out.println("add");  
    }  
}
```

### 创建增强类（编写增强逻辑）
在增强类里面，创建方法，让不同方法代表不同通知类型
```java
// 增强的类  
public class UserProxy {  
    // 前置通知 
    public void before() {  
        System.out.println("before");  
    }  
}
```

### 进行通知的配置
在 Spring 配置文件中，引入名称空间，开启注解扫描
```xml
<!--开启注解扫描-->  
<context:component-scan base-package="aopanno"/>
```

使用注解创建User和UserProxy对象
```java
@Component  
public class User {}
```

```java
@Component  
public class UserProxy {}
```

在增强类上面添加注解@Aspect
```java
@Aspect
@Component  
public class UserProxy {  
    // 前置通知 
    public void before() {  
        System.out.println("before");  
    }  
}
```

```xml
  
<!--开启Aspectj生成代理对象-->  
<aop:aspectj-autoproxy/>
```

### 配置不同类型的通知
在增强类的里面，在作为通知方法上面添加通知类型注解，使用切入点表达式配置
```java
//增强的类  
@Aspect//生成代理对象  
@Component  
public class UserProxy {  
  
    // 前置通知：在增强方法前执行  
    @Before(value = "execution(* aopanno.User.add(..))")  
    public void before() {  
        System.out.println("before");  
    }  
  
    // 最终通知  
    @After(value = "execution(* aopanno.User.add(..))")  
    public void after() {  
        System.out.println("after");  
    }  
  
    // AfterReturning只有正常返回才会执行，After无论正常异常都会执行  
    // 后置通知（返回通知）  
    @AfterReturning(value = "execution(* aopanno.User.add(..))")  
    public void afterReturning() {  
        System.out.println("afterReturning");  
    }  
  
    // 异常通知  
    @AfterThrowing(value = "execution(* aopanno.User.add(..))")  
    public void afterThrowing() {  
        System.out.println("afterThrowing");  
    }  
  
    // 环绕通知：在被增强方法前后都执行  
    @Around(value = "execution(* aopanno.User.add(..))")  
    public void around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {  
        System.out.println("环绕之前");  
  
        //被增强的方法执行  
        proceedingJoinPoint.proceed();  
  
        System.out.println("环绕之后");  
    }  
}
```

## 相同的切入点抽取
```java
//相同切入点抽取  
@Pointcut(value = "execution(* aopanno.User.add(..))")  
public void pointcut() {  
}  
  
//前置通知：在增强方法前执行  
@Before(value = "pointcut()")  
public void before() {  
    System.out.println("before");  
}
```

## 设置优先级
有多个增强类多同一个方法进行增强，设置增强类优先级
（1）在增强类上面添加注解 `@Order(数字类型值)`，数字类型值越小优先级越高
```java
@Component  
@Aspect  
@Order(1)  
public class PersonProxy {

    @Before(value = "execution(* aopanno.User.add(..))")    
    public void before() {  
        System.out.println("PersonProxy.....before");  
    }  
}
```

运行结果：
![[attachment/Pasted image 20220907183736.png]]

## 完全使用注解开发
创建配置类，不需要创建 xml 配置文件
```java
@Configurable  
@ComponentScan(basePackages = {"aopxml"})  
@EnableAspectJAutoProxy(proxyTargetClass = true)  
//@EnableAspectJAutoProxy 不写注解默认值是false，但是写了注解默认值是true（可以不写proxyTargetClass）  
public class ConfigAop {}
```

## AspectJ  XML配置文件
创建两个类，增强类和被增强类，创建方法
```java
@Component  
public class Book {  
    public void buy() {  
        System.out.println("buy");  
    }  
}
```

```java
@Aspect  
@Component  
public class BookProxy {  
    @Before(value = "execution(* aopxml.Book.buy(..))")  
    public void before() {  
        System.out.println("before");  
    }  
}
```

在 Spring 配置文件中创建两个类对象
```xml
<!--创建对象-->  
<bean id="bookProxy" class="aopxml.BookProxy"/>  
<bean id="book" class="aopxml.Book"/>
```

在 Spring 配置文件中配置切入点
```xml
<!--配置aop增强-->  
<aop:config>  
    <!--切入点：对那个类的那个方法进行增强-->  
    <!--id是对切入点取名字，expression写切入点表达式-->  
    <aop:pointcut id="pointcut" expression="execution(* aopxml.Book.buy(..))"/>  
  
    <!--配置切面：把增强或者通知应用到切入点的一个过程-->  
    <aop:aspect ref="bookProxy">  
        <!--method:增强的方法  
        pointcut-ref：切入点  
        -->  
        <aop:before method="before" pointcut-ref="pointcut"/>  
    </aop:aspect>  
</aop:config>
```

[下一页](第06章%20JdbcTemplate.md)