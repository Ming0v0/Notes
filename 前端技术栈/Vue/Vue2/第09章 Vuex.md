# Vuex 概述
## 组件之间共享数据的方式
父向子传值：`v-bind` 属性绑定
子向父传值：`v-on` 事件绑定

兄弟组件之间共享数据： `EventBus`
- $on 接收数据的那个组件
- $emit 发送数据的那个组件

## Vuex 是什么
Vuex 是实现组件全局状态（数据）管理的一种机制，可以方便的实现组件之间数据的共享。
![[images/Pasted image 20221127114109.png]]

**使用 Vuex 统一管理状态的好处**
- 能够在 vuex 中集中管理共享的数据，易于开发和后期维护
- 能够高效地实现组件之间的数据共享，提高开发效率
- 存储在 vuex 中的数据都是响应式的，能够实时保持数据与页面的同步

**什么样的数据适合存储到 Vuex 中**
一般情况下，只有组件之间共享的数据，才有必要存储到 vuex 中；对于组件中的私有数据，依旧存储在组件自身的 data 中即可。

# Vuex 的基本使用
**安装 vuex 依赖包**
```txt
npm install vuex --save
```

**导入 vuex包**
```js
import Vuex from 'vuex'

Vue.use(Vuex)
```

**创建 store对象**
```js
const store = new Vuex.Store({
	// state 中存放的就是全局共享的数据
	state:{ count:0 }
})
```

**将 store 对象挂载到vue实例中**
```js
new Vue({
	el:'#app',
	render: h => h(app),
	router,
	// 将创建的共享数据对象，挂载到 Vue 实例中
	// 所有的组件，就可以直接从 store 中获取全局的数据率了
	store
})
```

# Vuex 的核心概念
## 核心概念概述
Vuex 中的主要核心概念如下：
- State
- Mutation
- Action
- Getter

### State
```js
// 创建store数据源，提供唯一公共数据
const store = new Vuex.store({
	state: { count : 0 }
})
```
State 提供唯一的公共数据源，所有共享的数据都要统一放到 Store 的 State 中进行存储。
组件访问 State 中数据的第一种方式：
```js
this.$store.state.全局数据名称
```

组件访问 State 中数据的第二种方式：
```js
// 1．从vuex中按需导入mapstate 函数
import { mapstate } from 'vuex'
```
通过刚才导入的 mapState 函数，将当前组件需要的全局数据，映射为当前组件的 `computed` 计算属性：
```js
// 2．将全局数据，映射为当前组件的计算属性
computed: {
	...mapstate (['count'])
}
```

### Mutation
Mutation 用于变更 Store中 的数据。
- 只能通过 mutation 变更 Store 数据，不可以直接操作 Store 中的数据。
- 通过这种方式虽然操作起来稍微繁琐一些，但是可以集中监控所有数据的变化。
```js
//定义Mutation
const store = new vuex.store ( {
	state: {
		count: 0
	},
	mutations: {
		add (state) {
			// 变更状态
			state.count++
		}
	}
})
```

```js
// 触发mutation
methods: {
	handle1() {
		// 触发mutations 的第一种方式
		this.$store.commit ('add')
	}
}
```

可以在触发 mutations 时传递参数：
```js
//定义Mutation
const store = new vuex.store ( {
	state: {
		count: 0
	},
	mutations: {
		addN(state,step) {
			// 变更状态
			state.count += step
		}
	}
})
```

```js
// 触发mutation
methods: {
	handle2() {
		// 在调用 commit 函数
		// 触发 mutation 时携带参数
		this.$store.commit ('addN',3)
	}
}
```
`this.$store.commit() `是触发 `mutations `的第一种方式，触发 mutations 的第二种方式：
```js
// 1．从vuex中按需导入 mapMutations 函数
import { mapMutations } from 'vuex'
```

```js
// 2．将指定的mutations 函数，映射为当前组件的methods函数
methods: {
	...mapMutations (['add','addN'])
}
```

### Action
Action 用于处理异步任务。
如果通过异步操作变更数据，必须通过 Action，而不能使用 Mutation，但是在 Action 中还是要通过触发 Mutation 的方式间接变更数据。
```js
// 定义Action
const store = new vuex.store ( {// ...省略其他代码
	mutations: {
		add (state) {
			state.count++
		}
	},
	actions: {
		addAsync (context){
			setTimeout(()=>{
				context.commit ('add')}, 1000)
		}
	}
})
```

```js
// 触发Action
methods: {
	handle() {
		// 触发actions 的第一种方式
		this.$store.dispatch ('addAsync')
	}
}
```

触发 actions 异步任务时携带参数：
```js
// 定义Action
const store = new vuex.store ( {
	// ...省略其他代码
	mutations: {
		addN (state, step){
			state.count +=step
		}
	},
	actions: {
		addNAsync (context, step) {
			setTimeout (() => {
				context.commit ('addN', step)
			}, 1000)
		}
	}
})
```

```js
// 触发Action
methods: {
	handle() {
		// 在调用dispatch函数，
		// 触发actions时携带参数
		this.$store.dispatch ('addNAsync', 5)
	}
}
```
`this.$store.dispatch()` 是触发 actions 的第一种方式，触发 actions 的第二种方式：
```js
// 1．从vuex中按需导入mapActions 函数
import { mapActions } from 'vuex'
```
通过刚才导入的 mapActions 函数，将需要的 actions 函数，映射为当前组件的 methods 方法：
```js
// 2．将指定的 actions函数，映射为当前组件的methods 函数
methods: {
	...mapActions (['addASync', 'addNASync'])
}
```

### Getter
Getter 用于对 Store 中的数据进行加工处理形成新的数据。
- Getter 可以对 Store 中已有的数据加工处理之后形成新的数据，类似 Vue 的计算属性。
- Store 中数据发生变化，Getter 的数据也会跟着变化。
```js
// 定义Getter
const store = new Vuex.store({
	state: {
		count : 0
	},
	getters: {
		showNum: state => {
			return '当前最新的数量是【 '+state.count +'】'
		}
	}
})
```

使用 getters 的第一种方式：
```js
this.$store.getters.名称
```

使用 getters 的第二种方式：
```js
import { mapGetters } from 'vuex'

computed: {
	...mapGetters (['showNum'])
}
```