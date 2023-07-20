# 什么是组件化开发
组件化开发指的是：根据封装的思想，把页面上**可重用的 UI 结构封装为组件**，从而方便项目的开发和维护。

# Vue 中的组件化开发
Vue 是一个支持组件化开发的前端框架。
Vue 中规定：组件的后缀名是 .vue。之前接触到的 App.vue 文件本质上就是一个 Vue 的组件。

# Vue 组件的三个组成部分
每个 .vue 组件都由 3 部分构成，分别是：
- template  ➡️ 组件的模板结构
- script  ➡️ 组件的 JavaScript 行为
- style ➡️ 组件的样式

其中，每个组件中必须包含 template 模板结构，而 script 行为和 style 样式是可选的组成部分。


**template**
Vue 规定：每个组件对应的模板结构，需要定义到`<template>` 节点中。
```html
<template>
	<!-- 当前组件的 DOM 结构，需要定义到 template 标签的内部-->
</template>
```
注意：
- template 是 Vue 提供的容器标签，只起到包裹性质的作用，它不会被渲染为真正的 DOM 元素
- template 中只能包含唯一的根节点


**script**
Vue 规定：开发者可以在`<script>`节点中封装组件的JavaScript业务逻辑。
`<script>`节点的基本结构如下：
 ```html
 <script>
 	// 今后，组件相关的 data 数据、methods 方法等，
     // 都需要定义到export default 所导出的对象中。
     export default {}
 </script>
 ```

**.vue 组件中的 data 必须是函数**
Vue 规定：.vue 组件中的 data 必须是一个函数，不能直接指向一个数据对象。
因此在组件中定义 data 数据节点时，下面的方式是错误的：
 ```javascript
 data:{ // 组件中，不能直接让 data 指向一个数据对象(会报错)
 	conunt: 0
 }
 ```

会导致多个组件实例共用同一份数据的问题，请参考官方给出的[示例](https://cn.vuejs.org/v2/guide/components.html#data-必须是一个函数)

**style**
Vue 规定：组件内的 `<script>`节点是可选的，开发者可以在` <script>` 节点中编写样式美化当前组件的 UI 结构。
`<script> `节点的基本结构如下：
 ```html
 <style>
     h1{
         font-weight: normal;
     }
 </style>
 ```

**让 style 中支持 less 语法**
在 \<script\> 标签上添加 lang="less" 属性，即可使用 less 语法编写组件的样式：
```html
<style lang="less">
    h1{
        font-weight: normal;
            span{
                color:red;
        }
    }
</style>
```

# 组件之间的父子关系
![组件之间的父子关系](images/组件之间的父子关系.png)
组件在被封装好之后，彼此之间是相互独立的，不存在父子关系。在使用组件的时候，根据彼此的嵌套关系，形成了父子关系、兄弟关系

## 使用组件的三个步骤
```html
<template>
	<div id="app">
		<div class="box">
			<!-- 步骤3：以标签形式使用刚才注册的组件-->
			<Left></Left>
		</div>
	</div>
</template>
<script>
	// 步骤1：使用import 语法导入需要的组件
	import Left from '@/comonents/Left.vue'
	export default {  
	  // 步骤2：使用components 节点注册组件
	  components:{
		  Left
	  } 
	}  
</script>
```


**通过 components 注册的是私有子组件**
例如：
- 在组件 A 的 components 节点下，注册了组件 F。
- 则组件 F 只能用在组件 A 中；不能被用在组件 C 中。

请大家思考两个问题：
为什么 F 不能用在组件 C 中？
- 因为组件C没有注册组件F，组件F只对组件A生效
怎样才能在组件 C 中使用 F？
- 注册全局组件

## 注册全局组件
在 Vue 项目的 main.js 入口文件中，通过 Vue. component() 方法，可以注册全局组件。示例代码如下：
```javascript
// 导入需要全局注册的组件
import Count from '@/components/Count.vue'

// 参数1：字符串格式，表示组件的"注册名称"
// 参数2：需要被全局注册的哪个组件
Vue.component('MyCount',Count)
```



# 组件的props
props 是组件的自定义属性，在封装通用组件的时候，合理地使用 props 可以极大的提高组件的复用性

它的语法格式如下：
```html
<script>
	export default{
        // 组件的自定义属性 
        props: ['自定义属性A','自定义属性B','其他自定义属性...']
    }
    
    // 组件的私有属性
    data() {
        return { }
    }
</script>
```

**props 是只读的**
Vue 规定：组件中封装的自定义属性是只读的，程序员不能直接修改 props 的值。否则会直接报错：
![props 是只读的](images/props是只读的.png)
要想修改 props 的值，可以把 props 的值转存到 data 中，因为 data 中的数据都是可读可写的！

**props 的 default 默认值**
在声明自定义属性时，可以通过 default 来定义属性的默认值。
示例代码如下：
 ```html
 <script>
 	export default{
     	props: {
       		init: {
                 // 用 default 属性定义属性的默认值
                 default: 0
             }
     	}
 </script>
 ```

**props 的 type 值类型**
在声明自定义属性时，可以通过 type 来定义属性的值类型。
示例代码如下：
 ```html
 <script>
 	export default{
     	props: {
       		init: {
                 // 用 default 属性定义属性的默认值
                 default: 0,
                 // 用 type 属性定义属性的值类型，
                 // 如果传递过来的值不符合此类型，则会在终端报错
                 type:Number
             }
     	}
 </script>
 ```

通过数组形式，为当前属性定义多个可能的类型
```html
<script>
	export default{
    	props: {
      		init: {
                type:[Number,String]
            }
    	}
</script>
```

**在使用组件的时候，如果某个属性名是“小驼峰”形式，则绑定属性的时候，建议改写成“连字符”格式**
```javascript
:cmt-count="item.cmt_count"
```

**props 的 required 必填项**
在声明自定义属性时，可以通过 required 选项，将属性设置为必填项，强制用户必须传递属性的值。
示例代码如下：
```html
<script>
	export default{
    	props: {
      		init: {
				// 值类型为 Number 数字
                type:Number,
                // 必填项校验
                required: true
            }
    	}
</script>
```


# 组件之间的样式冲突问题
默认情况下，写在 .vue 组件中的样式会全局生效，因此很容易造成多个组件之间的样式冲突问题。 
导致组件之间样式冲突的根本原因是： 
- 单页面应用程序中，所有组件的 DOM 结构，都是基于唯一的 index.html 页面进行呈现的 
- 每个组件中的样式，都会影响整个 index.html 页面中的 DOM 元素 

**思考：如何解决组件样式冲突的问题**
为每个组件分配唯一的自定义属性，在编写组件样式时，通过属性选择器来控制样式的作用域，示例代码如下：
 ```html
 <template>
 	<div class="container" data-v-001>
         <h3 data-v-001>轮播图组件</h3>
     </div>
 </template>
 
 <style>
 	/* 通过中括号"属性选择器"，来防止组件之间的"样式冲突问题"
     	因为每个组件分配的自定义属性是"唯一的"*/
     .container[data-v-001]{
         border:1px solid red;
     }
 </style>
 ```


## style 节点的 scoped 属性
为了提高开发效率和开发体验，Vue 为 style 节点提供了 scoped 属性，从而防止组件之间的样式冲突问题：
 ```html
 <template>
 	<div class="container">
         <h3 >轮播图组件</h3>
     </div>
 </template>
 
 <style scoped>
 	/* style 节点的 scoped 属性，用来自动为每个组件分配唯一的"自定义属性",
     	并自动为当前组件的 DOM 标签和 style 样式应用这个自定义属性，防止组件的样式冲突问题*/
     .container{
         border:1px solid red;
     }
 </style>
 ```

## /deep/ 样式穿透
如果给当前组件的 style 节点添加了 scoped 属性，则当前组件的样式对其子组件是不生效的。如果想让某些样式对子组件生效，可以使用 /deep/ 深度选择器
```html
<style scoped>
    .title{
        color: blue; /* 不加 /deep/ 时，生成的选择器格式为 .title [data-v-052242de] */
    }
    /deep/ .title{
         color: blue;/* 加上 /deep/ 时，生成的选择器格式为 .[data-v-052242de] title */
    }
</style>
```

