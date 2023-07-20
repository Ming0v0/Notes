# HelloWorld
## HelloWorld简单版
1. 创建一张测试表
2. 创建对应的JavaBean
3. 创建mybatis配置文件，sql映射文件
4. 测试
### MyBatis操作数据库
1. 创建MyBatis全局配置文件
MyBatis 的全局配置文件包含了影响 MyBatis 行为甚深的设置（settings）和属性（properties）信息、如数据库连接池信息等。指导着MyBatis进行工作。

2. 创建SQL映射文件：映射文件的作用就相当于是定义Dao接口的实现类如何工作。
```xml
	<mapper namespace="dao.EmployeeMapper">  
    <!--  
	    namespace:名称空间;指定为接口的全类名  
	    id：唯一标识  
	    resultType：返回值类型  
	    #{id}：从传递过来的参数中取出id值  
  
    public Employee getEmpById(Integer id);     
    -->
	<select id="getEmpById" resultType="bean.Employee">  
    select id, last_name as lastName, email, gender  
    from tbl_employee    where id = #{id}
    </select>
```

测试
1. 根据全局配置文件，利用SqlSessionFactoryBuilder创建SqlSessionFactory
```Java
String resource = "mybatis-config.xml";  
InputStream resourceAsStream = Resources.getResourceAsStream(resource);  
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
```
2. 使用SqlSessionFactory获取sqlSession对象。一个SqlSession对象代表和数据库的一次会话。
```java
SqlSession sqlSession = sqlSessionFactory.openSession();
```
3. 使用sql的唯一标志id来告诉MyBatis执行哪个sql。sql都是保存sql映射文件中的
```java
try {  
    Employee employee = sqlSession.selectOne("EmployeeMapper.getEmpById", 1);  
    System.out.println(employee);  
} finally {  
    sqlSession.close();  
}
```

## HelloWorld-接口式编程
- 创建一个Dao接口
- 修改Mapper文件
- 测试

| 对比 | MyBatis    | 原生    |
| ---- | ---------- | ------- |
| 接口 | XXXMapper  | XXXDAO     |
| 实现 | XXXMapper.xml | XXXDAOImpl |
1. SqlSession代表和数据库的一次会话：用完必须关闭
2. SqlSession和connection一样它都是非线程安全。每次使用都应该获取新的对象
3. mapper接口没有实现类，但是mybatis会为这个接口生成一个代理对象。(将接口和xml进行绑定)
4. 两个重要的配置文件
	- MyBatis的全局配置文件：包含数据库连接池信息，事务管理器信息等系统运行环境信息
	- SQL映射文件：保存了每个SQL语句的映射信息：将SQL抽取出来

使用SqlSession获取映射器进行操作
```java
@Test  
public void test2() throws IOException {  
    String resource = "mybatis-config.xml";  
    InputStream inputStream = Resources.getResourceAsStream(resource);  
    //1、获取sqlSessionFactory对象  
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);  
  
    //2、获取sqlSession对象  
    SqlSession sqlSession = sqlSessionFactory.openSession();  
  
    //3、获取接口的实现类对象  
    //会为接口自动的创建一个代理对象，代理对象去实现增删改查方法  
  
    EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);  
  
    System.out.println(mapper.getClass());  
    Employee empById = mapper.getEmpById(1);  
    System.out.println(empById);  
  
    sqlSession.close();  
}
```
