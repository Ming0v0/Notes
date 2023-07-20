# 什么是 Vue
官方给出的概念：Vue (读音 `/vjuː/`，类似于 view) 是一套用于构建用户界面的前端框架。

![](images/什么是vue.png)

什么是构建用户界面
 - 用 vue 往 html 页面中填充数据，非常的方便

什么是框架
 - 框架是一套现成的解决方案，程序员只能遵守框架的规范，去编写自己的业务功能
 - 要学习 vue，就是在学习 Vue 框架中规定的用法！
 - vue 的指令、组件（是对 UI 结构的复用）、路由、Vuex、Vue 组件库

# Vue 的特性
Vue 框架的特性，主要体现在如下两方面：
- 数据驱动视图
- 双向数据绑定

## 数据驱动视图
在使用了 vue 的页面中，Vue 会监听数据的变化，从而自动重新渲染页面的结构。示意图如下：
![数据驱动视图](images/数据驱动视图.png)
- 优点：当页面数据发生变化时，页面会自动重新渲染！
- 注意：数据驱动视图是单向的数据绑定。

## 双向数据绑定
在填写表单时，双向数据绑定可以辅助开发者在不操作 DOM 的前提下，自动把用户填写的内容同步到数据源中。
示意图如下：

![双向数据绑定](images/双向数据绑定.png)
- 优点：开发者不再需要手动操作 DOM 元素，来获取表单元素最新的值！

## MVVM
MVVM 是 Vue 实现数据驱动视图和双向数据绑定的核心原理。MVVM 指的是 Model、View 和 ViewModel， 它把每个 HTML 页面都拆分成了这三个部分，如图所示：

![MVVM](images/MVVM.png)

**MVVM 的工作原理**
ViewModel 作为 MVVM 的核心，是它把当前页面的数据源（Model）和页面的结构（View）连接在了一起。

![MVVM](images/MVVM工作原理.png)
- 当数据源发生变化时，会被 ViewModel 监听到，VM 会根据最新的数据源自动更新页面的结构
- 当表单元素的值发生变化时，也会被 VM 监听到，VM 会把变化过后最新的值自动同步到 Model 数据源中

# Vue 的基本使用
## 基本使用步骤
- 导入 vue.js 的 script 脚本文件
- 在页面中声明一个将要被 Vue 所控制的 DOM 区域
- 创建 vm 实例对象（vue 实例对象）

```html
<body>
    <!-- 2.在页面中声明一个要被vue所控制的 DOM 区域 -->
    <div id="app">
        {{username}}
    </div>
    <!-- 1. 导入vue.js 的 script 脚本文件-->
    <script src="./lib/vue-2.6.12.js"></script>
    <script>
        // 3. 创建 vm 实例对象(vue 实例对象)
         const vm = new Vue({
            // 3.1 指定当前 vm 实例要控制页面的哪个区域
            el:'#app',
            //3.2 指定Model 数据源
            data:{
                username：'zs'
            }
        })
    </script>
</body>
```

## 基本代码与 MVVM 的对应关系
```html
<body>
    <!-- View 视图区域 -->
    <div id="app">
        {{username}}
    </div>
    <script src="./lib/vue-2.6.12.js"></script>
    <script>
        // 3. new Vue() 构造函数，得到的 vm 实例对象就是ViewModel
         const vm = new Vue({
            // 2. el指向得选择器就是View 视图区域
            el:'#app',
            // 1. data 指向得对象就是Model 数据源
            data:{
                username：'zs'
            }
        })
    </script>
</body>
```

## el:挂载点
- el 是用来设置 Vue 实例挂载（管理）的元素
- Vue 会管理 el 选项命中的元素及其内部的后代元素
- 可以使用其他的选择器，但是建议使用 ID 选择器
- 可以使用其他的双标签,不能使用 HTML 和 BODY

```html
<body>
    <div id="app">{{message}}</div>
    <script>
             const vm = new Vue({
                el:'#app',
                data:{
                    message：'Hello'
                }
            })
    </script>
</body>
```

## data:数据对象
- Vue 中用到的数据定义在 data 中
- data 中可以写复杂类型的数据
- 渲染复杂类型数据时,遵守 js 的语法即可

```html
<body>
    <div id="app">{{message}}</div>
    <script>
             const vm = new Vue({
                el:'#app',
                data:{
                    message：'Hello'
                }
            })
    </script>
</body>
```

# 单页面应用程序
## 什么是单页面应用程序
单页面应用程序（英文名：Single Page Application）简称 SPA，顾名思义，指的是一个 Web 网站中只有唯一的一个 HTML 页面，所有的功能与交互都在这唯一的一个页面内完成。

例如资料中的这个 Demo 项目：
![单页面应用程序](images/单页面应用程序.png)

## 什么是 vue-cli
vue-cli 是 Vue.js 开发的标准工具。它简化了程序员基于 webpack 创建工程化的 Vue 项目的过程。

引用自 vue-cli 官网上的一句话：[中文官网](https://cli.vuejs.org/zh/)
> 程序员可以专注在撰写应用上，而不必花好几天去纠结 webpack 配置的问题。

## 安装和使用
vue-cli 是 npm 上的一个全局包，使用 npm install 命令，即可方便的把它安装到自己的电脑上：
```txt
npm install -g @vue/cli
```

基于 vue-cli 快速生成工程化的 Vue 项目：
```txt
vue create 项目的名称
```

## vue 项目中 src 目录的构成
- assets 文件夹：存放项目中用到的静态资源文件，例如：css样式表、图片资源
- components 文件夹：程序员封装的、可复用的组件，都要放到 components 目录下
- main.js 是项目的入口文件。整个项目的运行，要先执行main.js
- App.vue 是项目的根组件。

## vue 项目的运行流程
在工程化的项目中，vue 要做的事情很单纯：通过 main.js 把 App.vue 渲染到 index.html 的指定区域中。
其中：
- App.vue 用来编写待渲染的模板结构
- index.html 中需要预留一个 el 区域
- main.js 把 App.vue 渲染到了 index.html 所预留的区域中

 