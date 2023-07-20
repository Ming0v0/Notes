# 指令的概念
指令（Directives）是 Vue 为开发者提供的模板语法，用于辅助开发者渲染页面的基本结构。
Vue 中的指令按照不同的用途可以分为如下 6 大类：
- 内容渲染指令
- 属性绑定指令
- 事件绑定指令
- 双向绑定指令
- 条件渲染指令
- 列表渲染指令

注意：指令是 Vue 开发中最基础、最常用、最简单的知识点。

# 内容渲染指令
内容渲染指令用来辅助开发者渲染 DOM 元素的文本内容。常用的内容渲染指令有如下 3 个：
-  `v-text`
- `{{ }}`
- `v-html`

## v-text 
用法示例： 
```html
<!-- 把 username 对应的值，渲染到第一个 p 标签中-->
<p v-text="username"></p>

<!-- 把 gernder 对应的值，渲染到第二个 p 标签中 -->
<!-- 注意：第二个 p 标签，默认的文本"性别"会被 gender 的值覆盖掉 -->
<p v-text="gender">性别</p>
```
注意：v-text 指令会覆盖元素内默认的值。 

## {{ }} 语法
Vue 提供的 {{ }} 语法，专门用来解决 v-text 会覆盖默认文本内容的问题。这种 {{ }} 语法的专业名称是**插值表达式**（英文名为：Mustache）。

```html
<!-- 使用{{ }}插值表达式，将对应的值渲染到元素的内容节点中 -->
<!-- 同时保留元素自身的默认值 -->
<p>姓名：{{username}}</p>
<p>性别：{{gender}}</p>
```
注意：相对于 v-text 指令来说，插值表达式在开发中更常用一些！因为它不会覆盖元素中默认的文本内容。

## v-html
v-text 指令和插值表达式只能渲染纯文本内容。如果要把包含 HTML 标签的字符串渲染为页面的 HTML 元素，则需要用到 v-html 这个指令：

```html
<!-- 假设 data 中定义了名为discription 的数据，数据的值为包含 HTML 标签的字符串 -->
<!-- <h5 style='color:red'>学习vue<h5> -->
```

最终渲染的结果为：



# 属性绑定指令
如果需要为元素的属性动态绑定属性值，则需要用到 v-bind 属性绑定指令。
用法示例如下： 
 ```vue
 <!-- 假设有如下的data数据
 data:{
 	inputValue:'请输入内容'
 	imgSrc:'https://cn.vue.org/images/log.png'
 }
 -->
 
 <!-- 使用 v-bind 指令，为 input 的 placeholder 动态绑定属性值 -->
 <input tpye = "text" v-bind:placeholder = "inputvalue" />
 <br/>
 <!-- 使用 v-bind 指令，为 img 的 src 动态绑定属性 -->
 <img v-bind:src=imgSrc alt="" />
 ```

## 属性绑定指令的简写形式
由于 v-bind 指令在开发中使用频率非常高，因此，Vue 官方为其提供了简写形式（简写为英文的  **:** ）。
 ```vue
 <!-- 假设有如下的data数据
 data:{
 	inputValue:'请输入内容'
 	imgSrc:'https://cn.vue.org/images/log.png'
 }
 -->
 
 <!-- 使用 v-bind 指令，为 input 的 placeholder 动态绑定属性值 -->
 <input tpye = "text" :placeholder="inputvalue" />
 <br/>
 <!-- 使用 v-bind 指令，为 img 的 src 动态绑定属性 -->
 <img :src=imgSrc alt="" />
 ```



## 使用 Javascript 表达式
 在 Vue 提供的模板渲染语法中，除了支持绑定简单的数据值之外，还支持 Javascript 表达式的运算，例如： 
 ```html
 {{ number + 1}}
 
 {{ ok ? 'Yes'：'NO'}}
 
 {{message.split('').reverse().join('') }}
 
 <div v-bind:id="'list-' +id"></div>
 ```

在使用 v-bind 属性绑定期间，如果绑定内容需要**进行动态拼接，则字符串的外面应该包裹单引号**，例如：
 ```html
 <div :title="'box'+index">这是一个 div</div>
 ```



# 事件绑定指令
Vue 提供了 v-on 事件绑定指令，用来辅助程序员为 DOM 元素绑定事件监听。语法格式如下：
```html
<h3>count 的值为:{{count}}</h3>
<!-- 语法格式为 v-on:事件名称="事件处理函数的名称" -->
<button v-on:click="addCount">+1</button>
```

注意：原生 DOM 对象有 onclick、oninput、onkeyup 等原生事件，替换为 vue 的事件绑定形式后， 分别为：v-on:click、v-on:input、v-on:keyup 

通过 v-on 绑定的事件处理函数，需要在 methods 节点中进行声明： 
```html
<script>
	const vm =new Vue({
		el:'#app',
		data:{count:0},
		methods:{		// v-on 绑定事件处理函数，需要声明在 methods 节点中
			addCount(){ // 事件处理函数的名字
				// this 表示当前 new 出来的 vm 实例对象
				// 通过 this 可以访问到 data 中的数据
				this.count += 1
			}			
		},
	})
</script>
```



## 事件绑定的简写形式
由于 v-on 指令在开发中使用频率非常高，因此，Vue 官方为其提供了简写形式（简写为英文的 **@** ）。

 ```html
 <div>
     <h3>count 的值为:{{count}}</h3>
 	
     <!-- 完整写法-->
 	<button v-on:click="addCount">+1</button>
     
     <!-- 简写形式，把 v-on: 简写为@符号 -->
     <!-- 如果事件处理的函数中的代码足够简单，只有一行代码，则可以间歇到行内 -->
     <button @click="count += 1">+1</button>
 </div>
 ```


## 事件参数对象
在原生的 DOM 事件绑定中，可以在事件处理函数的形参处，接收事件参数对象 event。同理，在 v-on 指令（简写为 @ ）所绑定的事件处理函数中，同样可以接收到事件参数对象 event，示例代码如下：

 ```html
 <h3>count 的值为:{{count}}</h3>
 <button v-on:click="addCount">+1</button>
 
 // ----------------------分割线-----------------------
 
 methods:{
 		addCount(e){ // 接收事件参数对象 event，简写为 e
 			const nowBgColor = e.target.style.backgroundColor
 			e.target.style.backgroundColor = nowBgColor === 'red' ? '' : 'red'
 			this.count += 1
 		}		
 }
 ```


## 绑定事件并传参
在使用 v-on 指令绑定事件时，可以使用 ( ) 进行传参
示例代码如下：
 ```html
 <h3>count 的值为:{{count}}</h3>
 <button v-on:click="addNewCount(2)">+2</button>
```

```js
 methods:{
 		// 形参处用 step 接收传递过来的参数值
 		addCount(step){ 
 			this.count += step
 		}		
 }
 ```



## $event
\$event 是 vue 提供的特殊变量，用来表示原生的事件参数对象 event。$event 可以解决事件参数对象 event 

被覆盖的问题。示例用法如下：
 ```html
 <h3>count 的值为:{{count}}</h3>
 <button v-on:click="addNewCount(2,$event)">+2</button>
 
```

```js 
 methods:{
 		// 形参处用 e 接收传递过来的原生事件参数对象 $event
 		addCount(step,e){ 
 			const nowBgColor = e.target.style.backgroundColor
 			e.target.style.backgroundColor = nowBgColor === 'cyan' ? '' : 'cyan'
 			this.count += step
 		}		
 }
 ```



## 事件修饰符
在事件处理函数中调用 event.preventDefault() 或 event.stopPropagation() 是非常常见的需求。
因此，Vue 提供了事件修饰符的概念，来辅助程序员更方便的对事件的触发进行控制。
常用的 5 个事件修饰符如下：
![事件修饰符](images/事件修饰符.png)

语法格式如下： 
 ```html
 <!-- 触发 click 点击事件时，阻止 a 链接的默认跳转行为 -->
 <a href="https://www.baidu.com" @click.prevent="onLinkClick">百度首页</a>
 ```



## 按键修饰符
在监听键盘事件时，我们经常需要判断详细的按键。此时，可以为键盘相关的事件添加按键修饰符，例如：
 ```html
 <!-- 只有在 'key' 是 'Enter' 时 调用 'vm.submit()' -->
 <input @keyup.enter="submit"/>
 
 <!-- 只有在 'key' 是 'Esc' 时 调用 'vm.clearInput()' -->
 <input @keyup.esc="clearInput"/>
 ```



# 双向绑定指令
Vue 提供了 v-model 双向数据绑定指令，用来辅助开发者在不操作 DOM 的前提下，快速获取表单的数据。 
 ```html
 <p>用户名是：{{username}}</p>
 <input type="text" v-model="username" />
 
 <p>选中的省份是：{{province}}</p>
 <select v-model=province>
     <option value="">请选择</option>
     <option value="1">北京</option>
     <option value="2">河北</option>
     <option value="3">黑龙江</option>
 </select>
 ```



## v-model 指令的修饰符
为了方便对用户输入的内容进行处理，Vue 为 v-model 指令提供了 3 个修饰符，分别是：
![v-model 指令的修饰符](images/v-model指令的修饰符.png)

示例用法如下： 
 ```html
 <input type="text" v-model.number="n1"/>
 <input type="text" v-model.number="n2"/>
 <span>{{{n1 + n2}}</span>
 ```



# 条件渲染指令
条件渲染指令用来辅助开发者按需控制 DOM 的显示与隐藏。
条件渲染指令有如下两个，分别是：
- `v-if`
- `v-show`

示例用法如下： 
 ```html
 <div id="app">
     <p v-if="networkState === 200">请求成功 --- 被 v-if 控制</p>
     <p v-show="networkState === 200">请求成功 --- 被 v-show 控制</p>
 </div>
 ```

## v-if 和 v-show 的区别
实现原理不同：
- v-if 指令会动态地创建或移除 DOM 元素，从而控制元素在页面上的显示与隐藏
- v-show 指令会动态为元素添加或移除 `style="display: none;"` 样式，从而控制元素的显示与隐藏

性能消耗不同：
- v-if 有更高的切换开销，而 v-show 有更高的初始渲染开销。

因此：
- 如果需要非常频繁地切换，则使用 v-show 较好
- 如果在运行时条件很少改变，则使用 v-if 较好

## v-else
v-if 可以单独使用，或配合 v-else 指令一起使用：
```html
<div v-if="Math.random() > 0.5">
    随机数大于 0.5
</div>
<div v-else>
    随机数小于或等于 0.5
</div>
```
注意：v-else 指令必须配合 v-if 指令一起使用，否则它将不会被识别！ 

 
## v-else-if
v-else-if 指令，顾名思义，充当 v-if 的“else-if 块”，可以连续使用：
```html
<div v-if="type === 'A'">优秀</div>
<div v-else-if="type === 'B'">优秀</div>
<div v-else-if="type === 'C'">优秀</div>
<div v-else>差</div>
```
注意：v-else-if 指令必须配合 v-if 指令一起使用，否则它将不会被识别！ 



# 列表渲染指令
Vue 提供了 v-for 列表渲染指令，用来辅助开发者基于一个数组来循环渲染一个列表结构。v-for 指令需要使

用 `item in items` 形式的特殊语法，其中：
- items 是待循环的数组
- item 是被循环的每一项

```js
data:{
	list:[ // 列表数据
		{ id:1,name:'zs' },
		{ id:2,name:'ls' }
	]
}
```

```html
<ul>
    <li v-for="item in list">姓名是：{{item.name}}</li>
</ul>
```

## v-for 中的索引
v-for 指令还支持一个可选的第二个参数，即当前项的索引。
语法格式为 `(item, index) in items`
示例代码如下：
```js
data:{
	list:[ // 列表数据
		{ id:1,name:'zs' },
		{ id:2,name:'ls' }
	]
}
```

```html
<ul>
    <li v-for="(item,index) in list">索引是：{{index}}, 姓名是：{{item.name}}</li>
</ul>
```

注意：v-for 指令中的 item 项和 index 索引都是形参，可以根据需要进行重命名。
例如 `(user, i) in userlist `

## 使用 key 维护列表的状态
当列表的数据变化时，默认情况下，Vue 会尽可能的复用已存在的 DOM 元素，从而提升渲染的性能。但这种默认的性能优化策略，会导致有状态的列表无法被正确更新。

为了给 vue 一个提示，以便它能跟踪每个节点的身份，从而在保证有状态的列表被正确更新的前提下，提升渲染的性能。此时，需要为每项提供一个唯一的 key 属性：
```html
<!-- 用户列表区域 -->
<ul>
  <!-- 加 key 属性的好处：-->
  <!-- 1.正确维护列表的状态 -->
  <!-- 2.复用现在 DOM 元素，提升渲染的性能 -->
   <li v-for="user in userlist" :key="user.id">
    	<input tpye="checkbox" />
       姓名：{{user.name}}
    </li>
</ul>
```

**key 的注意事项**
- key 的值只能是字符串或数字类型
- key 的值必须具有唯一性（即：key 的值不能重复）
- 建议把数据项 id 属性的值作为 key 的值（因为 id 属性的值具有唯一性）
- 使用 index 的值当作 key 的值没有任何意义（因为 index 的值不具有唯一性）
- 建议使用 v-for 指令时一定要指定 key 的值（既提升性能、又防止列表状态紊乱）

 