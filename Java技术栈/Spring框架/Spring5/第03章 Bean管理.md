# 什么是 Bean 管理？
Bean 管理指的是两个操作
- Spring 创建对象
- Spring 注入属性

Bean 管理操作有两种方式
- 基于 xml 配置文件方式实现
- 基于注解方式实现

# IOC 操作 Bean 管理（基于 XML 方式）
## 1. 基于 XML 方式创建对象
在`Spring`配置文件中，使用`bean`标签，标签里面添加对应属性，就可以实现对象创建
```xml
<!--配置User对象创建-->  
<bean id="user" class="Spring5.User"/>
```

在`bean`标签有很多属性，介绍常用的属性
| 属性  | 描述                                                                                                                          |
| ----- | ----------------------------------------------------------------------------------------------------------------------------- |
| id    | Bean 的唯一标识符，Spring 容器对 Bean 的配置和管理都通过该属性完成。id 的值必须以字母开始，可以使用字母、数字、下划线等符号。 |
| name  | name 属性中可以为 Bean 指定多个名称，每个名称之间用逗号或分号隔开。Spring 容器可以通过 name 属性配置和管理容器中的 Bean。     |
| class | 该属性指定了 Bean 的具体实现类，它必须是一个完整的类名，即类的全限定名。                                                      |
| scope | 用于设定 Bean 实例的作用域，属性值可以为 singleton（单例）、prototype（原型）、request、session 和 global Session。其默认值是 singleton                                                                                                                              |

创建对象，默认也是执行无参构造方法完成对象创建

## 2. 基于XML方式注入属性
DI：依赖注入，就是注入属性

### 第一种注入方式：使用 set 方法进行注入
在 `Spring` 实例化 `Bean` 的过程中，首先会**调用默认的构造方法实例化 `Bean` 对象，然后通过 Java 的反射机制调用 setXxx() 方法进行属性的注入**。
因此，setter 注入要求 Bean 的对应类必须满足以下两点要求。
-   必须提供一个默认的无参构造方法。
-   必须为需要注入的属性提供对应的 `setter` 方法。
```java
/**  
 * 演示使用set方法进行注入属性  
 */  
public class book {  
    //创建属性  
    private String bname;  
    private String bauthor;  
  
    //创建属性对应的set方法  
    public void setBname(String bname) {  
        this.bname = bname;  
    }  
  
    public void setBauthor(String bauthor) {  
        this.bauthor = bauthor;  
    }  
  
    public void testDemo() {  
        System.out.println(this.bauthor + " " + this.bname);  
    }  
}
```

在`Spring`配置文件配置对象创建，配置属性注入在 `<property>` 标签中，包含 name、ref、value 等属性。
-   name 用于指定参数名称
-   value 属性用于注入基本数据类型以及字符串类型的值
-   ref 属性用于注入已经定义好的 Bean

```xml
<!--2 set方法注入属性-->  
<bean id="book" class="Spring5.book">  
    <!--使用property完成属性注入  
        name:类里面属性名称  
        value：向属性注入的值  
    -->  
    <property name="bname" value="java编程思想"/>  
    <property name="bauthor" value="Bruce Eckel"/>  
</bean>
```

### 第二种注入方式：使用有参数构造器进行注入
创建类，定义属性，创建属性对应有参数构造方法
```java
/**  
 * 使用有参构造注入  
 */  
public class Orders {  
    //属性  
    private String oName;  
    private String address;  
  
    //有参构造  
    public Orders(String oName, String adress) {  
        this.oName = oName;  
        this.address = adress;  
    }  
  
    public void testDemo() {  
        System.out.println(this.oName + " " + this.address);  
    }  
}
```

在spring 配置文件中进行配置，在标签中，包含 `ref、value、type、index `等属性。
-  `value` 属性用于注入基本数据类型以及字符串类型的值
-  `ref` 属性用于注入已经定义好的 `Bean`
-  `type` 属性用来指定对应的构造函数
-  当构造函数有多个参数时，可以使用 index 属性指定参数的位置，index 属性值从 0 开始
```xml
<!--3 有参构造注入属性-->  
<bean id="orders" class="Spring5.Orders">  
    <constructor-arg name="oName" value="大话数据结构"/>  
    <constructor-arg name="address" value="China"/>  
</bean>
```

### 第三种注入方式：p名称空间注入
使用p名称空间注入，可以**简化基于XML配置方式**

添加p名称空间在配置文件中
```xml
<beans xmlns="http://www.springframework.org/schema/beans"  
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
       xmlns:p="http://www.springframework.org/schema/p"  
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
```

进行属性注入，在bean标签里面进行操作
```xml
<bean id="book" class="Spring5.book" p:bname="计算机网络" p:bauthor="谢希仁"/>
```

### XML 注入其他类型属性
字面量：null 值
```xml
<!-- null值 -->
<property name="bauthor">  
    <null/>  
</property>
```

属性值包含特殊符号
```xml
<!--属性值包含特殊符号  
    1. 把<>进行转义 &lt; &gt;        
    2. 把带特殊符号内容写到CDATA  
-->    
<property name="bname">  
    <value><![CDATA[<<java编程思想>>]]></value>  
</property>  
```

### 注入属性(外部 Bean 对象属性）
创建两个类 service 类和 dao 类，在 service 调用 dao 里面的方法
```java
public class UserService {  
    //创建 UserDao 类型属性，生成 set 方法  
    private UserDao userDao;  
  
    public void setUserDao(UserDao userDao) {  
        this.userDao = userDao;  
    }  
  
    public void add() {  
        userDao.update();  
    }  
}
```

在 `Spring` 配置文件中进行配置
```xml
<!--1 service 和 dao 对象创建-->  
<bean id="UserService" class="service.UserService">  
    <!--注入 userDao 对象  
        name 属性：类里面属性名称  
        ref 属性：创建userDao 对象 bean 标签 id 值  
    -->  
    <property name="userDao" ref="UserDao"/>  
</bean>  
<bean id="UserDao" class="dao.UserDaoimpl"/>
```

### 注入属性-内部 Bean
Java 中在类内部定义的类称为内部类，同理在 `Bean` 中定义的 `Bean` 称为内部 `Bean`。
注入内部 Bean 使用 `<property>` 和 `<constructor-arg>` 中的` <bean>` 标签。

**如下所示。**
-   一对多关系：部门和员工
-   一个部门有多个员工，一个员工属于一个部门 部门是一，员工是多
-   在实体类之间表示一对多关系，员工表示所属部门，使用对象类型属性进行表示
```java
/**  
 * 部门类  
 */  
public class Dept {  
    private String dname;  
  
    public void setDname(String dname) {  
        this.dname = dname;  
    }  
  
    @Override  
    public String toString() {  
        return "Dept{" +  
                "dname='" + dname + '\'' +  
                '}';  
    }  
}
```

```java
/**  
 * 员工类  
 */  
public class Emp {  
  
    private String ename;  
    private String gender;  
    //员工属于某一个部门，使用对象表示  
    private Dept dept;  
  
    public Dept getDept() {  
        return dept;  
    }  
  
    public void setDept(Dept dept) {  
        this.dept = dept;  
    }  
  
    public void setEname(String ename) {  
        this.ename = ename;  
    }  
  
    public void setGender(String gender) {  
        this.gender = gender;  
    }  
  
    public void testDemo() {  
        System.out.println(ename + " " + gender + " " + dept);  
    }  
}
```

（3）在spring配置文件进行配置
```xml
<bean id="emp" class="bean.Emp">  
    <property name="ename" value="小明"/>  
    <property name="gender" value="男"/>  
    <property name="dept">  
        <bean class="bean.Dept">  
            <property name="dname" value="研发部"/>  
        </bean>  
    </property>  
</bean>
```

内部 Bean 的定义不需要指定 id 和 name 。如果指定了，容器也不会将其作为区分 Bean 的标识符，反而会无视内部 Bean 的 scope 属性。所以内部 Bean 总是匿名的，而且总是随着外部 Bean 创建。

#### 注入属性-级联赋值
第一种写法
```xml
<!--级联赋值-->  
<bean id="emp" class="bean.Emp">  
    <!--设置两个普通属性-->  
    <property name="ename" value="小明"/>  
    <property name="gender" value="男"/>  
    <!--级联赋值-->  
    <property name="dept" ref="dept"/>   
</bean>  
<bean id="dept" class="bean.Dept">  
    <property name="dname" value="研发部"/>  
</bean>

```

第二种写法：一定要有get方法
```java
public Dept getDept() {  
    return dept;  
}
```

```xml
  <!--级联赋值-->  
<bean id="emp" class="bean.Emp">  
    <!--设置两个普通属性-->  
    <property name="ename" value="小明"/>  
    <property name="gender" value="男"/>  
    <!--级联赋值-->  
    <property name="dept" ref="dept"/>  
    <property name="dept.dname" value="算法部"/>  
</bean>  
<bean id="dept" class="bean.Dept">  
    <property name="dname" value="研发部"/>  
</bean>
```

### XML 注入集合属性
创建类，定义数组、list、map、set 类型属性，生成对应 set 方法
```java
public class Stu {  
    //1 数组类型属性  
    private String[] courses;  
    //2 list集合类型属性  
    private List<String> lists;  
    //3 map集合类型属性  
    private Map<String, String> maps;  
  
    private Set<String> set;  
  
    private List<Course> courseList;
}
```
1. 注入数组类型属性
2. 注入 List 集合类型属性
3. 注入 Map 集合类型属性
```xml
<!--1 集合类型属性注入-->  
<bean id="stu" class="collectiontype.Stu">

    <!--数组类型属性注入-->  
    <property name="courses">  
        <array>  
            <value>java课程</value>  
            <value>数据库课程</value>  
        </array>  
    </property>
      
    <!--list 类型属性注入-->  
    <property name="lists">  
        <list>  
            <value>小明</value>  
            <value>小红</value>  
        </list>  
    </property>  
    
    <!--map 类型属性注入-->  
    <property name="maps">  
        <map>  
            <entry key="JAVA" value="java"/>  
            <entry key="PHP" value="php"/>  
        </map>  
    </property> 
     
    <!--set 类型属性注入-->  
    <property name="set">  
        <set>  
            <value>MySQL</value>  
            <value>Redis</value>  
        </set>  
    </property>  
</bean>
```

| 标签     | 说明                                                     |
| -------- | -------------------------------------------------------- |
| \<list>  | 用于注入 list 类型的值，允许重复                         |
| \<set>   | 用于注入 set 类型的值，不允许重复                        |
| \<map>   | 用于注入 key-value 的集合，其中 key-value 可以是任意类型 |
| \<props> | 用于注入 key-value 的集合，其中 key-value 都是字符串类型                                                         |

**如果集合里是对象，如何在集合里面设置对象类型值**
```xml
<!--创建多个 course 对象-->  
<bean id="course1" class="collectiontype.Course">  
    <property name="cname" value="Spring5框架课程"/>  
</bean>  
<bean id="course2" class="collectiontype.Course">  
    <property name="cname" value="Java课程"/>  
</bean>
```

```xml
<!--注入 list 集合类型，值是对象-->  
<property name="courseList">  
    <list>  
        <ref bean="course1"/>  
        <ref bean="course2"/>  
    </list>  
</property>
```

**把集合注入部分提取出来**
在 spring 配置文件中引入名称空间 util
```xml
<beans xmlns="http://www.springframework.org/schema/beans"  
       xmlns:util="http://www.springframework.org/schema/util"  
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd  
        http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd">
```

使用 util 标签完成 list 集合注入提取
```xml
<!--1 提取list集合类型属性注入-->  
<util:list id="bookList">  
    <value>大话设计模式</value>  
    <value>大话数据结构</value>  
    <value>计算机组成原理</value>  
</util:list>  
  
<!--2 提取list集合类型属性注入-->  
<bean id="book" class="collectiontype.book" scope="prototype">  
    <property name="list" ref="bookList"/>  
</bean>
```

# FactoryBean
1. Spring有两种类型`bean`，一种普通`bean`，另一种工厂`bean`（`FactoryBean`）
2. 普通bean：在配置文件中定义bean类型就是返回类型
3. 工厂bean：在配置文件定义bean类型可以和返回类型不一样
-   第一步：创建类，让这个类作为工厂bean，实现接口FactoryBEan
-   第二步：实现接口里面的方法，在实现的方法中定义返回的bean类型
```java
import collectiontype.Course;  
import org.springframework.beans.factory.FactoryBean;  
  
  
public class MyBean implements FactoryBean<Course> {  
    @Override  
    public boolean isSingleton() {  
        return FactoryBean.super.isSingleton();  
    }  
  
    @Override  
    public Course getObject() throws Exception {  
        Course course = new Course();  
        course.setCname("abc");  
        return course;  
    }  
  
    @Override  
    public Class<?> getObjectType() {  
        return null;  
    }  
}
```

```xml
<bean id="mybean" class="factorybean.MyBean"/>
```

```java
@Test  
public void test3() {  
    ApplicationContext context = new ClassPathXmlApplicationContext("bean3.xml");  
    Course mybean = context.getBean("mybean", Course.class);  
    System.out.println(mybean);  
}
```
结果：`Course{cname='abc'}`

## Bean Factory与FactoryBean有什么区别？

相同点：都是用来创建bean对象的

不同点：使用BeanFactory创建对象的时候，必须要遵循严格的生命周期流程，太复杂了，**如果想要简单的自定义某个对象的创建，同时创建完成的对象想交给Spring来管理**，那么就需要实现FactoryBean接口了它的方法


- isSingleton：是否是单例对象
- getObjectType：获取返回对象的类型
- getObject：自定义创建对象的过程(new,反射,动态代理)

# Bean 生命周期
生命过程：从对象创建到对象销毁的过程

Bean生命周期
1. 通过构造器创建`Bean`实例（调用无参数构造）
2. 为`Bean`的属性值和对其他`Bean`引用（调用set方法）
3. 调用`Bean`的初始化方法（需要进行配置初始化方法）
4. `Bean`可以使用了
5. 当容器关闭时候，调用`Bean`销毁的方法（需要进行配置销毁的方法）

```java
public class Orders {  
    private String oname;  
  
    public void setOname(String oname) {  
        this.oname = oname;  
        System.out.println("第二步 调用 set 方法设置属性值");  
    }  
  
    public Orders() {  
        System.out.println("第一步 执行无参数构造创建 bean 实例");  
    }  
  
    //创建执行的初始化的方法  
    public void initMethod() {  
        System.out.println("第三步 执行初始化的方法");  
    }  
  
    //创建执行的销毁的方法  
    public void destroyMethod() {  
        System.out.println("第五步 执行销毁的方法");  
    }  
}
```
-   Init-method：设置初始化执行的方法
-   Destroy-method：设置销毁时执行的方法
```xml
<bean id="orders" class="bean.Orders"  
      init-method="initMethod"  
      destroy-method="destroyMethod"  
>  
    <property name="oname" value="手机"/>  
</bean>
```

```java
@Test  
public void test4() {  
    ApplicationContext context = new ClassPathXmlApplicationContext("bean4.xml");  
    Orders orders = context.getBean("orders", Orders.class);  
    System.out.println("第四步 获取创建 bean 实例对象");  
    System.out.println(orders);  
    ((ClassPathXmlApplicationContext) context).close();  
}
```

```txt
第一步 执行无参数构造创建 bean 实例
第二步 调用 set 方法设置属性值
第三步 执行初始化的方法
第四步 获取创建 bean 实例对象
bean.Orders@8458f04
第五步 执行销毁的方法
```

## Bean 的后置处理器
`Bean` 生命周期有七步
`BeanPostProcessor` 接口也被称为后置处理器，通过该接口可以自定义调用初始化前后执行的操作方法。

1.  通过构造器创建 Bean 实例（无参数构造）
2.  为 `Bean` 的属性设置值和对其他 `Bean` 引用（调用 set 方法）
3.  把 `Bean` 实例传递 `Bean` 后置处理器的方法 `postProcessBeforeInitialization`
4.  调用 `Bean` 的初始化的方法（需要进行配置初始化的方法）
5.  把 `Bean` 实例传递 `Bean` 后置处理器的方法 `postProcessAfterInitialization`
6.  `Bean` 可以使用了（对象获取到了）
7.  当容器关闭时候，调用 `Bean` 的销毁的方法（需要进行配置销毁的方法）

### 演示添加后置处理器效果
创建类，实现接口 `BeanPostProcessor`，创建后置处理器
```java
import org.springframework.beans.BeansException;  
import org.springframework.beans.factory.config.BeanPostProcessor;  
  
  
    public class MyBeanPost implements BeanPostProcessor {  
        @Override  
        public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {  
            System.out.println("在初始化之前执行的方法");  
            return bean;  
        }  
  
        @Override  
        public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {  
            System.out.println("在初始化之后执行的方法");  
            return bean;  
        }  
    }
```
- `postProcessBeforeInitialization` 在 `Bean` 实例化、依赖注入后，初始化前调用。
- `postProcessAfterInitialization` 在 `Bean` 实例化、依赖注入、初始化都完成后调用。

当需要添加多个后置处理器实现类时，**默认情况下 `Spring` 容器会根据后置处理器的定义顺序来依次调用（在配置文件配置先后顺序）**。也可以通过实现 `Ordered` 接口的 `getOrder` 方法指定后置处理器的执行顺序。该方法返回值为整数，默认值为 0，值越大优先级越低。

```xml
<!--配置后置处理器-->  
<bean id="myBeanPost" class="bean.MyBeanPost"/>
```

```txt
第一步 执行无参数构造创建 bean 实例
第二步 调用 set 方法设置属性值
在初始化之前执行的方法
第三步 执行初始化的方法
在初始化之后执行的方法
第四步 获取创建 bean 实例对象
bean.Orders@8458f04
第五步 执行销毁的方法
```
由运行结果可以看出，`postProcessBeforeInitialization` 方法是在 `Bean` 实例化和依赖注入后，自定义初始化方法前执行的。而 `postProcessAfterInitialization` 方法是在自定义初始化方法后执行的。

# Bean作用域
在 Spring 里面，设置创建 Bean 实例是单实例还是多实例？
```java
@Test  
public void testCollection2() {  
    ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");  
    book book1 = context.getBean("book", book.class);  
    book book2 = context.getBean("book", book.class);  
    System.out.println(book1);  
    System.out.println(book2);  
}
```

默认情况下，Bean 是单实例对象
```txt
collectiontype.book@2de56eb2
collectiontype.book@2de56eb2
```

## 如何设置单实例还是多实例
（1）在Spring配置文件bean标签里面有属性（`scope`）用于设置单实例还是多实例
（2）`scope`属性值：
-   第一个：`singleton` 表示是单实例对象（默认值）
-   第二个：`prototype`，表示多实例对象
```xml
<bean id="book" class="collectiontype.book" scope="prototype">  
    <property name="list" ref="bookList"/>  
</bean>
```
``
```txt
collectiontype.book@2de56eb2
collectiontype.book@5f8e8a9d
```

## singleton 和 prototype 区别
- `singleton` 单实例，`prototype` 多实例
- 设置 `scope` 值是
	-   `singleton` 时候，加载 `Spring`配置文件时候就会创建单实例对象
	-   `prototype` 时候，不是在加载 `Spring` 配置文件的时候创建对象，而是在调用`getBean`方法的时候创建多实例对象
	
- Spring 根据 `Bean` 的作用域来选择管理方式。对于 `singleton` 作用域的 `Bean`，Spring 能够精确地知道该 `Bean` 何时被创建，何时初始化完成，以及何时被销毁；而对于 `prototype` 作用域的 `Bean`，Spring 只负责创建，当容器创建了 `Bean` 的实例后，`Bean` 的实例就交给客户端代码管理，Spring    容器将不再跟踪其生命周期。

# Bean继承
Bean 定义可以包含很多配置信息，包括构造函数参数、属性值和容器的一些具体信息，如初始化方法、销毁方法等。子 Bean 可以继承父 Bean 的配置数据，根据需要，子 Bean 可以重写值或添加其它值。

需要注意的是，Spring Bean 定义的继承与 Java 中的继承无关。您可以将父 Bean 的定义作为一个模板，其它子 Bean 从父 Bean 中继承所需的配置。

在配置文件中通过 parent 属性来指定继承的父 Bean。

在配置文件中，分别为 HelloWorld 中的 message1 和 message2 赋值。
```java
public class HelloWorld{
	private String message1;
	private String message2;

	public void setMessage1(String message){
		this.message1=message;
	}

	public void setMessage2(String message){
		this.message2=message;
	}

	public void getMessage1(){
		System.out.println("World Message1："+message1);
	}
	
	public void getMessage2(){
		System.out.println("World Message2："+message2);
	}
}
```

```java
public class HelloChina{
	private String message1;
	private String message2;
	private String message3;

	public void setMessage1(String message){
		this.message1=message;
	}

	public void setMessage2(String message){
		this.message2=message;
	}
	
	public void setMessage3(String message){
		this.message3=message;
	}

	public void getMessage1(){
		System.out.println("World Message1："+message1);
	}
	
	public void getMessage2(){
		System.out.println("World Message2："+message2);
	}
	
	public void getMessage3(){
		System.out.println("World Message3："+message3);
	}
}
```

使用 parent 属性将 HelloChain 定义为 HelloWorld 的子类，并为 HelloChain 中的 message1 和 message3 赋值。

Beans.xml 文件代码如下。
```xml
<bean id="helloWorld" class="bean.HelloWorld">
	<property name="message1" value="Hello World1!"/>
	<property name="message2" value="Hello World2!"/>
</bean>

<bean id="helloChina" class="bean.HelloChina" parent="helloWorld">
	<property name="message1" value="Hello China1!"/>
	<property name="message2" value="Hello China2!"/>
</bean>
```

```java
HelloWorld objA=(HelloWorld)context.getBean("helloWorld");
objA.getMessage1();
objA.getMessage2();

HelloChina objB=(HelloChina)context.getBean("helloChina");
objB.getMessage1();
objB.getMessage2();
objB.getMessage3();
```

结果：
![[attachment/Pasted image 20220907132254.png]]

 由结果可以看出，我们在创建 helloChina 时并没有给 message2 赋值，但是由于 Bean 的继承，将值传递给了 message2。

## Bean定义模板
您可以创建一个 Bean 定义模板，**该模板只能被继承，不能被实例化**。创建 Bean 定义模板时，不用指定 class 属性，而是指定 abstarct="true" 将该 Bean 定义为抽象 Bean，如下所示。
```xml
<bean id="beanTeamplate" class="bean.HelloWorld"  abstarct="true">
	<property name="message1" value="Hello World1!"/>
	<property name="message2" value="Hello World2!"/>
</bean>

<bean id="helloChina" class="bean.HelloChina" parent="helloWorld">
	<property name="message1" value="Hello China1!"/>
	<property name="message2" value="Hello China2!"/>
</bean>
```

# 自动装配
## 什么是自动装配
根据指定装配规则（属性名称或者属性类型），Spring 自动将匹配的属性值进行注入

自动装配就是指 Spring 容器在不使用 `<constructor-arg>` 和`<property>` 标签的情况下，可以自动装配（autowire）相互协作的 Bean 之间的关联关系，将一个 Bean 注入其他 Bean 的 Property 中。

## 演示自动装配过程
根据属性名称自动注入
```xml
<!--实现自动装配  
bean 标签属性autowire，配置自动装配  
autowire 属性常用两个值：  
    byName 根据属性名称注入 ，注入值 bean 的 id 值和类属性名称一样  
    byType 根据属性类型注入  
-->  
<bean id="emp" class="autowire.Emp" autowire="byName"/>  
<bean id="dept" class="autowire.Dept"/>
```

根据属性类型自动注入
```xml
<!--实现自动装配  
bean 标签属性autowire，配置自动装配  
autowire 属性常用两个值：  
    byName 根据属性名称注入 ，注入值 bean 的 id 值和类属性名称一样  
    byType 根据属性类型注入  
-->  
<bean id="emp" class="autowire.Emp" autowire="byType"/>  
<bean id="dept" class="autowire.Dept"/>
```

自动装配的优缺点
优点：自动装配只需要较少的代码就可以实现依赖注入。
缺点
- 不能自动装配简单数据类型，比如 int、boolean、String 等。
- 相比较显示装配，自动装配不受程序员控制。

## 外部属性文件：如数据库配置文件
### 直接配置数据库信息
- 配置德鲁伊连接池
- 引入德鲁伊连接池依赖 jar 包

```xml
<!-- 直接配置连接池 -->  
<bean id="druidDataSource" class="com.alibaba.druid.pool.DruidDataSource">  
    <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>  
    <property name="url" value="jdbc:mysql://localhost:3306/userDb"></property>  
    <property name="username" value="root"/>
    <property name="password" value="123456"/>
</bean>
```

### 引入外部属性文件配置数据库连接池
创建外部属性文件，`properties` 格式文件，写数据库信息
```properties
url=jdbc:mysql:///test  
username=root  
password=123456  
driverClassName=com.mysql.cj.jdbc.Driver  
initialSize=10  
maxActive=10
```

把外部 properties 属性文件引入到 Spring 配置文件中
引入 context 名称空间
```xml
<beans xmlns="http://www.springframework.org/schema/beans"  
       xmlns:context="http://www.springframework.org/schema/context"  
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd  
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
```

在 Spring 配置文件使用标签引入外部属性文件
```xml
<!--引入外部属性文件-->  
<context:property-placeholder location="jdbc.properties"/>  

<!--配置连接池-->  
<bean id="druidDataSource" class="com.alibaba.druid.pool.DruidDataSource">  
    <property name="driverClassName" value="${driverClassName}"/>  
    <property name="url" value="${url}"/>  
    <property name="username" value="${username}"/>  
    <property name="password" value="${password}"/>  
</bean>
```

# IOC 操作 Bean 管理（基于注解方式）
### 什么是注解
1.  注解是代码特殊标记，格式：`@注解名称(属性名称=属性值, 属性名称=属性值..)`
2.  使用注解，注解作用在类上面，方法上面，属性上面
3.  使用注解目的：简化 XML 配置

**Spring 针对 Bean 管理中创建对象提供注解**
| 标签        | 描述                            |
| ----------- | ------------------------------- |
| @Component  | 通用                            |
| @Service    | 一般用在业务逻辑层或service层上 |
| @Controller | 一般用在web层上                 |
| @Repository | 一般用在dao层上                 |
上面四个注解功能是一样的，都可以用来创建Bean实例

### 基于注解方式实现对象创建
第一步：引入依赖
第二步：开启组件扫描
```xml
<!--开启组件扫描  
    1 如果扫描多个包，多个包使用逗号隔开  
    2 扫描包上层目录  
-->  
<context:component-scan base-package="testdemo,Dao"/>
```

第三步：创建类，在类上面添加创建对象注解。
当属性值value不写时，对象名是默认是开头小写，其他不变
```java
// 在注解里面 value 属性值可以省略不写，  
// 默认值是类名称，首字母小写  
// UserService -- userService 

@Component(value = "userService")// <bean id="userService" class=".."/>  
public class UserService {  
  
    public void add() {  
        System.out.println("service add");  
    }  
}
```

### 开启组件扫描细节配置
```xml
<!--示例 1    
	use-default-filters="false" 
	表示现在不使用默认filter，自己配置 filter    
	context:include-filter ，设置扫描哪些内容  
-->  
<context:component-scan base-package="testdemo" use-default-filters="false">  
    <context:include-filter type="annotation"  
                            expression="org.springframework.stereotype.Controller"/>  
</context:component-scan>  
  
<!--示例 2    
	下面配置扫描包所有内容  
    context:exclude-filter： 设置哪些内容不进行扫描  
-->  
<context:component-scan base-package="testdemo">  
    <context:exclude-filter type="annotation"  
                            expression="org.springframework.stereotype.Controller"/>  
</context:component-scan>
```

## 基于注解方式实现属性注入
### @Autowired
**根据属性类型进行自动装配**

把 service 和 dao 对象创建，在 service 和 dao 类添加创建对象注解
```java
@Component  
public class UserDaoImpl implements UserDao {  
    @Override  
    public void add() {  
        System.out.println("hello");  
    }  
}
```
```java
@Service  
public class UserService {  
   
   private String name;  
  
    public void add() {  
        System.out.println("service add");  
    }  
}
```

在 service 注入 dao 对象，在 service 类添加 dao 类型属性，在属性上面使用注解
```java
@Service  
public class UserService {  
  
   private String name;  
  
    // 定义 dao 类型属性  
    // 不需要添加 set 方法  
    // 添加注入属性注解  
    @Autowired // 根据类型注入  
    public UserDao userDao;  
  
    public void add() {  
        System.out.println("service add");  
        userDao.add();  
    }  
}
```

### @Qualifier
**根据名称进行注入**
这个@Qualifier 注解的使用，和上面@Autowired 搭配使用
```java
@Service  
public class UserService {  
 
   private String name;  
  
    // 定义 dao 类型属性  
    // 不需要添加 set 方法  
    // 添加注入属性注解  
    @Autowired 
    @Qualifier(value = "userDaoImpl")// 根据名称进行注入  
    public UserDao userDao;  
  
    public void add() {  
        System.out.println("service add");  
        userDao.add();  
    }  
}
```

### @Resource
可以根据类型注入，可以根据名称注入（@Resource不是Spring自带的，不推荐使用）
```java
@Service  
public class UserService {  
   
   private String name;  
  
    // 定义 dao 类型属性  
    // 不需要添加 set 方法  
    // 添加注入属性注解  
	// @Resource //根据类型进行注入  
	@Resource(name = "userDaoImpl")// 根据名称进行注入 
    public UserDao userDao;  
  
    public void add() {  
        System.out.println("service add");  
        userDao.add();  
    }  
}
```

```java
@Service  
public class UserService {  
  
	@Value(value = "abc")  
   private String name;  
  
    //定义 dao 类型属性  
    //不需要添加 set 方法  
    //添加注入属性注解  
	@Autowired //根据类型注入  
    public UserDao userDao;  
  
    public void add() {  
        System.out.println("service add");  
        userDao.add();  
    }  
}
```

## 完全注解开发
创建配置类，替代xml 配置文件
```java
import org.springframework.context.annotation.ComponentScan;  
import org.springframework.context.annotation.Configuration;  
  
    @Configuration//作为配置类，替代 xml 配置文件  
    @ComponentScan(basePackages = {"testdemo"})  
    @ComponentScan(basePackages = {"Dao"})  
    public class SpringConfig {  
    }
```

[下一页](第05章%20AOP面向切面编程.md)