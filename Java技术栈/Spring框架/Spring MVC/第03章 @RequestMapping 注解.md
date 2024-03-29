# @RequestMapping 注解的功能
从注解名称上我们可以看到，@RequestMapping 注解的作用就是将**请求和处理请求的控制器方法关联起来，建立映射关系。**

SpringMVC 接收到指定的请求，就会来找到在映射关系中对应的控制器方法来处理这个请求。

# @RequestMapping 注解的位置

- @RequestMapping 标识一个类：设置映射请求的请求路径的初始信息

- @RequestMapping 标识一个方法：设置映射请求请求路径的具体信息

```java
@Controller
@RequestMapping("/test")
public class RequestMappingController {

    //此时请求映射所映射的请求的请求路径为：/test/testRequestMapping
    @RequestMapping("/testRequestMapping")
    public String testRequestMapping(){
        return "success";
    }

}
```

# @RequestMapping 注解的 value 属性

- @RequestMapping 注解的 value 属性通过请求的请求地址匹配请求映射

- @RequestMapping 注解的 value 属性是一个字符串类型的数组，表示该请求映射能够匹配多个请求地址所对应的请求

- @RequestMapping 注解的 value 属性必须设置，至少通过请求地址匹配请求映射

```html
<a th:href="@{/testRequestMapping}">测试@RequestMapping的value属性-->/testRequestMapping</a><br>
<a th:href="@{/test}">测试@RequestMapping的value属性-->/test</a><br>
```

```java
@RequestMapping(
        value = {"/testRequestMapping", "/test"}
)
public String testRequestMapping(){
    return "success";
}
```

# @RequestMapping 注解的 method 属性

- @RequestMapping 注解的 method 属性通过请求的请求方式（get 或 post）匹配请求映射

- @RequestMapping 注解的 method 属性是一个 RequestMethod 类型的数组，表示该请求映射能够匹配多种请求方式的请求

若当前请求的请求地址满足请求映射的 value 属性，但是请求方式不满足 method 属性，则浏览器报错 405：`Request method 'POST' not supported`

```html
<a th:href="@{/test}">测试@RequestMapping的value属性-->/test</a><br>
<form th:action="@{/test}" method="post">
    <input type="submit">
</form>
```

```java
@RequestMapping(
        value = {"/testRequestMapping", "/test"},
        method = {RequestMethod.GET, RequestMethod.POST}
)
public String testRequestMapping(){
    return "success";
}
```

> 注：
> 对于处理指定请求方式的控制器方法，SpringMVC 中提供了@RequestMapping 的派生注解
> 处理 get 请求的映射-->@GetMapping
> 处理 post 请求的映射-->@PostMapping
> 处理 put 请求的映射-->@PutMapping
> 处理 delete 请求的映射-->@DeleteMapping
>
> 常用的请求方式有 get，post，put，delete
> 但是目前浏览器只支持 get 和 post，若在 form 表单提交时，为 method 设置了其他请求方式的字符串（put 或 delete），则按照默认的请求方式 get 处理
>
> 若要发送 put 和 delete 请求，则需要通过 spring 提供的过滤器 HiddenHttpMethodFilter，在 RESTful 部分会讲到

# @RequestMapping 注解的 params 属性（了解）

- @RequestMapping 注解的 params 属性通过请求的请求参数匹配请求映射

- @RequestMapping 注解的 params 属性是一个字符串类型的数组，可以通过四种表达式设置请求参数和请求映射的匹配关系

- "param"：要求请求映射所匹配的请求必须携带 param 请求参数
- "!param"：要求请求映射所匹配的请求必须不能携带 param 请求参数
- "param=value"：要求请求映射所匹配的请求必须携带 param 请求参数且 param=value
- "param!=value"：要求请求映射所匹配的请求必须携带 param 请求参数但是 param!=value

```html
<a th:href="@{/test(username='admin',password=123456)">测试@RequestMapping的params属性-->/test</a><br>
```

```java
@RequestMapping(
        value = {"/testRequestMapping", "/test"}
        ,method = {RequestMethod.GET, RequestMethod.POST}
        ,params = {"username","password!=123456"}
)
public String testRequestMapping(){
    return "success";
}
```

> 注：
> 若当前请求满足@RequestMapping 注解的 value 和 method 属性，但是不满足 params 属性，此时页面回报错 400：Parameter conditions "username, password!=123456" not met for actual request parameters: username={admin}, password={123456}

# @RequestMapping 注解的 headers 属性（了解）

- @RequestMapping 注解的 headers 属性通过请求的请求头信息匹配请求映射

- @RequestMapping 注解的 headers 属性是一个字符串类型的数组，可以通过四种表达式设置请求头信息和请求映射的匹配关系

- "header"：要求请求映射所匹配的请求必须携带 header 请求头信息

- "!header"：要求请求映射所匹配的请求必须不能携带 header 请求头信息

- "header=value"：要求请求映射所匹配的请求必须携带 header 请求头信息且 header=value

- "header!=value"：要求请求映射所匹配的请求必须携带 header 请求头信息且 header!=value

若当前请求满足@RequestMapping 注解的 value 和 method 属性，但是不满足 headers 属性，此时页面显示 404 错误，即资源未找到

# SpringMVC 支持 ant 风格的路径

- `？`：表示任意的单个字符

- `*`：表示任意的 0 个或多个字符

- `**`：表示任意的一层或多层目录

注意：在使用`**`时，只能使用`/**/xxx `的方式

# SpringMVC 支持路径中的占位符（重点）

原始方式：`/deleteUser?id=1`

rest 方式：`/deleteUser/1`

SpringMVC 路径中的占位符常用于 RESTful 风格中，当请求路径中将某些数据通过路径的方式传输到服务器中，就可以在相应的@RequestMapping 注解的 value 属性中通过占位符{xxx}表示传输的数据，在通过@PathVariable 注解，将占位符所表示的数据赋值给控制器方法的形参

```html
<a th:href="@{/testRest/1/admin}">测试路径中的占位符-->/testRest</a><br>
```

```java
@RequestMapping("/testRest/{id}/{username}")
public String testRest(@PathVariable("id") String id, @PathVariable("username") String username){
    System.out.println("id:"+id+",username:"+username);
    return "success";
}
// 最终输出的内容为-->id:1,username:admin
```

[下一页](./SpringMVC 获取请求参数.md)