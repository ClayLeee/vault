#### chap 1: 關於 vuex
-   什麼時候該導入 Vuex:
	-   components 很少互相溝通, 上下傳遞其實就很夠: 不用 vuex
	-   components 會互相溝通, 但情境很固定, 數量也很少: event bus
	-   components 常會跨結構的傳遞狀態: vuex
	-   需要全域狀態管理: vuex
-   vuex 可以做到的事情: 透過 global state 讓 components 之間能夠溝通
-   vuex 和 event bus 的差別
	-   event bus —> 發出 emit 修改一個 component 的狀態
	-   vuex —> 發出 mutation 修改全部 component 共用的狀態

event bus —> event bus 可以做到結構上遠距離的跨組件溝通    
![](./photo/Pasted%20image%2020221118140634.png)

vuex —> 希望可以把狀態統一管理的時候可以使用    
![](./photo/Pasted%20image%2020221118140656.png)

#### chap 2: 引入 vuex
App.vue
```js
<template>
	<div id=“app”>
		<h1>{{ count }}</h1>
		<button @click=“addCount”>Add</button>
	</div>
</template>

<script>
export default {
	computed: {
		count () {
			return this.$store.state.count
		}
	},
	methods: {
		addCount () {
			this.$store.commit(‘addCount')
		}
	}
}
</script>
```
store.js
```js
import Vue from ‘vue’
import Vuex from ‘vuex’

Vue.use(Vuex)

const store = new Vuex.Store({
	state: {
		count: 0
	},
	mutations: {
		addCount (state) {
			state.count += 1
		}
	}
})

export default store
```
main.js
```js
import Vue from ‘vue’
import App from ‘./App.vue’
import store from ‘./store’

new Vue({
	el: ‘#app’,
	store,
	render: h => h(App)
})
```

#### chap3: 注入組件的 $store
也可以不用在 main.js 裡面引用 store, 改成在 App.vue 裡面引用

但是如果在上層引用的話, 有幾點好處:
-   如果在 main.js 裡面引用的話，會注入 this.$store 到每一個 components 裡面，而不用每個 component 都引用一次
-   在上層引用才能使用 mapState, mapMutations… 等函式

App.vue
```js
<template>
	<div id=“app”>
		<h1>{{ count }}</h1>
		<button @click=“addCount”>Add</button>
	</div>
</template>

<script>
import store from ‘./store’

export default {
	computed: {
		count () {
			return store.state.count
		}
	},
	methods: {
		addCount () {
			store.commit(‘addCount')
		}
	}
}
</script>
```
main.js
```js
import Vue from ‘vue’
import App from ‘./App.vue’

new Vue({
	el: ‘#app’,
	render: h => h(App)
})
```
App.vue
```js
<template>
	<div id=“app”>
		<h1>{{ count }}</h1>
		<button @click=“addCount”>Add</button>
	</div>
</template>

<script>
import { mapState, mapMutations } from ‘vuex’

export default {
	computed: {
		...mapState([‘count’])
	},
	methods: {
		...mapMutations([‘addCount’])
	}
}
</script>
```

#### chap 4: 存放狀態的 State
- State 是 Store 裡面用來存放狀態的物件
- 要用到的屬性就必須先定義在 state 裡面, 就算是空的也沒有關係
- 如果是沒有預先定義的, 就必須要使用 Vue.set() 去設置
- 在 component 裡面要取用 state 的話使用 computed

store.js
```js
import Vue from ‘vue’
import Vuex fr om ‘vuex’

Vue.use(Vuex)

const store = new Vuex.Store({
	state: {
		count: 0,
		username: ‘’,
		list: []
	},
	mutations: {
		addCount (state) {
			state.count += 1
			Vue.set(state, ‘loading’, false)
		}
	}
})

export default store
```

App.vue —> 使用 computed 的方式
mapState 可以傳入陣列和物件
陣列 —>
```js
<template>
	<div id=“app”>
		<h1>{{ count }}</h1>
		<button @click=“addCount”>Add</button>
	</div>
</template>

<script>
import { mapState } from ‘vuex’

export default {
	data () {
		return {
			localCount: 0
		}
	},
	computed: {
		myCount () {
			return this.localCount
		},
		…mapState([
			‘count’,
			‘username’,
			‘list'
		])
	},
	methods: {
		addCount () {
			this.$store.commit(‘addCount')
		}
	}
}
</script>
```
物件 —> 
key: value, function, arrow function 都可以
如果 mapState 使用物件的方法裡面有用到 this 的 function 就不能用 arrow function
```js
…mapState({
	storeCount: ‘count’,
	storeCount: state => state.count,
	totalCount (state) {
		return this.localCount + state.count
	}
})
```

#### chap 5: 變更狀態的 Mutations
要改變 store 裡面的 state 只能透過 mutations

如果有沒有在 state 裡面宣告的值卻想要 mutation 的話，使用 Vue.set(state, ‘paramName’, paramValue)
```js
mutations: {
	addCount (state) {
		state.count += 1
		Vue.set(’state’, ‘loading’, true)
	}
}
```
如果 state 是巢狀的，要使用的參數(paramName) 不存在 state 裡面時，可以使用以下兩種用法:
-   Vue.set(state.stateName, ‘paramName’, paramValue)
-   ES6 解構用法 —> state.stateName = { …state.stateName, paramName: paramValue }
    
不能夠用 state.editor.loading = true
```js
state: {
	count: 0,
	editor: {
		text: ''
	}
},
mutations: {
	addCount (state) {
		Vue.set(state.editor, ‘loading’, true)
		state.editor = { …state.editor, loading: true }
	}
}
```

commit mutations 可以帶有第二個參數，在 store.js 裡面定義的 mutations 則多加一個參數
App.vue
```js
methods: {
	addCount () {
		this.$store.commit(‘addCount’, 2)
	}
}
```
store.js
```js
mutations: {
	addCount (state, payload) {
		state.count = state.count + payload
	}
}
```
commit mutations 也可以傳一個物件
App.vue
```js
methods: {
	addCount () {
		this.$store.commit({
			type: ‘addCount’,
			step: 2
		})
	}
}
```
store.js
```js
mutations: {
	addCount (state, payload) {
		state.count = state.count + payload.step
	}
}
```

mapMutations 跟 mapState 一樣，可以傳入物件和陣列
注意在例子中 @click 後面的 addCount 也可以傳入 payload
App.vue
```js
<template>
	<div>
		<h1>{{ count }}</h1>
		<button @click=“addCount(2)”>Add</button>
	</div>
</template>

<script>
import { mapState, mapMutations } from ‘vuex’

export default {
	computed: mapState([‘count’]),
	methods: mapMutations([‘addCount'])
}
</script> 
```

store.js
```js
mutations: {
	addCount (state, payload) {
		state.count = state.count + payload
	}
}
```
mutations 傳入物件
```js
<tamplate>
	<div>
		<h1>{{ count }}</h1>
		<button @click=“add(2)”></button>
	</div>
</template>

<script>
import { mapState, mapMutations } from ‘vuex’

export default {
	computed: mapState([’count’]),
	methods: mapMutations({
		add: ‘addCount'
	})
}
</script>
```

mutations 裡面只能做同步操作，不能夠使用非同步操作，如果要用非同步操作的話要移到 actions 裡面
```js
mutations: {
	addCount (state, payload) {
		// 同步
		state.count = state.count + payload
		// 非同步
		fetch(url).then(() => {
			state.count
		})
	}
}
```

#### chap 6: 取得狀態的 Getters
getters 其實就是 store 裡面的 computed
-   如果 todos 在很多地方都會用到, 可以寫在 getters 裡面

store.js
```js
const store = new Vuex.Store({
	state: {
		todos: []
	},
	getters: {
		itemsNotDone (state) {
			return state.todos.filter(item => !item.done).length
		}
	}
})
```
App.vue
```js
import { mapState, mapGetters } from ‘vuex’

export default {
	computed: {
		…mapState([’todos’]),
		…mapGetters([‘itemsNotDone'])
	}
}
```
- 如果 todos 只用在一個 component，可以直接在 component 裡面取值
App.vue
```js
computed: {
	…mapState([’todos’]),
	itemsNotDone () {
		return this.todos.filter(item => !item.done).length
	}
}
```

getters 的第二個參數: itemsDone (state, getters)
```js
getters: {
	itemsNotDone (state) {
		return state.todos.filter(item => !item.done).length
	},
	itemsDone (state, getters) {
		return state.todos.length - getters.itemsNotDone
	}
}
```

getters 也可以回傳一個 function
store.js
```js
getters: {
	itemsWithID: state => id => state.todos.filter(item => item.id === id）
}
```
App.vue
```js
export default {
	computed: {
		…mapState([’todos’]),
		…mapGetters([‘itemsWithID'])
	},
	methods: {
		test () {
			this.itemsWithID(2)
		}
	}
}
```

getters 也跟 state 和 mutations 一樣，可以傳入物件和陣列
```js
computed: {
	…mapGetters({
		items: ‘itemsWithID'
	})
},
methods: {
	test () {
		this.items(2)
	}
}
```

#### chap 7: 發出指令的 Actions
mutations 用來做同步的操作，actions 則用來做非同步的操作
mutations 對應的是 commit，actions 對應的是 dispatch
actions 不能修改 state，他也必須依賴發出 mutations 才能修改 state

actions function 的參數 context 幾乎和 store 一模一樣，但是他比 store 還多了一點東西

store.js
```js
import Vue from ‘vue’
import Vuex from ‘vuex’

Vue.use(Vuex)

const url = ‘’

const store = new Vuex.Store({
	state: {
		todos: []
	},
	mutations: {
		setTodos (state, todos) {
			state.todos = todos
		}
	},
	actions: {
		fetchTodos (context) {
			fetch(`${url}/todos`).then(res => res.json()).then(todos => {
				context.commit(’setTodos’, todos)
			})
		}
	}
})

export default store
```
App.vue
```js
export default {
	mounted () {
		this.$store.dispatch(‘fetchTodos')
	}
}
```

解構用法 —> 把 context 解構，只要裡面的 commit
[在函式傳入參數定義中使用](https://eyesofkids.gitbooks.io/javascript-start-from-es6/content/part4/destructuring.html) —> a: {b: xxx} —> function ({b: xxx}) {} or function (a) { a.b }
```js
function (context) {
	const {a, b, c, d, e, f} = context
}
```
store.js
```js
actions: {
	fetchTodos ({commit}) {
		fetch(`${url}/todos`).then(res => res.json()).then(todos => {
			commit(’setTodos’, todos)
		})
	}
}
```

跟 mutations 一樣，actions 也吃第二個參數
在 actions 裡面可以做多次的 commit，因為 commit 是同步的

App.vue
```js
mounted () {
	this.$store.dispatch(‘fetchTodos’, {id: 2})
}
```
store.js
```js
const store = new Vuex.Store({
	state: {
		loading: false,
		todos: []
	},
	mutations: {
		setTodos (state, todos) {
			state.todos = todos
		},
		setLoading (state, loading) {
			state.loading = loading
		}
	},
	actions: {
		fetchTodos ({commit}, payload) {
			commit(’setLoading’, true)
			fetch(`${url}/todos`).then(res => res.json()).then(todos => {	
				commit(’setTodos’, todos)
				commit(’setLoading’, false)
			})
		}
	}
})
```

mapActions 是給 methods 用的, 也一樣可以傳入陣列或物件

App.vue
```js
export default {
	mounted () {
		this.fetchTodos({id: 2})
	},
	methods: mapActions([‘fetchTodos'])
}
```

actions 裡面可以連接好幾個 actions，可以在 actions 裡面發出其他 actions

store.js
```js
actions: {
	fetchTodos({commit, dispatch}, payload) {
		commit(’setLoading’, true)
		fetch(`${url}/todos`).then(res => res.json()).then(todos => {
			commit(’setTodos’, todos)
			commit(’setLoading’, false)
			dispatch(‘fetchUserIndo')
		})
	},
	fetchUserIndo () {}
}
```

發出 actions 之後想做一些非同步的操作時，actions 可以回傳一個原生的 Promise，ex: axios, fetch
$.ajax() 是假的 Promise

store.js
```js
actions: {
	fetchTodos({commit, dispatch}, payload) {
		return new Promise(resolve => {
			commit(’setLoading’, true)
			fetch(`${url}/todos`).then(res => res.json()).then(todos => {
				commit(’setTodos’, todos)
				commit(’setLoading’, false)
				dispatch(‘fetchUserIndo’)
				reslove()
			})
		})
	}
}
```

--> fetch 本身也是一種 Promise，所以可以再簡化
```js
actions: {
	fetchTodos({commit, dispatch}, payload) {
		commit(’setLoading’, true)
		return fetch(`${url}/todos`).then(res => res.json()).then(todos => {
			commit(’setTodos’, todos)
			commit(’setLoading’, false)
			dispatch(‘fetchUserIndo’)
			reslove()
		})
	}
}
```

#### chap 8: 模組劃分: Modules
可以把專案裡面的狀態都集中到 store 裡面, 在 store 裡面可以把不同的區塊拆分成不同的 store 物件，然後在建立實例的時候用 modules 定義

getters 的參數可以放 rootState 和 rootGetters 去取得實例(全域)的 state 和 getters，例如要存取別人的 state 就用 rootState.member.username

actions 的參數 context 裡面有 commit, dispatch, state, getters, rootState, rootGetters

store.js
```js
import Vue from ‘vue’
import Vuex from ‘vuex’

Vue.use(Vuex)

const todos = {
	state: {
		list: []
	},
	mutations: {
		setList(state, payload) {}
	},
	getters: {
		getLength(state, getters, rootState, rootGetters) {
			rootState.member.username
		}
	},
	actions: {
		fetchList (context) {
			const {commit, dispatch, state, getters, rootState, rootGetters} = context
		}
	}
}

const member = {
	state: {
		username: ‘’,
		rank: 0
	},
	mutations: {},
	actions: {}
}

const store = new Vuex.Store({
	modules: {
		todos: todos,
		member: member
	}
})

export default store
```
App.vue
```js
<script>
export default {
	computed: {
		todosList () {
			return this.$store.state.todos.list
		}
	}
}
</script>
```

如果 actions 和 mutations 有相同的 function 名，則 dispatch 或 commit 的時候會同時呼叫同名的 function
為了避免上述情況:
	-   可以把 function 的名字取的不一樣
	-   在物件裡面寫這一個屬性 —> namespaced: true
  
取名不同 —> 
store.js
```js
const todos = {
	actions: {
		fetchTodosList () {}
	}
}

const member = {
	actions: {
		fetchMemberList () {}
	}
}
```
App.vue
```js
export default {
	mounted () {
		this.$store.dispatch(‘fetchTodosList')
	}
}
```
namespaced: true —> 
store.js
```js
const todos = {
	namespaced: true,
	actions: {
		fetchList () {}
	}
}

const member = {
	namespaced: true,
	actions: {
		fetchList () {}
	}
}
```

App.vue
```js
export default {
	mounted () {
		this.$store.dispatch(‘todos/fetchList’)
		this.$store.dispatch(‘member/fetchList')
	}
}
```
如果想在不同物件的 actions 裡面 commit 其他物件的 mutations，因為沒有 rootCommit 跟 rootDispatch，所以用以下方法
commit 時設定第三個參數: {root: true} 相當於從 store 發出 mutations
要用 ‘object/mutations’ 這種名字的話就必須要加上 namespaced: true，流程 —> 設定第三個參數 {root: true} —> 他就會去找 object 裡面的 namespaced 裡面的 mutations
```js
const todos = {
	namespaced: true,
	state: {
		list: []
	},
	mutations: {
		setList(state, payload) {}
	}
}

const member = {
	namespaced: true,
	state: {
		username: ‘’,
		rank: 0
	},
	mutations: {
		setList () {}
	}
	actions: {
		fetchList ({commit}) {
			// commit(‘object/mutations’, payload, {root: true})
			commit(’todos/setList’, [1, 2, 3, 4], {root: true})
			// commit 自己的 mutations setList
			commit(’setList’, [1, 2, 3, 4])
			// 下列等同上列
			commit(‘member/setList’, [1, 2, 3, 4], {root: true})
		}
	}
}
```
如果使用 modules 想用 mapState, mapGetters, mapMutations, mapActions，可以在第一個參數傳入 namespaced，modules 裡面必須要有 namespaced: true 這個屬性

…mapState(namespaced, [...])
App.vue
```js
<script>
import { mapState } from ‘vuex’

export default {
	computed: {
		…mapState(’todos’, [
			‘list’,
			’text’,
			‘input'
		]),
		…mapState(‘member’, [
			‘username’,
			‘ranking'
		])
	}
}
</script>
```

#### chap 9: 專案結構
使用 vuex 三大原則:
-   app 層級的狀態應該集中到 store 裡面
-   mutations 是改變 state 的唯一方法, 並且是同步的
-   非同步操作都放在 actions 裡面


src
- api —> 放跟後端溝通的程式(抓 api)
        shop.js 
- components —> 放視覺組件
        App.vue
        ProductList.vue
        ShoppingCart.vue
- store
	- modules
            cart.js —> 包含 state, getters, actions, mutations
            products.js
        actions.js —> 放跨模組的 actions
        index.js —> 定義 store
        mutation-types.js —> 定義常數名
    app.js —> 把 @/components/App.vue 匯入

@/app.js
```js
import Vue from ‘vue’
import App from ‘./components/App’
import store from ‘./store’

new Vue({
	el: ‘#app’,
	store,
	render: h => h(App)
})
```
@/store/index.js
```js
import Vue from ‘vue’
import Vuex from ‘vuex’
import * as actions from ‘./actions’
import cart from ‘./modules/cart’
import products from ‘./modules/products’
import createLogger from ‘../../../src/plugins/logger’

Vue.use(Vuex)

const debug = process.env.NODE_ENV !== ‘production’  

export default new Vuex.Store({
	actions,
	modules: {
		cart,
		products
	},
	strict: debug,
	plugins: debug ? [createLogger()]: []
})
```
@/store/actions.js
```js
import * as types from ‘./mutations-types’

export const addToCart = ({ commit }, product => {
	if (product.inventory > 0) {
		commit(type.ADD_TO_CART, {
			id: product.id
		})
	}
})
```
@/store/mutation-types.js —> 要用名字的時候保證不會打錯XD
```js
export const ADD_TO_CART = ‘ADD_TO_CART’

export const SET_CART_ITEMS = ‘SET_CART_ITEMS’

export const SET_CHECKOUT_STATUS = ‘SET_CHECKOUT_STATUS’

export const RECEIVE_PRODUCTS = ‘RECEIVE_PRODUCTS’
```
@/store/modules/carts.js
```js
import * as types from ‘../mutation-types’

const state = {}

const getters = {}

const actions = {}

const mutations = {
	[types.ADD_TO_CART] (state, { id }) {
		state.checkoutStatus = null
		const record = state.added.find(product => product.id === id)
		if (!record) {
			state.added.push({
				id,
				quantity: 1
			})
		} else {
			record.quantity++
		}
	},
	[types.SET_CART_ITEMS] (state, { items }) {
		state.added = items
	}
}
```

#### chap 10: 表單處理
第一種方式 ```<input type=“text” :value=“text” @input=“val => text = val”/>```

App.vue
```js
<template>
	<div>
		<input type=“text” :value=“text” @input=“setText"/>
	</div>
</template>

<script>
import { mapState, mapMutations } from ‘vuex’

export default {
	computed: {
		…mapState([’text'])
	},
	methods: {
		…mapMutations([’setText'])
	}
}
</script>
```
store.js
```js
import Vue from ‘vue’
import Vuex from ‘vuex’

Vue.use(Vuex)

const store = new Vuex.Store({
	state: {
		text: ''
	},
	mutations: {
		setText (state, val) {
			state.text = val
		}
	}
})
```

第二種方式 ```<input type=“text” v-model=“text”/>```
App.vue
```js
<template>
	<div>
		<input type=“text” v-model=“text”/>
	</div>
</template>

<script>
export default {
	computed: {
		text: {
			get () {
				return this.$store.state.text
			},
			set (val) {
				this.$store.commit(’setText’, val)
			}
		}
	}
}
</script>
```
store.js
```js
import Vue from ‘vue’
import Vuex from ‘vuex’

Vue.use(Vuex)

const store = new Vuex.Store({
	state: {
		text: ''
	},
	mutations: {
		setText (state, val) {
			state.text = val
		}
	}
})
```

#### chap 11: 幫 HiSKIO 寫一個購物車(HiSKIO 要買的話給你們 99 折優惠)
main.js
```js
import Vue from ‘vue’
import App from ‘./App.vue’
import store from ‘./store’

new Vue({
	el: ‘#app’,
	store,
	render: h => h(App)
})
```
store.js
```js
import Vue from ‘vue’
import Vuex from ‘vuex’

Vue.use(Vuex)

const LESSONS_URL = ‘...'

const store = new Vuex.Store({
	state: {
		lessons: [],
		cart: []
	},
	getters: {
		lessonsInCart: state => (
			state.cart.length
		),
		priceInCart: state => (
			state.cart.reduce((acc, lesson) => (
acc + lesson.price
		), 0)
	),
		isLessonInCart: state => lesson => (
			state.cart.findIndex(({id}) => (
				id === lesson.id
			)) !== -1
		)
	},
	mutations: {
		setLessons (state, lessons) {
			state.lessons = lessons
		},
		addToCart (state, lesson) {
			state.cart = state.cart.comcat(lesson)
			// 同上
			// state.cart = […state, lesson]
			// 同上
			// state.cart.push(lesson)
		}
	},
	actions: {
		fetchLessons ({commit}) {
			fetch(LESSONS_URL)
				.then(res => res.json())
				.then(res => {
					commit(’setLessons’, res.courses)
				})
		}
	}
})

export default store
```
App.vue
```js
<template>
	<div>
		<Header/>
		<LessonList/>
	</div>
</template>

<script>
import LessonList from ‘./LessonList.vue’
import Header from ‘./Header.vue'

export default {
	components: {
		Header,
		LessonList
	}
}
</script>
```
LessonList.vue
```js
<template>
	<div>
		<LessonItem
			v-for=“lesson in lessons”
			:lesson=“lesson"
		/>
	</div>
</template>

<script>
import { mapState, mapActions } from ‘vuex’
import LessonItem from ‘./LessonItem'

export default {
	components: {
		LessonItem
	},
	computed: {
		…mapState([‘lessons'])
	},
	methods () {
		…mapActions([‘fetchLessons'])
	},
	mounted () {
		this.fetchLessons()
	}
}
</script>
```
LessonItem.vue
```js
<template>
	<div>
		<div class=“cover” :style=“coverStyle”/>
		<span class=“title”>{{lesson.title}}</span>
		<span class=“price”>{{lesson.price}}</span>
		<span class=“teachers”>{{teachers}}</span>
		<div :class=“btnClass” @click=“addToCart(lesson)”>{{btnLabel}}</div>
	</div>
</template>

<script>
import { mapGetters, mapMutations } from ‘vuex’

export default {
	props: [‘lesson’],
	computed () {
		…mapGetters([‘isLessonInCart’]),
		inCart () {
			return this.isLessonInCart(this.lesson)
		},
		btnClass () {
			return {
				buy: true,
				bought: this.inCart
			}
		},
		btnLabel () {
			this.inCart
				? ‘已加入購物車'
				: ‘加入購物車'
		},
		teachers () {
			return this.lesson.teachers
				.map(t => t.username)
				.join(‘, ')
		},
		coverStyle () {
			return {
				backgroundImage: `url(‘${this.lesson.cover}’)`
			}
		}
	},
	methods: {
		…mapMutations([‘addToCart'])
	}
}
</script> 

<style scoped>
.buy {
	clear: both;
	text-align: center;
	padding: 6px;
	font-size: .7em;
	background-color: #dee;
	cursor: pointer;
}

.buy:hover {
	background-color: #cee;
}

.buy.bought {
	background-color: #ccc;
}
</style>
```
Header.vue
```js
<template>
	<div class=“header">
		<span class=“cart”>
			<span>購物車內: </span>
			<span class=“lessons”>{{lessons}}</span>
			<span> 套課程, 共 </span>
			<span class=“price”>${{price}}</span>
		</span>
	</div>
</template>


<script>
import { mapGetters } from ‘vuex’


export default {
	computed: {
		…mapGetters({
			lessons: ‘lessonsInCart’,
			price: ‘priceInCart'
		})
	}
}
</script>
```

tag: #Vue #Vuex