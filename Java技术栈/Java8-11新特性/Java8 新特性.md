# Lambda

## 为什么使用 Lambda 表达式

Lambda 是一个匿名函数，我们可以把 Lambda 表达式理解为是一段可以传递的代码（将代码像数据一样进行传递）。使用它可以写出更简洁、更灵活的代码。作为一种更紧凑的代码风格，使Java的语言表达能力得到了 提升。

**从匿名类到 Lambda 的转换举例**
```java
// 匿名内部类  
Runnable r1 = new Runnable() {
    @Override
    public void run() {
        System.out.println("我爱北京天安门");
    }
};
```

```java
// Lambda 表达式
 Runnable r2 = () -> System.out.println("我爱北京故宫");
```

```java
// 原来使用匿名内部类作为参数传递
 Comparator<Integer> com1 = new Comparator<Integer>() {
    @Override
    public int compare(Integer o1, Integer o2) {
        return Integer.compare(o1,o2);
    }
};
```

```java
// Lambda 表达式作为参数
 Comparator<Integer> com2 = (o1,o2) -> Integer.compare(o1,o2);
```

## Lambda 表达式：语法

Lambda 表达式：在Java 8 语言中引入的一种新的语法元素和操作符。这个操作符为 `->` ， 该操作符被称为 Lambda 操作符 或箭头操作符。

它将 Lambda 分为两个部分： 
- 左侧：指定了 Lambda 表达式需要的参数列表 
- 右侧：指定了 Lambda 体，是抽象方法的实现逻辑，也即 Lambda 表达式要执行的功能。

**语法格式一：无参，无返回值**
```java
// 以前的方式
Runnable r1 = new Runnable() {
    @Override
    public void run() {
        System.out.println("我爱北京天安门");
    }
};

r1.run();
```

```java
// lambda表达式
Runnable r2 = () -> {
    System.out.println("我爱北京故宫");
};

r2.run();
```

**语法格式二：Lambda 需要一个参数，但是没有返回值。**
```java
// 以前的方式
Consumer<String> con = new Consumer<String>() {
    @Override
    public void accept(String s) {
        System.out.println(s);
    }
};
con.accept("谎言和誓言的区别是什么？");
```

```java
// lambda表达式
Consumer<String> con1 = (String s) -> {
    System.out.println(s);
};
con1.accept("一个是听得人当真了，一个是说的人当真了");
```

**语法格式三：数据类型可以省略，因为可由编译器推断得出，称为“类型推断”**
```java
Consumer<String> con1 = (String s) -> {
    System.out.println(s);
};
con1.accept("一个是听得人当真了，一个是说的人当真了");
```

```java
// 不需要声明类型，编译器可以进行类型推断
Consumer<String> con2 = (s) -> {
    System.out.println(s);
};
con2.accept("一个是听得人当真了，一个是说的人当真了");
```

**类型推断**
上述 Lambda 表达式中的参数类型都是由编译器推断得出的。Lambda  表达式中无需指定类型，程序依然可以编译，这是因为 `javac` 根据程序 的上下文，在后台推断出了参数的类型。

Lambda 表达式的类型依赖于 上下文环境，是由编译器推断出来的。这就是所谓的“类型推断”。

**语法格式四：Lambda 若只需要一个参数时，参数的小括号可以省略**
```java
Consumer<String> con1 = (s) -> {
        System.out.println(s);
};
con1.accept("一个是听得人当真了，一个是说的人当真了");

```

```java
// 参数的小括号可以省略
Consumer<String> con2 = s -> {
    System.out.println(s);
};
con2.accept("一个是听得人当真了，一个是说的人当真了");
```

**语法格式五：Lambda 需要两个或以上的参数，多条执行语句，并且可以有返回值**
```java
// 以前的方式
Comparator<Integer> com1 = new Comparator<Integer>() {
    @Override
    public int compare(Integer o1, Integer o2) {
        System.out.println(o1);
        System.out.println(o2);
        return o1.compareTo(o2);
    }
};

System.out.println(com1.compare(12,21));
```

```java
// lambda表达式
Comparator<Integer> com2 = (o1,o2) -> {
    System.out.println(o1);
    System.out.println(o2);
    return o1.compareTo(o2);
};

System.out.println(com2.compare(12,6));
```

**语法格式六：当 Lambda 体只有一条语句时，return 与大括号若有，都可以省略**
```java
// 以前的方式:
Comparator<Integer> com1 = (o1,o2) -> {
    return o1.compareTo(o2);
};
System.out.println(com1.compare(12,6));
```

```java
// lambda表达式:
Comparator<Integer> com2 = (o1,o2) -> o1.compareTo(o2);
System.out.println(com2.compare(12,21));
```

# 什么是函数式(Functional)接口

只包含一个抽象方法的接口，称为函数式接口。

可以通过 Lambda 表达式来创建该接口的对象。（若 Lambda 表达式 抛出一个受检异常(即：非运行时异常)，那么该异常需要在目标接口的抽象方法上进行声明）。

可以在一个接口上使用 `@FunctionalInterface` 注解，这样做可以检查它是否是一个函数式接口。同时 `javadoc` 也会包含一条声明，说明这个接口是一个函数式接口。

在`java.util.function`包下定义了Java 8 的丰富的函数式接口

## 如何理解函数式接口

Java从诞生日起就是一直倡导 “一切皆对象”，在Java里面面向对象(OOP) 编程是一切。但是随着python、scala等语言的兴起和新技术的挑战，Java不得不做出调整以便支持更加广泛的技术要求，也即java不但可以支持OOP还 可以支持OOF（面向函数编程）

在函数式编程语言当中，函数被当做一等公民对待。在将函数作为一等公民的 编程语言中，Lambda表达式的类型是函数。但是在Java8中，有所不同。在 Java8中，Lambda表达式是对象，而不是函数，它们必须依附于一类特别的 对象类型函数式接口。

简单的说，在Java8中，Lambda表达式就是一个函数式接口的实例。这就是 Lambda表达式和函数式接口的关系。也就是说，只要一个对象是函数式接口的实例，那么该对象就可以用Lambda表达式来表示。

所以以前用匿名实现类表示的现在都可以用Lambda表达式来写。

**函数式接口举例**
```java
@FunctionalInterface  
public interface Runnable {  
/**  
 * When an object implementing interface <code>Runnable</code> is used  
* to create a thread, starting the thread causes the object's * <code>run</code> method to be called in that separately executing  
* thread. * <p>  
* The general contract of the method <code>run</code> is that it may  
* take any action whatsoever. * * @see java.lang.Thread#run()  
*/ 
	public abstract void run();  
}
```

**自定义函数式接口**
```java
@FunctionalInterface
public interface MyNumber{
    public double getValue();
}
```

**函数式接口中使用泛型**
```java
@FunctionalInterface
publci interface MyFunc<T>{
    public T getValue(T t);
}
```

**作为参数传递 Lambda 表达式**
```java
public String toUpperString(MyFunc<String> mf,String str){
    return mf.getValue(str)
}
```

```java
// 作为参数传递 Lambda 表达式
String newStr = toUpperString(
    (str) -> str.toUpperCase(),"abcdef"
);
```

作为参数传递 Lambda 表达式：为了将 Lambda 表达式作为参数传递，接收Lambda 表达式的参数类型必须是与该 Lambda 表达式兼容的函数式接口的类型。

## Java 内置四大核心函数式接口
 
| 函数式接口              | 参数类型 | 返回类型    | 用途                                                    |
| ------------------ | ---- | ------- | ----------------------------------------------------- |
| Consumer\<T> 消费型接口  | T    | void    | 对类型为T的对象应用操作，包含方法： void accept(T t)                   |
| Supplier\<T> 供给型接口  | 无    | T       | 返回类型为T的对象，包含方法：T get()                                |
| Function\<T> 函数型接口  | T    | R       | 对类型为T的对象应用操作，并返回结果。结 果是R类型的对象。包含方法：R apply(T t)       |
| Predicate\<T> 断定型接口 | T    | boolean | 确定类型为T的对象是否满足某约束，并返回 boolean 值。包含方法：boolean test(T t) |

**其他接口**

| 函数式接口                                                  | 参数类型            | 返回类型            | 用途                                                     |
| ------------------------------------------------------ | --------------- | --------------- | ------------------------------------------------------ |
| BiFunction<T,U,R>                                      | T,R             | R               | 对类型为 T, U 参数应用操作，返回 R 类型的结 果。包含方法为： R apply(T t, U u); |
| UnaryOperator\<T> (Function子接口)                         | T               | T               | 对类型为T的对象进行一元运算，并返回T类型的 结果。包含方法为：T apply(T t);          |
| BinaryOperator\<T> (BiFunction 子接口)                     | T,T             | T               | 对类型为T的对象进行二元运算，并返回T类型的 结果。包含方法为： T apply(T t1, T t2);  |
| BiConsumer\<T,U>                                        | T,U             | void            | 对类型为T, U 参数应用操作。 包含方法为： void accept(T t, U u)          |
| BiPredicate\<T,U>                                       | T,U             | boolean         | 包含方法为： boolean test(T t,U u)                           |
| ToIntFunction\<T> ToLongFunction\<T> ToDoubleFunction\<T> | T               | int、long、double | 分别计算int、long、double值的函数                                |
| IntFunction\<R> LongFunction\<R> DoubleFunction\<R>       | int、long、double | R               | 参数分别为int、long、double 类型的函数                             |

# 方法引用(Method References)
- 当要传递给Lambda体的操作，已经有实现的方法了，可以使用方法引用！
- 方法引用可以看做是Lambda表达式深层次的表达。换句话说，方法引用就是Lambda表达式，也就是函数式接口的一个实例，通过方法的名字来指向 一个方法，可以认为是Lambda表达式的一个语法糖。
- 要求：实现接口的抽象方法的参数列表和返回值类型，必须与方法引用的 方法的参数列表和返回值类型保持一致！
- 格式：使用操作符 “::” 将类(或对象) 与 方法名分隔开来。
- 如下三种主要使用情况：
  - 对象::实例方法名
  - 类::静态方法名
  - 类::实例方法名

例如：
```java
Consumer<String> con = (x) -> System.out.println(x);
```

等同于
```java
Consumer<String> con2 =System.out::println;
```

例如：
```java
Comparator<Integer> com = (x,y) -> Integer.compare(x,y);
```

等同于
```java
Comparator<Integer> com = Integer::compare;
com.compare(12,32);
```

例如：
```java
BiPreDicate<String,String> bp =(x,y) ->x.equals(y);
```

等同于：
```java
BiPreDicate<String,String> bp1 =Stirng::equals;
boolean flag =bp1.test("hello","hi");
```

注意：当函数式接口方法的第一个参数是需要引用方法的调用者，并且第二 个参数是需要引用方法的参数(或无参数)时：ClassName::methodName

## 构造器引用

格式： `ClassName::new`

与函数式接口相结合，自动与函数式接口中方法兼容。 可以把构造器引用赋值给定义的方法，要求构造器参数列表要与接口中抽象 方法的参数列表一致！且方法的返回值即为构造器对应类的对象。

例如：
```java
Function<Integer,MyClass> fun = (n) -> new MyClass(n);
```

等同于
```java
Function<Integer,Myclass> fun =MyClass::new;
```

## 数组引用

格式： `type[] :: new`

例如：
```java
Function<Integer,Integer[]> fun = (n) -> new Integer(n);
```

等同于
```java
Function<Integer,Integer[]> fun =Integer::new;
```

# Stream API说明
- Java8中有两大最为重要的改变。第一个是 Lambda 表达式；另外一个则 是 Stream API。
- Stream API ( java.util.stream) 把真正的函数式编程风格引入到Java中。这是目前为止对Java类库最好的补充，因为Stream API可以极大提供Java程序员的生产力，让程序员写出高效率、干净、简洁的代码。
- Stream 是 Java8 中处理集合的关键抽象概念，它可以指定你希望对集合进行的操作，可以执行非常复杂的查找、过滤和映射数据等操作。 使用 Stream API 对集合数据进行操作，就类似于使用 SQL 执行的数据库查询。 也可以使用 Stream API 来并行执行操作。简言之，Stream API 提供了一种 高效且易于使用的处理数据的方式。

**为什么要使用Stream API**

- 实际开发中，项目中多数数据源都来自于Mysql，Oracle等。但现在数据源可以更多了，有MongDB，Radis等，而这些NoSQL的数据就需要 Java层面去处理。
- Stream 和 Collection 集合的区别：Collection 是一种静态的内存数据 结构，而 Stream 是有关计算的。前者是主要面向内存，存储在内存中， 后者主要是面向 CPU，通过 CPU 实现计算。

## 什么是 Stream

Stream到底是什么呢？ 是数据渠道，用于操作数据源（集合、数组等）所生成的元素序列。

```
 “集合讲的是数据，Stream讲的是计算！” 
```

注意：

① Stream 自己不会存储元素。

② Stream 不会改变源对象。相反，他们会返回一个持有结果的新Stream。

③ Stream 操作是延迟执行的。这意味着他们会等到需要结果的时候才执行。

# Stream 的操作三个步骤

1. 创建 Stream

​ 一个数据源（如：集合、数组），获取一个流

2. 中间操作
   
   一个中间操作链，对数据源的数据进行处理

3. 终止操作(终端操作)
   
   一旦执行终止操作，**就执行中间操作链**，并产生结果。之后，不会再被使用

![](file://C:\Users\Jun\OneDrive\我的文档\笔记\Java\Java8新特性\images\Stream 的操作三个步骤.png?msec=1651145211306)

## 创建 Stream

### 创建 Stream方式一：通过集合

Java8 中的 Collection 接口被扩展，提供了两个获取流 的方法：

- default Stream stream() : 返回一个顺序流
- default Stream parallelStream() : 返回一个并行流

```java
    // 创建 Stream方式一：通过集合
    @Test
    public void test1(){
        List<Employee> employees = EmployeeData.getEmployees();

        // default Stream<E> stream() : 返回一个顺序流
        Stream<Employee> stream = employees.stream();

        // default Stream<E> parallelStream() : 返回一个并行流
        Stream<Employee> parallelStream = employees.parallelStream();

    }
```

### 创建 Stream方式二：通过数组

Java8 中的 Arrays 的静态方法 stream() 可以获取数组流：

​ **static Stream stream(T[] array): 返回一个流 **

重载形式，能够处理对应基本类型的数组：

- public static IntStream stream(int[] array)

- public static LongStream stream(long[] array)

- public static DoubleStream stream(double[] array)

```java
    // 创建 Stream方式二：通过数组
    @Test
    public void test2(){
        int[] arr = new int[]{1,2,3,4,5,6};
        // 调用Arrays类的static <T> Stream<T> stream(T[] array): 返回一个流
        IntStream stream = Arrays.stream(arr);

        Employee e1 = new Employee(1001,"Tom");
        Employee e2 = new Employee(1002,"Jerry");
        Employee[] arr1 = new Employee[]{e1,e2};
        Stream<Employee> stream1 = Arrays.stream(arr1);

    }
```

### 创建 Stream方式三：通过Stream的of()

可以调用Stream类静态方法 of(), 通过显示值创建一个 流。它可以接收任意数量的参数。

**public static Stream of(T... values) : 返回一个流**

```java
     // 创建 Stream方式三：通过Stream的of()
    @Test
    public void test3(){

        Stream<Integer> stream = Stream.of(1, 2, 3, 4, 5, 6);

    }
```

### 创建 Stream方式四：创建无限流

可以使用静态方法 Stream.iterate() 和 Stream.generate(), 创建无限流。

- 迭代 public static Stream iterate(final T seed, final UnaryOperator f)

- 生成 public static Stream generate(Supplier s)

```java
    // 方式四：创建无限流
    @Test
    public void test4() {
        // 迭代
        // public static<T> Stream<T> iterate(final T seed, finalUnaryOperator<T> f)
        // 遍历前10个偶数
        Stream<Integer> stream = Stream.iterate(0, x -> x + 2);
        stream.limit(10).forEach(System.out::println);

        // 生成
        // public static<T> Stream<T> generate(Supplier<T> s)
        Stream<Double> stream1 = Stream.generate(Math::random);
        stream1.limit(10).forEach(System.out::println);
    }
```

## Stream 的中间操作

多个中间操作可以连接起来形成一个流水线，除非流水线上触发终止 操作，否则中间操作不会执行任何的处理！而在终止操作时一次性全 部处理，称为“惰性求值”。

### 1-筛选与切片

| 方 法                 | 描 述                                                      |
| ------------------- | -------------------------------------------------------- |
| filter(Predicate p) | 接收 Lambda ， 从流中排除某些元素                                    |
| distinct()          | 筛选，通过流所生成元素的 hashCode() 和 equals() 去除重复元素                |
| limit(long maxSize) | 截断流，使其元素不超过给定数量                                          |
| skip(long n)        | 跳过元素，返回一个扔掉了前 n 个元素的流。若流中元素不足 n 个，则返回一 个空流。与 limit(n) 互补 |

```java
//1-筛选与切片
    @Test
    public void test1(){
        List<Employee> list = EmployeeData.getEmployees();
        // filter(Predicate p)——接收 Lambda ， 从流中排除某些元素。
        Stream<Employee> stream = list.stream();
        // 练习：查询员工表中薪资大于7000的员工信息
        stream.filter(e -> e.getSalary() > 7000).forEach(System.out::println);

        System.out.println();
        // limit(n)——截断流，使其元素不超过给定数量。
        list.stream().limit(3).forEach(System.out::println);
        System.out.println();

        // skip(n) —— 跳过元素，返回一个扔掉了前 n 个元素的流。若流中元素不足 n 个，则返回一个空流。与 limit(n) 互补
        list.stream().skip(3).forEach(System.out::println);

        System.out.println();
        // distinct()——筛选，通过流所生成元素的 hashCode() 和 equals() 去除重复元素

        list.add(new Employee(1010,"刘强东",40,8000));
        list.add(new Employee(1010,"刘强东",41,8000));
        list.add(new Employee(1010,"刘强东",40,8000));
        list.add(new Employee(1010,"刘强东",40,8000));
        list.add(new Employee(1010,"刘强东",40,8000));

        // System.out.println(list);

        list.stream().distinct().forEach(System.out::println);
    }
```

### 2-映 射

| 方法                              | 描述                                             |
| ------------------------------- | ---------------------------------------------- |
| map(Function f)                 | 接收一个函数作为参数，该函数会被应用到每个元 素上，并将其映射成一个新的元素。        |
| mapToDouble(ToDoubleFunction f) | 接收一个函数作为参数，该函数会被应用到每个元 素上，产生一个新的 DoubleStream。 |
| mapToInt(ToIntFunction f)       | 接收一个函数作为参数，该函数会被应用到每个元 素上，产生一个新的 IntStream。    |
| mapToLong(ToLongFunction f)     | 接收一个函数作为参数，该函数会被应用到每个元 素上，产生一个新的 LongStream。   |
| flatMap(Function f)             | 接收一个函数作为参数，将流中的每个值都换成另 一个流，然后把所有流连接成一个流        |

```java
    // 映射
    @Test
    public void test2(){
        // map(Function f)——接收一个函数作为参数，将元素转换成其他形式或提取信息，该函数会被应用到每个元素上，并将其映射成一个新的元素。
        List<String> list = Arrays.asList("aa", "bb", "cc", "dd");
        list.stream().map(str -> str.toUpperCase()).forEach(System.out::println);

        // 练习1：获取员工姓名长度大于3的员工的姓名。
        List<Employee> employees = EmployeeData.getEmployees();
        Stream<String> namesStream = employees.stream().map(Employee::getName);
        namesStream.filter(name -> name.length() > 3).forEach(System.out::println);
        System.out.println();
        // 练习2：
        Stream<Stream<Character>> streamStream = list.stream().map(StreamAPITest1::fromStringToStream);
        streamStream.forEach(s ->{
            s.forEach(System.out::println);
        });
        System.out.println();
        //  flatMap(Function f)——接收一个函数作为参数，将流中的每个值都换成另一个流，然后把所有流连接成一个流。
        Stream<Character> characterStream = list.stream().flatMap(StreamAPITest1::fromStringToStream);
        characterStream.forEach(System.out::println);

    }

    //将字符串中的多个字符构成的集合转换为对应的Stream的实例
    public static Stream<Character> fromStringToStream(String str){//aa
        ArrayList<Character> list = new ArrayList<>();
        for(Character c : str.toCharArray()){
            list.add(c);
        }
       return list.stream();

    }
```

### 3-排序

| 方法                     | 描述                |
| ---------------------- | ----------------- |
| sorted()               | 产生一个新流，其中按自然顺序排序  |
| sorted(Comparator com) | 产生一个新流，其中按比较器顺序排序 |

```java
    // 3-排序
    @Test
    public void test4(){
        // sorted()——自然排序
        List<Integer> list = Arrays.asList(12, 43, 65, 34, 87, 0, -98, 7);
        list.stream().sorted().forEach(System.out::println);
        // 抛异常，原因:Employee没有实现Comparable接口
        // List<Employee> employees = EmployeeData.getEmployees();
        // employees.stream().sorted().forEach(System.out::println);


        // sorted(Comparator com)——定制排序

        List<Employee> employees = EmployeeData.getEmployees();
        employees.stream().sorted( (e1,e2) -> {

           int ageValue = Integer.compare(e1.getAge(),e2.getAge());
           if(ageValue != 0){
               return ageValue;
           }else{
               return -Double.compare(e1.getSalary(),e2.getSalary());
           }

        }).forEach(System.out::println);
    }
```

## Stream 的终止操作

- 终端操作会从流的流水线生成结果。其结果可以是任何不是流的值，例 如：List、Integer，甚至是 void 。

- 流进行了终止操作后，不能再次使用。

### 1-匹配与查找

| 方法                     | 描述               |
| ---------------------- | ---------------- |
| allMatch(Predicate p)  | 检查是否匹配所有元素       |
| anyMatch(Predicate p)  | 检查是否至少匹配一个元素     |
| noneMatch(Predicate p) | 检查是否**没有**匹配所有元素 |
| findFirst()            | 返回第一个元素          |
| findAny()              | 返回当前流中的任意元素      |

| 方法                  | 描述                                                                     |
| ------------------- | ---------------------------------------------------------------------- |
| count()             | 返回流中元素总数                                                               |
| max(Comparator c)   | 返回流中最大值                                                                |
| min(Comparator c)   | 返回流中最小值                                                                |
| forEach(Consumer c) | 内部迭代(使用 Collection 接口需要用户去做迭代， 称为外部迭代。相反，Stream API 使用内部迭 代——它帮你把迭代做了) |

```java
    // 1-匹配与查找
    @Test
    public void test1(){
        List<Employee> employees = EmployeeData.getEmployees();

        // allMatch(Predicate p)——检查是否匹配所有元素。
        // 练习：是否所有的员工的年龄都大于18
        boolean allMatch = employees.stream().allMatch(e -> e.getAge() > 18);
        System.out.println(allMatch);

        // anyMatch(Predicate p)——检查是否至少匹配一个元素。
        // 练习：是否存在员工的工资大于 10000
        boolean anyMatch = employees.stream().anyMatch(e -> e.getSalary() > 10000);
        System.out.println(anyMatch);

        // noneMatch(Predicate p)——检查是否没有匹配的元素。
        // 练习：是否存在员工姓“雷”
        boolean noneMatch = employees.stream().noneMatch(e -> e.getName().startsWith("雷"));
        System.out.println(noneMatch);
        // findFirst——返回第一个元素
        Optional<Employee> employee = employees.stream().findFirst();
        System.out.println(employee);
        // findAny——返回当前流中的任意元素
        Optional<Employee> employee1 = employees.parallelStream().findAny();
        System.out.println(employee1);

    }
```

```java
    @Test
    public void test2(){
        List<Employee> employees = EmployeeData.getEmployees();
        // count——返回流中元素的总个数
        long count = employees.stream().filter(e -> e.getSalary() > 5000).count();
        System.out.println(count);
        // max(Comparator c)——返回流中最大值
        // 练习：返回最高的工资：
        Stream<Double> salaryStream = employees.stream().map(e -> e.getSalary());
        Optional<Double> maxSalary = salaryStream.max(Double::compare);
        System.out.println(maxSalary);
        // min(Comparator c)——返回流中最小值
        // 练习：返回最低工资的员工
        Optional<Employee> employee = employees.stream().min((e1, e2) -> Double.compare(e1.getSalary(), e2.getSalary()));
        System.out.println(employee);
        System.out.println();
        // forEach(Consumer c)——内部迭代
        employees.stream().forEach(System.out::println);

        // 使用集合的遍历操作
        employees.forEach(System.out::println);
    }
```

### 2-归约

| 方法                               | 描述                               |
| -------------------------------- | -------------------------------- |
| reduce(T iden, BinaryOperator b) | 可以将流中元素反复结合起来，得到一 个值。返回 T        |
| reduce(BinaryOperator b)         | 可以将流中元素反复结合起来，得到一 个值。返回 Optional |

备注：map 和 reduce 的连接通常称为 map-reduce 模式，因 Google 用它来进行网络搜索而出名。

```java
     //2-归约
    @Test
    public void test3(){
        // reduce(T identity, BinaryOperator)——可以将流中元素反复结合起来，得到一个值。返回 T
        // 练习1：计算1-10的自然数的和
        List<Integer> list = Arrays.asList(1,2,3,4,5,6,7,8,9,10);
        Integer sum = list.stream().reduce(0, Integer::sum);
        System.out.println(sum);


        // reduce(BinaryOperator) ——可以将流中元素反复结合起来，得到一个值。返回 Optional<T>
        // 练习2：计算公司所有员工工资的总和
        List<Employee> employees = EmployeeData.getEmployees();
        Stream<Double> salaryStream = employees.stream().map(Employee::getSalary);
        // Optional<Double> sumMoney = salaryStream.reduce(Double::sum);
        Optional<Double> sumMoney = salaryStream.reduce((d1,d2) -> d1 + d2);
        System.out.println(sumMoney.get());

    }
```

### 3-收集

| 方法                   | 描述                                                 |
| -------------------- | -------------------------------------------------- |
| collect(Collector c) | 将流转换为其他形式。接收一个 Collector 接口的实现，用于给Stream中元素做汇总 的方法 |

```java
    // 3-收集
    @Test
    public void test4(){
    // collect(Collector c)——将流转换为其他形式。接收一个 Collector接口的实现，用于给Stream中元素做汇总的方法
    // 练习1：查找工资大于6000的员工，结果返回为一个List或Set

        List<Employee> employees = EmployeeData.getEmployees();
        List<Employee> employeeList = employees.stream().filter(e -> e.getSalary() > 6000).collect(Collectors.toList());

        employeeList.forEach(System.out::println);
        System.out.println();
        Set<Employee> employeeSet = employees.stream().filter(e -> e.getSalary() > 6000).collect(Collectors.toSet());

        employeeSet.forEach(System.out::println);

    }
```

# Optional类

Collector 接口中方法的实现决定了如何对流执行收集的操作(如收集到 List、Set、 Map)。 另外， Collectors 实用类提供了很多静态方法，可以方便地创建常见收集器实例， 具体方法与实例如下表：

- 到目前为止，臭名昭著的空指针异常是导致Java应用程序失败的最常见原因。 以前，为了解决空指针异常，Google公司著名的Guava项目引入了Optional类， Guava通过使用检查空值的方式来防止代码污染，它鼓励程序员写更干净的代码。受到Google Guava的启发，Optional类已经成为Java 8类库的一部分。
- Optional 类(java.util.Optional) 是一个容器类，它可以保存类型T的值，代表 这个值存在。或者仅仅保存null，表示这个值不存在。原来用 null 表示一个值不 存在，现在 Optional 可以更好的表达这个概念。并且可以避免空指针异常。
- Optional类的Javadoc描述如下：这是一个可以为null的容器对象。如果值存在 则isPresent()方法会返回true，调用get()方法会返回该对象。

**Optional提供很多有用的方法，这样我们就不用显式进行空值检测。**

## 创建Optional类对象的方法

- Optional.of(T t) : 创建一个 Optional 实例，t必须非空；

- Optional.empty() : 创建一个空的 Optional 实例

- Optional.ofNullable(T t)：t可以为null

## 判断Optional容器中是否包含对象

- boolean isPresent() : 判断是否包含对象

- void ifPresent(Consumer consumer) ：如果有值，就执行Consumer 接口的实现代码，并且该值会作为参数传给它。

## 获取Optional容器的对象

- T get(): 如果调用对象包含值，返回该值，否则抛异常
- T orElse(T other) ：如果有值则将其返回，否则返回指定的other对象。
- T orElseGet(Supplier other) ：如果有值则将其返回，否则返回由 Supplier接口实现提供的对象。
- T orElseThrow(Supplier exceptionSupplier) ：如果有值则将其返 回，否则抛出由Supplier接口实现提供的异常。

```java
@Test
public void test1() {
    Boy b = new Boy("张三");
    Optional<Girl> opt = Optional.ofNullable(b.getGrilFriend());
    // 如果女朋友存在就打印女朋友的信息
    opt.ifPresent(System.out::println);
}

@Test
public void test2() {
    Boy b = new Boy("张三");
    Optional<Girl> opt = Optional.ofNullable(b.getGrilFriend());
    // 如果有女朋友就返回他的女朋友，否则只能欣赏“嫦娥”了
    Girl girl = opt.orElse(new Girl("嫦娥"));
    System.out.println("他的女朋友是：" + girl.getName());
}
```

```java
@Test
public void test3(){
    Optional<Employee> opt = Optional.of(new Employee("张三", 8888));
    // 判断opt中员工对象是否满足条件，如果满足就保留，否则返回空
    Optional<Employee> emp = opt.filter(e -> e.getSalary()>10000);
    System.out.println(emp);
}

@Test
public void test4(){
    Optional<Employee> opt = Optional.of(new Employee("张三", 8888));
    //如果opt中员工对象不为空，就涨薪10%
    Optional<Employee> emp = opt.map(e -> 
    {e.setSalary(e.getSalary()%1.1);return e;});
    System.out.println(emp);
}
```
