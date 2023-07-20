# 动态SQL
-   动态 SQL是MyBatis强大特性之一。极大的简化我们拼装SQL的操作
-   动态 SQL 元素和使用 JSTL 或其他类似基于 XML 的文本处理器相似
-   MyBatis 采用功能强大的基于 OGNL 的表达式来简化操作

OGNL（ Object Graph Navigation Language ）对象图导航语言，这是一种强大的表达式语言，通过它可以非常方便的来操作对象属性。 类似于我们的EL，SpEL等
![[attachment/Pasted image 20220530232804.png]]
访问集合伪属性：
![[attachment/Pasted image 20220530232817.png]]

## If:判断
可以使用where标签替代where
```xml
<!-- 查询员工，要求，携带了哪个字段查询条件就带上这个字段的值 -->
<select id="getEmpsByConditionIf" resultType="bean.Employee">  
    select * from tbl_employee  
    <!-- where -->  
    <where>  
        <!-- test：判断表达式（OGNL）  
        OGNL参照PPT或者官方文档。  
               c:if  test        从参数中取值进行判断  
  
        遇见特殊符号应该去写转义字符：  
        &&：  
        -->  
        <if test="id!=null">  
            id=#{id}  
        </if>  
        <if test="lastName!=null &amp;&amp; lastName!=&quot;&quot;">  
            and last_name like #{lastName}  
        </if>  
        <if test="email!=null and email.trim()!=&quot;&quot;">  
            and email=#{email}  
        </if>  
        <!-- ognl会进行字符串与数字的转换判断  "0"==0 -->  
        <if test="gender==0 or gender==1">  
            and gender=#{gender}  
        </if>  
    </where>  
</select>
```

## choose (when, otherwise)
分支选择：带了break的switch-case
如果带了id就用id查，如果带了lastName就用lastName查
只会进入其中一个
```xml
<!-- public List<Employee> getEmpsByConditionChoose(Employee employee); -->  
<select id="getEmpsByConditionChoose" resultType="bean.Employee">  
    select * from tbl_employee  
    <where>  
        <!-- 如果带了id就用id查，如果带了lastName就用lastName查;只会进入其中一个 -->  
        <choose>  
            <when test="id!=null">  
                id=#{id}  
            </when>  
            <when test="lastName!=null">  
                last_name like #{lastName}  
            </when>  
            <when test="email!=null">  
                email = #{email}  
            </when>  
            <otherwise>  
                gender = 0  
            </otherwise>  
        </choose>  
    </where>  
</select>
```

## set
可以使用set标签替代set
```xml
<!-- public void updateEmp(Employee employee);  -->  
<update id="updateEmp">  
    <!-- Set标签的使用 -->  
    update tbl_employee  
    <set>  
        <if test="lastName!=null">  
            last_name=#{lastName},  
        </if>  
        <if test="email!=null">  
            email=#{email},  
        </if>  
        <if test="gender!=null">  
            gender=#{gender}  
        </if>  
    </set>  
    where id=#{id}
</update>
```
## trim
后面多出的and或者or where标签不能解决
trim标签体中是整个字符串拼串后的结果
- prefix=""：前缀，prefix给拼串后的整个字符串加一个前缀
- prefixOverrides=""，前缀覆盖：去掉整个字符串前面多余的字符
- suffix=""，后缀：suffix给拼串后的整个字符串加一个后缀
- suffixOverrides=""，后缀覆盖：去掉整个字符串后面多余的字符
```xml
<!--public List<Employee> getEmpsByConditionTrim(Employee employee);  -->  
<select id="getEmpsByConditionTrim" resultType="bean.Employee">  
    select * from tbl_employee  
    <!-- 自定义字符串的截取规则 -->  
    <trim prefix="where" suffixOverrides="and">  
        <if test="id!=null">  
            id=#{id} and  
        </if>  
        <if test="lastName!=null &amp;&amp; lastName!=&quot;&quot;">  
            last_name like #{lastName} and  
        </if>  
        <if test="email!=null and email.trim()!=&quot;&quot;">  
            email=#{email} and  
        </if>  
        <!-- ognl会进行字符串与数字的转换判断  "0"==0 -->  
        <if test="gender==0 or gender==1">  
            gender=#{gender}  
        </if>  
    </trim>  
</select>
```

```xml
<!--public void updateEmp(Employee employee);  -->  
<update id="updateEmp">  
    <!-- Set标签的使用 -->  
    update tbl_employee  
    <trim prefix="set" suffixOverrides=",">  
        <if test="lastName!=null">  
            last_name=#{lastName},  
        </if>  
        <if test="email!=null">  
            email=#{email},  
        </if>  
        <if test="gender!=null">  
            gender=#{gender}  
        </if>  
    </trim>  
    where id=#{id}  
</update>
```

## foreach
动态 SQL 的另外一个常用的必要操作是需要对一个集合进行遍历，通常是在构建 IN 条件语句的时候。
- collection：指定要遍历的集合
- list类型的参数会特殊处理封装在map中，map的key就叫list
- item：将当前遍历出的元素赋值给指定的变量
- separator：每个元素之间的分隔符
- open：遍历出所有结果拼接一个开始的字符
- close：遍历出所有结果拼接一个结束的字符
- index:：索引。遍历list的时候是index就是索引，item就是当前值
- 遍历map的时候index表示的就是map的key，item就是map的值

 #{变量名}就能取出变量的值也就是当前遍历出的元素
```xml
<!--public List<Employee> getEmpsByConditionForeach(List<Integer> ids);  -->  
<select id="getEmpsByConditionForeach" resultType="bean.Employee">  
    select * from tbl_employee  
    <foreach collection="ids" item="item_id" separator=","  
             open="where id in(" close=")">  
        #{item_id}  
    </foreach>  
</select>
```

**批量保存**
方式一：
```xml
<!-- 批量保存 -->  
<!--public void addEmps(@Param("emps")List<Employee> emps);  -->  
<!--MySQL下批量保存：可以foreach遍历   mysql支持values(),(),()语法-->  
<insert id="addEmps">  
    insert into tbl_employee(last_name,email,gender,d_id)
	values    
    <foreach collection="emps" item="emp" separator=",">  
        (#{emp.lastName},#{emp.email},#{emp.gender},#{emp.dept.id})  
    </foreach>  
</insert>
```

方式二：
```xml
<!-- 这种方式需要数据库连接属性allowMultiQueries=true；  
    这种分号分隔多个sql可以用于其他的批量操作（删除，修改） -->  
<insert id="addEmps">  
    <foreach collection="emps" item="emp" separator=";">        
	    insert into tbl_employee(last_name,email,gender,d_id)        
	    values(#{emp.lastName},#{emp.email},#{emp.gender},#{emp.dept.id})    
    </foreach>
</insert>
```

**bind**
- bind 元素可以从 OGNL 表达式中创建一个变量并将其绑定到上下文。比如：
```xml
<bind name="_lastName" value="'%'+lastName+'%'"/>
```

**两个内置参数**：不只是方法传递过来的参数可以被用来判断，取值
mybatis默认还有两个内置参数：  
- \_parameter：代表整个参数  
	- 单个参数：`_parameter`就是这个参数  
	- 多个参数：参数会被封装为一个map，`_parameter`就是代表这个map
- \_databaseId：如果配置了databaseIdProvider标签
        `_databaseId`就是代表当前数据库的别名oracle
```xml
<!--public List<Employee> getEmpsTestInnerParameter(Employee employee);  -->  
<select id="getEmpsTestInnerParameter" resultType="bean.Employee">  
    <!-- bind：可以将OGNL表达式的值绑定到一个变量中，方便后来引用这个变量的值 -->  
    <bind name="_lastName" value="'%'+lastName+'%'"/>  
    <if test="_databaseId=='mysql'">  
        select * from tbl_employee  
        <if test="_parameter!=null">  
            where last_name like #{lastName}  
        </if>  
    </if>  
    <if test="_databaseId=='oracle'">  
        select * from tbl_employee  
        <if test="_parameter!=null">  
            where last_name like #{_parameter.lastName}  
        </if>  
    </if>  
</select>
```

## sql
抽取可重用的sql片段。方便后面引用
- sql抽取：经常将要查询的列名，或者插入用的列名抽取出来方便引用
- include来引用已经抽取的sql：
- include还可以自定义一些property，sql标签内部就能使用自定义的属性

include-property：取值的正确方式${prop}，#{不能使用这种方式}
```xml
<sql id="insertColumn">  
    <if test="_databaseId=='oracle'">  
        employee_id,last_name,email  
    </if>  
    <if test="_databaseId=='mysql'">  
        last_name,email,gender,d_id  
    </if>  
</sql>
```

```xml
<insert id="addEmps">  
    insert into tbl_employee(<include refid="insertColumn"></include>)  
    values    <foreach collection="emps" item="emp" separator=",">  
    (#{emp.lastName},#{emp.email},#{emp.gender},#{emp.dept.id})  
    </foreach>  
</insert>
```