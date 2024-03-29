## UML概述

统一建模语言(UML)是面向对象软件的标准化建模语言。UML由3个要素构成，即UML的基本构造块、支配这些构造块如何放置在一起的规则和运用于整个语言的一些公共机制

UML的词汇表包含3种构造块，即事物、关系和图
- 事物是对模型中最具代表性的成分的抽象
- 关系把事物结合在一起
- 图聚集了相关的事物

## 事物

事物包括结构事物、行为事物、分组事物和注释事物。

结构事物(Structural Thing)。结构事物是UML模型中的名词。它们通常是模型的静态部分，**描述概念或物理元素**。

结构事物包括类(Class)、接口(Interface)、协作(Collaboration)、用例(Use Case)、主动类(Active Class)、构件(Component)和节点(Node)

![[attachment/Pasted image 20220509180119.png]]

**类（Class）**

类是具有相同属性、相同方法、相同语义和相同关系的一组对象的集合。**在UML图中，类用包括类名、属性和方法的矩形来表示。**

---

**接口（Interface）**

接口是指类或组件所提供的、可以完成特定功能的一组操作的集合。**接口描述了一个类或构件的一个服务的操作集。** 接口仅仅是定义了一组操作的规范，并没有给出这组操作的具体实现。也就是说，接口描述了类或组件对外的、可视化的动作。通常，一个类实现一个或多个接口。在UML中，接口通常用一个圆形来表示。

---

**协作（Collaboration）**

协作定义了交互的操作，表示一些角色和其他元素一起工作，提供一些合作的动作。在UML图中，协作通常用一个虚线椭圆来表示。

---

**用例（Use Case）**

用例定义系统执行的一组操作，对特定的用户产生可以观察的结果。在UML图中，用例通常用一个实现椭圆来表示。

---

**活动类（ActiveClass）**

活动类是指类对象有一个或多个线程或进程的类。活动类和类相似，只是它的对象代表的元素的行为和其他的元素同时存在。在UML图中，活动类的表示方法和普通类的表示方法相似，也是使用一个矩形，只是最外面的边框使用粗线。

---

**组件（Component）**

组件是物理上可替换的，实现了一个或多个接口的系统元素。在UML图中组件的表示方法比较复杂。

---

**行为事物(Behavior Thing)** 

行为事物是UML模型的动态部分。它们是模型中的动词，描述了跨越时间和空间的行为。共有两类主要的行为事物，即交互(Interaction)和状态机(State Machine)。
![[attachment/Pasted image 20220509180146.png]]

---

**分组事物(Grouping Thing)**

分组事物是UML模型的组成部分。它们是一些由模型分解成的“盒子”。在所有的分组事物中，最主要的分组事物是包(Package)。
![[attachment/Pasted image 20220509180226.png]]

---

**注释事物(Annotational Thing)**

注释事物是UML模型的解释部分。这些注释事物用来描述、说明和标注模型的任何元素。注解(Note)是一种主要的注释事物。注解是一个依附于一个元素或者一组元素之上，并对它进行约束或解释的简单符号。
![[attachment/Pasted image 20220509180233.png]]

## 关系

UML中有4种关系，即依赖、关联、泛化和实现。

### 依赖

依赖(Dependency)。依赖是两个事物间的语义关系，其中一个事物（独立事物）发生变化会影响另一个事物（依赖事物）的语义。
![[attachment/Pasted image 20220509180304.png]]
如果对象A用到对象B，但是和B的关系不是太明显的时候，就可以把这种关系看作是依赖关系。如果对象A依赖于对象B，则 `A use a B`。比如驾驶员和汽车的关系，驾驶员使用汽车，二者之间就是依赖关系。

依赖关系在Java中的具体代码表现形式为**B为A的构造器**或**方法中的局部变量**、**方法或构造器的参数**、**方法的返回值**，或者**A调用B的静态方法**。

假设我们有两个类：`Order`（订单）和`PaymentProcessor`（支付处理器）。`Order` 类依赖于 `PaymentProcessor` 类来完成支付操作。

定义 `PaymentProcessor` 类：
```java
public class PaymentProcessor {
    public void processPayment(double amount) {
        // 执行支付处理逻辑
        System.out.println("Payment processed for amount: " + amount);
    }
}
```

定义 `Order` 类，它在某些情况下需要使用 `PaymentProcessor` 来处理支付：
```java
public class Order {
    private double totalAmount;
    private PaymentProcessor paymentProcessor;

    public Order(double totalAmount, PaymentProcessor paymentProcessor) {
        this.totalAmount = totalAmount;
        this.paymentProcessor = paymentProcessor;
    }

    public void checkout() {
        // 在这里，Order 类使用了 PaymentProcessor 来完成支付
        paymentProcessor.processPayment(totalAmount);
    }
}
```

在上述示例中，`Order` 类通过构造函数接受一个 `PaymentProcessor` 对象，并在 `checkout` 方法中调用了 `PaymentProcessor` 的 `processPayment` 方法来完成支付操作。这种关系表示 `Order` 类依赖于 `PaymentProcessor` 类来执行支付操作。

### 关联

关联(Association)。关联是一种结构关系，它描述了一组链，链是对象之间的连接（对象和对象之间的连接）。它使一个对象知道另一个对象的属性和方法。

在Java中，关联关系的代码表现形式为**一个对象含有另一个对象的引用**。也就是说，如果一个对象的类代码中，包含有另一个对象的引用，那么这两个对象之间就是关联关系。

>关联关系有单向关联和双向关联。如果两个对象都知道（即可以调用）对方的公共属性和操作，那么二者就是双向关联。如果只有一个对象知道（即可以调用）另一个对象的公共属性和操作，那么就是单向关联。大多数关联都是单向关联，单向关联关系更容易建立和维护，有助于寻找可重用的类。


在UML图中，<u>双向关联关系用带双箭头的实线或者无箭头的实线双线表示。</u>单向关联用一个带箭头的实线表示，箭头指向被关联的对象。一个对象可以持有其它对象的数组或者集合。

在UML中，通过放置多重性（multipicity）表达式在关联线的末端来表示。多重性表达式可以是一个数字、一段范围或者是它们的组合。

多重性允许的表达式示例如下
-   数字：精确的数量
-   `*`或者`0..*`：表示0到多个
-   `0..1`：表示0或者1个，在Java中经常用一个空引用来实现
-   `1..*`：表示1到多个
![[attachment/Pasted image 20220509180326.png]]
```java
// 单向一对一关系
public class Person { 
	private IDCard card; 
} 
public class IDCard{
	// 功能
} 
// 双向一对一关系
public class Person {
	private IDCard card; 
} 
public class IDCard{ 
	private Person person 
}
```

<font color='#6950a1'>关联关系又分为聚合关联和组合关联两种类型。</font>

聚合（Aggregation）是整体与部分的关系, 且**部分可以离开整体而单独存在**。 如：一台电脑由键盘(keyboard)、显示器(monitor)，鼠标等组成；组成电脑的各个配件是可以从电脑上分离出来的。

聚合关系是关联关系的一种，是强的关联关系；关联和聚合在语法上无法区分，必须考察具体的逻辑关系。

```java
public class Computer{
	private Mouse mouse;
	private Mnitor monitor;

	public void setMouse(Mouse mouse){
		this.mouse=mouse;
	}
	public void setMonitor(Monitor monitor){
		this.mouse=mouse;
	}
}
```

<font color='#007d65'>在UML图中，聚合关系用空心菱形加实线箭头表示，空心菱形在整体一方，箭头指向部分一方</font>

![聚合关系](attachment/聚合关系.md)

---

组合（Composition）。是整体与部分的关系, 但**部分不能离开整体而单独存在。** 如公司和部门是整体和部分的关系, 没有公司就不存在部门。

```java
public void Company{
	private Department department = new department();
}
```

<font color='#007d65'>在UML图中，组合关系用实心菱形加实线箭头表示，实心菱形在整体一方，箭头指向部分一方</font>
![[attachment/组合关系]]

组合关系是关联关系的一种，是比聚合关系还要强的关系，它要求普通的聚合关系中代表整体的对象负责代表部分的对象的生命周期

### 泛化

泛化(Generalization)。泛化是一种**特殊与一般**（对象与对象之间的继承）关系，特殊元素(子元素)的对象可替代一般元素(父元素)的对象。用这种方法子元素共享了父元素的结构和行为。
即*子类继承父类，父类泛化子类*。

<font color='#007d65'>在UML类图中，泛化关系用空心三角和实线组成的箭头表示，从子类指向父类</font>
![[attachment/Pasted image 20220509180358.png]]
### 实现

实现(Realization)。实现是类元（接口及其实现类）之间的语义关系，其中一个类元指定了由另一个类元保证执行的契约。

<u>在UML类图中，实现关系用空心三角和虚线组成的箭头来表示，从实现类指向接口</u>，在两种地方要遇到实现关系：一种是在接口和实现它们的类或构件之间;另一种是在用例和实现它们的协作之间。
![[attachment/Pasted image 20220509180411.png]]

## UML中的图

UML提供的图包括类图、对象图、用例图、交互图、状态图、活动图、构件图和部署图。

### 类图

类图(Class Diagram)展现了<font color="#c63c26">一组对象、接口、协作及其关系</font>。类图给出系统的*静态设计视图。* 包含主动类的类图给出了系统的静态进程视图

**类（Class）**：类是类图的核心元素，用于表示系统中的对象或类。类通常包括类名、属性和方法。
- **类名**：类名位于顶部，通常以粗体显示。
- **属性**：属性是类的特征或数据成员，通常以名称和类型表示。
	- `+` : public 公有的
	- `-`  : private 私有的
	- `#` : protected 受保护的
	- `~` : package 包的
- **方法**：方法是类的行为或函数成员，通常以名称、参数列表和返回类型表示。

**关系（Relationships）**：关系用于表示不同类之间的连接或关联。以下是一些常见的关系类型
- **关联关系（Association）**：表示两个类之间的关联。关联可以具有角色、多重性和导航性。
 - **聚合关系（Aggregation）**：表示"整体-部分"的关系，其中一个类包含其他类的一部分。聚合关系通常使用一个菱形表示。
- **组合关系（Composition）**：与聚合关系类似，但更强，表示一个类包含其他类的整体部分，生命周期受控制。组合关系通常使用实心菱形表示。
- **继承关系（Inheritance）**：表示类之间的继承关系，其中一个类（子类或派生类）继承另一个类（父类或基类）的属性和方法。继承关系通常使用一个带空心箭头的实线表示

**接口（Interface）**：接口表示类必须实现的方法集合，通常以斜体字体表示，并在上方带有 "`<<interface>>`" 标记。

**抽象类（Abstract Class）**：抽象类是不能被实例化的类，通常用斜体字体表示，并在上方带有 "{abstract}" 标记。

**依赖关系（Dependency）**：表示一个类依赖于另一个类以执行某些操作，通常用虚线箭头表示。


![[attachment/Pasted image 20220513225821.png]]

### 对象图

对象图(Object Diagram)展现了一组对象及其关系。对象图描述了在类图中所建立的事物的实例的静态快照。对象图一般包括对象和链。

![[attachment/Pasted image 20220522161901.png]]

和类图一样，对象图给出**系统的静态设计视图或静态进程视图**，但它们是从真实的或原型实例的角度建立的。这种视图主要支持系统的功能需求，即系统应该提供给最终用户的服务。利用对象图可以对静态数据结构建模。

在对系统的静态设计视图或静态进程视图建模时，主要是使用对象图对对象结构进行建模。对象结构建模涉及在给定时刻抓取系统中对象的快照。对象图表示了交互图表示的动态场景的一个静态画面，可以使用对象图可视化、详述、构造和文档化系统中存在的实例以及它们之间的相互关系。

### 用例图

用例图(Use Case Diagram)展现了一组用例、参与者(Actor)及其关系。描述人们希望如何使用一个系统，最常用来描述系统以及子系统。

用例图描述了一组用例、参与者以及它们之间的关系。包括以下3方面的内容
- 参与者（Actor）
- 用例（Use Case）
- 参与者、用例之间的关系，泛化关系、包含（`<<include>>`）关系、扩展（`<<extend>>`）关系等。

![[attachment/Pasted image 20220522162216.png]]


包含关系（`include`) ：`include`为包含关系

- 当两个或多个用例中共用一组相同的动作，这时可以将这组相同的动作抽出来作为一个独立的子用例，供多个基用例所共享。
- 因为子用例被抽出，基用例并非一个完整的用例，所以include关系中的基用例必须和子用例一起使用才够完整,子用例也必然被执行。即**当基本用例执行时，被包含用例一定会执行。**
- include关系在用例图中使用<u>带箭头的虚线</u>表示（在线上标注`<<include>>`），箭头从**基用例指向子用例。** 只有包含关系是基本用例指向用例。


举例说明：用户在账号登录过程中，包括输入账号、输入密码、确认登录等操作

![[attachment/Pasted image 20220518170937.png]]




扩展关系（extend)： extend关系是对基用例的扩展

- 基用例是一个完整的用例,即使没有子用例的参与，也可以完成一个完整的功能。
- extend的基用例中将存在一个扩展点只有当扩展点被激活时，子用例才会被执行。即：**一个用例执行的时候，可能会出现<font color="red">特殊情况或可选情况</font>，这个时候就会执行扩展用例。**
- extend关系在用例图中使用<u>带箭头的虚线</u>表示（在线上标注`<<extend>>`)，箭头**从子用例指向基用例。**

举例说明：用户在登录过程中忘记了密码
![[attachment/Pasted image 20220518171049.png]]



泛化关系（generalization）︰泛化关系是一种继承关系

- 子用例将继承基用例的所有行为，关系和通信关系，也就是说在任何**使用基用例的地方都可以用子用例来代替。**
- 泛化关系在用例图中使用<u>空心的箭头</u>表示，箭头方向**从子用例指向基用例。**

举例说明：VIP会员和普通用户，归纳为用户；账号登录与微信登录，也可归纳为登录系统。
![[attachment/Pasted image 20220518170804.png]]


### 交互图

序列图、通信图、交互概览图和时序图均被称为交互图，它们用于对系统的动态方面进行建模。
- 序列图是强调消息**时间顺序**的交互图
- 通信图是强调接收和发送消息的**对象的结构组织**的交互图
- 交互概览图强调控制流的交互图。

#### 序列图
序列图(Sequence Diagram）是场景(Scenario）的图形化表示，描述了<font color ='red'>以时间顺序组织的对象之间的交互活动</font>。

如图所示，形成序列图时，首先把<u>参加交互的对象放在图的上方，沿水平方向排列。</u>**通常把发起交互的对象放在左边，下级对象依次放在右边。然后，把这些对象发送和接收的消息沿垂直方向按时间顺序从上到下放置。** 这样，就提供了控制流随时间推移的清晰的可视化轨迹。
![[attachment/Pasted image 20220522162715.png]]
序列图有两个不同于通信图的特性
**序列图有对象生命线。** 
<u>对象生命线是一条垂直的虚线，表示一个对象在一段时间内存在。</u> 在交互图中出现的大多数对象存在于整个交互过程中，所以这些对象全都排列在图的顶部，其生命线从图的顶部画到图的底部。但对象也可以在交互过程中创建，它们的生命线从接收到构造型为create的消息时开始。对象也可以在交互过程中撤销，它们的生命线在接收到构造型为destroy的消息时结束（并且给出一个大X的标记表明生命的结束)。

**序列图有控制焦点。** 
<u>控制焦点是一个瘦高的矩形，表示一个对象执行一个动作所经历的时间段，既可以是直接执行，也可以是通过下级过程执行。</u> 矩形的顶部表示动作的开始，底部表示动作的结束（可以由一个返回消息来标记)。还可以通过将另一个控制焦点放在它的父控制焦点的右边来显示（由循环、自身操作调用或从另一个对象的回调所引起的）控制焦点的嵌套（其嵌套深度可以任意)。如果想特别精确地表示控制焦点在哪里，也可以在对象的方法被实际执行（并且控制还没传给另一个对象)期间将那段矩形区域阴影化。

**消息**
消息（Messages）是对象间的一种通信机制。消息由发送对象向另一个或其他几个接收对象发送信号，或由一个对象（发送者或调用者）调用另一个对象（接收者）的操作。
<u>消息/方法名字放置在带箭头的线上面。正在被传递给接收对象的消息，表示接收对象的类实现的一个操作/方法。</u>
在UML中消息分为5类：
- 递归调用：实心实线箭头
- 普通（同步）操作：实心实线箭头
- 返回消息：虚线箭头
- [异步调用](https://baike.baidu.com/item/%E5%BC%82%E6%AD%A5%E8%B0%83%E7%94%A8)的消息：实线箭头
- 过程调用的消息
在例子中，客户对象c调用Transaction类的一个实例的匿名对象。c对象在调用匿名对象的 setAction 方法。匿名对象然后调用p对象上的、包括参数3,4的setValues方法。

#### 通信图
通信图(Communication Diagram）**强调收发消息的对象的结构组织**，在早期的版本中也被称作**协作图**。

通信图强调参加交互的对象的组织。产生一张通信图，如图所示，首先要将参加交互的对象作为图的顶点，然后把连接这些对象的链表示为图的弧，最后用对象发送和接收的消息来修饰这些链。这就提供了在协作对象的结构组织的语境中观察控制流的一个清晰的可视化轨迹。
![[attachment/Pasted image 20220522170038.png]]
通信图有两个不同于序列图的特性
**通信图有路径。** 为了指出一个对象如何与另一个对象链接，可以在链的末端附上一个路径构造型（如构造型（local），表示指定对象对发送者而言是局部的）。通常只需要显式地表示以下几种链的路径: local（局部)、parameter(参数)，但arameter 、global(全局）以及self（自身)，但不必表示 association（关联)。

**通信图有顺序号。** 为表示一个消息的时间顺序，可以给消息加一个数字前缀（从1号消息开始)，在控制流中，每个新消息的顺序号单调增加（如2、3等)。为了显示嵌套，可使用带小数点的号码（1表示第一个消息；1.1表示嵌套在消息1中的第一个消息，1.2表示嵌套在消息1中的第二个消息，等等)。嵌套可为任意深度。还要注意的是，沿同一个链可以显示许多消息（可能发自不同的方向)，并且每个消息都有唯一的一个顺序号。

序列图和通信图是同构的，它们之间可以相互转换。

### 状态图
状态图（State Diagram）展现了一个状态机，它由状态、转换、事件和活动组成。**状态图关注系统的动态视图，它对于接口、类和协作的行为建模尤为重要，强调对象行为的事件顺序。**

状态图通常包括简单状态和组合状态、转换、事件和动作

- **状态**是指对象的生命周期中某个条件或者状态，在此期间对象将满足某些条件、执行某些活动或等待某些事件，是对象执行了一系列活动的结果，当某个事件发生后，对象的状态将发生变化。 
- **转换**是两个状态之间的一种关系，表示对象将在源状态中执行定的动作，并在某个特定事件发生而且某个特定的警戒（监护）条件满足时进入目标状态。
- **事件**是发生在时间和空间上的对状态机来讲有意义的那些事情。事件通常会引起状态的变迁，促使状态机从一种状态切换到另一种状态，如信号、对象额度创建和销毁等。
- **活动**：活动指的是状态机中进行的非原子操作。
- **动作**：动作指的是状态机中可以执行的哪些原子操作。所谓原子操作，指的是他们在运行的过程中不能被其他消息中断，必须一直执行下去，以至最终导致状态的变更或者返回一个值。

在UML中，状态图由表示状态的节点和表示状态之间转换的带箭头的直线组成。

状态的转换由事件触发，状态和状态之间由转换箭头连接。每一个状态图都有一个初始状态（实心圆），用来表示状态机的开始。还有一个中止状态（半实心圆），用来表示状态机的终止。

状态图主要由元素状态、转换、初始状态、中止状态和判定等组成
![[attachment/Pasted image 20220522172400.png]]

**状态**：状态用于对实体在其生命周期中的各种状况进行建模，一个实体总是在有限的一段时间内保持一个状态。状态由一个带圆角的矩形表示，状态的描绘素应该包括名称、入口和出口动作、内部转换和嵌套状态。如下图，为一个简单状态
![[attachment/Pasted image 20220522172502.png]]
状态名指的是状态的名字，通常用字符串表示，其中每个单词的首字母大写。状态名可以包含任意数量的字母、数字和除了冒号“：”以外的一些字符，可以较长，甚至连续几行。但是一定要注意一个状态的名称在状态图所在的上下文中应该是唯一的，能够把该状态和其他状态区分开。

- 入口和出口动作一个状态可以具有或者没有入口和出口动作。入口和出口动作分别指的是进入和退出一个状态时所执行的“边界”动作。

- 内部转换指的是不导致状态改变的转换。内部转换中可以包含进入或者退出该状态应该执行的活动或动作。

- 嵌套状态状态分为简单状态（Simple State）和组成状态（Composite State）。简单状态是指在语义上不可分解的、对象保持一定属性值的状况，简单状态不包含其他状态：而组成状态是指内部嵌套有子状态的状态，在组成状态的嵌套状态图部分包含的就是此状态的子状态。

**转换**：在UML的状态建模机制中，转换用带箭头的直线表示，一端连接源状态，箭头指向目标状态。转换还可以标注与此转换相关的选项，如事件、监护条件和动作等，如下图所示。注意：如果转换上没有标注触发转换的事件，则表示此转换自动进行。
![[attachment/Pasted image 20220522172704.png]]
在状态转换机制中需要注意的五个概念如下：
- 状态源（Source State）：指的是激活转换之间对象处于的状态。如果一个一个状态处于源状态，当它接收到转换的触发事件或满足监护条件时，就激活了一个离开的转换。
- 目标状态（Event State）：指的是转换完成后对象所处的状态。
- 事件触发器（Event Trigger）：指的是引起源状态转换的事件。事件不是持续发生的，它只发生在时间的一点上，对象接收到事件，导致源状态发生变化，激活转换并使监护条件得到满足。
- 监护条件（Guard Condition）：是一个布尔表达式。当接收到触发事件要触发转换时，要对该表达式求值。如果表达式为真，则激活转换：如果表达式为假，则不激活转换，所接收到的触发事件丢失。
- 动作（Action）：是一个可执行的原子计算。

**初始状态**：每个状态图都应该有一个初始状态，它代表状态图的起始位置。初始状态是一个伪状态（一个和普通状态有连接的假状态），对象不可能保持在初始状态，必须要有一个输出的无触发转换（没有事件触发器的转换）。通常初始状态上的转换是无监护条件的，并且初始状态只能作为转换的源，而不能作为转换的目标。在UML中，一个状态图只能有一个初始状态，用一个实心圆表示。

**终止状态**：终止状态是一个状态图的终点，一个状态图可以拥有一个或者多个终止状态。对象可以保持在终止状态，但是终止状态不可能有任何形式的和触发转换，它的目的就是为了激发封装状态上的转换过程的结束。因此，终止状态只能作为转换的目标而不能作为转换的源，在UML中，终止状态用一个含有实心圆的空心圆表示。

**判定**：活动图和状态图中都有需要根据给定条件进行判断，然后根据不同的判断结果进行不同转换的情况。实际就是工作流在此处按监护条件的取值发生分支，在UML中，判定用空心菱形表示。
可以**用状态图对系统的动态方面建模**。这些动态方面可以包括出现在系统体系结构的任何视图中的任何一种对象的按事件排序的行为，这些对象包括类（各主动类)、接口、构件和结点。当对系统、类或用例的动态方面建模时，通常是对反应型对象建模。

### 活动图
活动图(Activity Diagram)是一种特殊的状态图，它展现了在系统内<u>从一个活动到另一个活动的流程</u>。活动图专注于系统的动态视图，它对于系统的功能建模特别重要，并强调对象间的控制流程。
![[attachment/Pasted image 20220522173504.png]]
- 活动图一般包括活动状态和动作状态、转换和对象
-  活动图可以表示分支、合并、分岔和汇合
- 当对一个系统的动态方面建模时，通常有两种使用活动图的方式
	- 对工作流建模
	- 对操作建模

### 构件图
构件图(Component Diagram)展现了一组构件之间的组织和依赖。构件图专注于系统的静态实现视图。它与类图相关，通常把构件映射为一个或多个类、接口或协作。

### 部署图
部署图(Deployment Diagram)展现了运行处理节点以及其中构件的配置。部署图给出了体系结构的静态实施视图。它与构件图相关，通常一个节点包含一个或多个构件。
