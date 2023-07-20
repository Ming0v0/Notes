## 概念和原理
### 什么是 IOC
控制反转，把对象创建和对象之间的调用过程，交给 Spring 进行管理

> 当某个 Java 实例需要另一个 Java 实例时，传统的方法是由调用者创建被调用者的实例（例如，使用 new 关键字获得被调用者实例），而使用 Spring 框架后，被调用者的实例不再由调用者创建，而是由 Spring 容器创建，这称为控制反转。

Spring 容器在创建被调用者的实例时，会自动将调用者需要的对象实例注入给调用者，调用者通过 
Spring 容器获得被调用者实例，这称为依赖注入。


2. 使用 IOC 目的：为了耦合度降低
![](attachment/Pasted%20image%2020230224183409.png)
可以把 Spring IOC 容器看作是一个大工厂，Bean 相当于工厂的产品，如果希望这个大工厂生产和管理 Bean，则需要告诉容器需要哪些 Bean，以及需要哪种方式装配 Bean。


### IOC 底层原理
XMl 解析、工厂模式、反射

### 画图讲解 IOC 底层原理
![[attachment/Pasted image 20220904214331.png]]

## IOC（BeanFactory 接口）
IOC 思想基于 IOC 容器完成，IOC 容器底层就是对象工厂

Spring 提供 IOC 容器实现两种方式：（两个接口）
- BeanFactory：IOC 容器基本实现，是 Spring 内部的使用接口，不提供开发人员进行使用。**加载配置文件时候不会创建对象，在获取对象（使用）才去创建对象**

- ApplicationContext：BeanFactory 接口的子接口，提供更多更强大的功能，一般由开发人员进行使用。**加载配置文件时候就会把在配置文件对象进行创建**
 
ApplicationContext 接口有实现类
![[attachment/Pasted image 20220904214459.png]]
- ClassPathXMLApplicationContext是从类路径去加载要装载的配置
- FileSystemXMLApplicationContext是从文件路径去装载

