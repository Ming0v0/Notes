# 概念和准备
## 什么是JdbcTemplate
Spring框架对JDBC进行封装，使用JdbcTemplate方便实现对数据操作

# 准备工作
- 引入相关的依赖
- 在 spring 配置文件配置数据库连接池
```xml
<!--引入外部属性文件-->  
<context:property-placeholder location="jdbc.properties"/>  
<!--配置连接池-->  
<bean id="druidSource" class="com.alibaba.druid.pool.DruidDataSource">  
    <property name="driverClassName" value="${driverClassName}"/>  
    <property name="url" value="${url}"/>  
    <property name="username" value="${jdbc.username}"/>  
    <property name="password" value="${password}"/>  
</bean>
```

配置 JdbcTemplate 对象，注入 DataSource
```xml
<!--JdbcTemplate-->  
<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">  
    <!--注入druidSource-->  
    <property name="dataSource" ref="druidSource"/>  
</bean>
```

创建 service 类，创建 dao 类，在 dao 注入 jdbcTemplate 对象
```java
/**  
 * service层要用dao层的东西 所以需要注入dao对象 dao层需要连接池来连接数据库所以要注入jdbcTemplate对象  
 **/  
@Service  
public class BookService {  
    //注入 dao    @Autowired  
    private BookDao bookDao;
}
```

```java
@Service  
public class BookDaoImpl implements BookDao {  
    //注入 JdbcTemplate    @Autowired  
    private JdbcTemplate jdbcTemplate;
}
```

```java
@Configuration  
@ComponentScan(basePackages = {"dao"})  
@ComponentScan(basePackages = {"service"})  
public class BookConfig {  
}
```

# 增加
对应数据库创建实体类
```java
public class Book {  
    private String id;  
    private String name;  
    private String status;
}
```

编写 Service 和 DAO
在 dao 进行数据库添加操作
调用 JdbcTemplate 对象里面 update 方法实现添加操作
```java
@Service  
public class BookDaoImpl implements BookDao {  
    //注入 JdbcTemplate    
    @Autowired  
    private JdbcTemplate jdbcTemplate;  
  
    @Override  
    public void add(Book book) {  
        //1 创建 sql 语句  
        String sql = "insert into t_book values(?,?,?)";  
        //2 调用方法实现  
        jdbcTemplate.update(sql, book.getId(), book.getName(), book.getStatus());  
    }
}
```
有两个参数
![[attachment/Pasted image 20220908124451.png]]
-   第一个参数：sql 语句
-   第二个参数：可变参数，设置 sql 语句值

测试：
```java
public class testBook {  
    ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");  
    BookService bookService = context.getBean("bookService", BookService.class);  
  
    @Test  
    public void TestBook() {  
        bookService.addBook(new Book("1", "Spring5实战", "a"));  
    }
}
```

![[attachment/Pasted image 20220908124558.png]]

# 修改和删除
```java
@Override  
public void update(Book book) {  
    String sql = "update t_book set name=?,status=? where ?";  
    jdbcTemplate.update(sql, book.getName(), book.getStatus(), book.getId());  
}  
```

```java
@Override  
public void delete(int id) {  
    String sql = "delete from t_book where id=?";  
    jdbcTemplate.update(sql, id);  
}
```

# 查询返回某个值
查询表里面有多少条记录，返回是某个值
使用 JdbcTemplate 实现查询返回某个值代码
![[attachment/Pasted image 20220908124757.png]]
有两个参数
-   第一个参数：sql 语句
-   第二个参数：返回类型 Class
```java
//查询表记录数  
@Override  
public int selectCount() {  
    String sql = "select count(*) from t_book";  
    return jdbcTemplate.queryForObject(sql, Integer.class);  
}
```

# 查询返回对象
场景：查询图书详情
JdbcTemplate 实现查询返回对象
![[attachment/Pasted image 20220908124901.png]]
有三个参数
-   第一个参数：sql 语句
-   第二个参数：RowMapper 是接口，针对返回不同类型数据，使用这个接口里面实现类完成数据封装
-   第三个参数：sql 语句值

# 查询返回集合
1. 场景：查询图书列表分页
2. 调用 JdbcTemplate 方法实现查询返回集合
![[attachment/Pasted image 20220908124955.png]]
有三个参数
-   第一个参数：sql 语句
-   第二个参数：RowMapper 是接口，针对返回不同类型数据，使用这个接口里面实现类完成数据封装
-   第三个参数：sql 语句值

# 批量操作
批量操作：操作表里面多条记录
### JdbcTemplate 实现批量添加操作
![[attachment/Pasted image 20220908125123.png]]
有两个参数
-   第一个参数：sql 语句
-   第二个参数：List 集合，添加多条记录数据
```java
//批量添加  
@Override  
public void batchAll(List list) {  
    String sql = "insert into t_book values(?,?,?)";  
    int[] ints = jdbcTemplate.batchUpdate(sql, list);  
    System.out.println(Arrays.toString(ints));  
  
}
```

测试：
```java
@Test  
public void test7() {  
    ArrayList<Object[]> list = new ArrayList<>();  
    list.add(new Object[]{"2", "大话数据结构", "a"});  
    list.add(new Object[]{"3", "计算机网络", "a"});  
    list.add(new Object[]{"4", "计算机组成原理", "a"});  
    bookService.batchAddBook(list);  
}
```

结果：
![[attachment/Pasted image 20220908125302.png]]

### JdbcTemplate 实现批量修改操作
```java
// 批量修改
@Override  
public void batchUpdateBook(List list) {  
    String sql = "update t_book set name=?,status=? where id=?";  
    int[] ints = jdbcTemplate.batchUpdate(sql, list);  
    System.out.println(Arrays.toString(ints));  
}
```

### JdbcTemplate 实现批量删除操作
```java
// 批量删除  
@Override  
public void batchDelete(List list) {  
    String sql = "delete from t_book where id=?";  
    int[] ints = jdbcTemplate.batchUpdate(sql, list);  
    System.out.println(Arrays.toString(ints));  
}  
```

[下一页](第07章%20事务管理.md)