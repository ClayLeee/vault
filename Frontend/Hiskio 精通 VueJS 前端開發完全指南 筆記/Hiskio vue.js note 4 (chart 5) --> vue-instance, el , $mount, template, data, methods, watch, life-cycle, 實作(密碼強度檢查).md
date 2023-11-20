### Vue-Instance
- class (類別) --> 用來形容某一類的東西，他有怎樣的屬性，怎樣的方法
	ex: Human class: two hands, two feet, one brain …
	給人類一個“概念”or”定義"
```js
const me = new Human() —> new 是為 Human 這個類別產生實例的方法核取方塊

const you = new Human()

me !== you —> 我是人，你也是人，但我們不一樣
```

---
建立 Vue 實例
```js
const vm = new Vue({});
```

---
### el, mount, template
-   el(string) 元素
	-   若掛載的 el 是 class,且同名的 class 有好幾個，他只會掛載到第一個上面，所以幾乎都會直接掛載在 id
	-   el 後面可以放 css 選擇器的字串, ex: el: ‘#app'
	-   或給他元素得參照, ex: el: document.getElementById(‘app')
-   mount(string) 掛載
	-   跟 el 很像 
	-   mount 括號裡面可以是 css 選擇器的字串, ex: $mount(‘#app’)
	-   或給他元素的參照, ex: $mount(document.getElementById(‘app'))
-   template(string) 模板
	-   定義掛載上去想要呈現出來的樣子

App.vue
```html
<template>
	<div id=“app”>{{msg}}</div>
</template>
```
main.js
```js
import Vue from ‘vue’
import App from ‘./App.vue'
import store from ‘./store’
import router from ‘./router’

new Vue({
	store,
	router,
	render: h => h(App)
}).$mount(‘#app')
```

---
### data
-   data 也可以宣告在 export default 外面，不管宣告在裡面和在外面的 data 會有相同的 reference (參照)
```js
<template>
	<div id=“app”>{{ x + y }}</div>
</template>

<script>
const data = {
	x: 1,
	y: 2
}

export default {
	data
}
</script>
```

-   vm.$data === data (vue 2)
```js
const data = {
	x: 1,
	y: 2
}

const vm = new Vue({
	el: ‘#app’,
	data
})

console.log(vm.$data === data) // true
```

---
### methods
-   this.count 指向 data.count
-   this.step 指向 data.step
-   this.add 指向 methods.add
-   data 與 methods 不能有同名的屬性
-   methods 不能用箭頭函式(this)，因為物件本身不是一個 scope，如果用 arrow function 會讓 this 指向 global or window，而非指向實例本身
```js
<template>
	<h1>{{count}}</h1>
	<button @click=“add”>+{{step}}</button>
</template>

<script>
export default {
	data () {
		return {
			count: 0,
			step: 1
		}
	},
	methods: {
		add () {
			this.count += this.step
			this.step += 1
		},
		add2 () {
			this.add()
		}
	}
}
</script>
```
---
### computed
-   vm.a or this.a 指向 data.a
-   vm.b or this.b 指向 data.b
-   vm.c or this.c 指向 computed 裡的 c
-   computed 裡面的屬性不應該跟 data or methods 裡面的屬性撞名
-   computed 也不能宣告成箭頭函式，用箭頭函式裡面的 computed 裡面的 this 會指向 global or window

```js
<template>
	<input type=“number” v-model=“a"/>
	+
	<input type=“number v-model=“b"/>
	=
	<span>{{c}}</span><br/>
</template>

<script>
export default {
	data () {
		return {
			a: 0,
			b: 0
		}
	},
	computed: {
		c () {
			return parseInt(this.a, 10) + parseInt(this.b, 10)
		}
	}
}
</script>
```
-   computed 除了可以宣告成 function，還可以宣告成 object，宣告成物件有兩種使用方法: get, set
-   get() —> 跟上面例子裡，原本使用 function 的 c 函式內容一樣，意思: c 這個東西是從什麼東西取出來的，當我要取用 c 這個值的時候就會呼叫 get 這個 function
-   set(val) —> 意思: 當我要設定 c 的值的時候就會呼叫 set 這個 function，例如底下的 set 給他 val = 30，a 保持原本的 8 不動，this.b 就會被算出來是 22
```js
<script>
export default {
	data () {
		return {
			a: 0,
			b: 0
		}
	},
	computed: {
		c: {
			get () {
				return parseInt(this.a, 10) + parseInt(this.b, 10)
			},
			set (val) {
				this.b = parseInt(val, 10) - parseInt(this.a, 10)
			}
		}
	}
}
</script>
```
---
### watch
—> 當偵聽的屬性改變的時候，他就會執行 watch 裡面的內容，監聽器(watch)可以監聽 data, computed 裡面的屬性，有 function 和 Object 兩種方法，兩者的效果相同
#### compared with computed
—> 計算屬性只有在他的相關依賴發生改變時才會重新求值，如果其依賴的屬性都沒有發生變化，多次進行訪問該屬性都會立即返回之前緩存的計算結果，而不必再重新執行函數

ex: 只有 c 的值改變的時候才會執行 {} 裡面的計算，如果 a 和 ｂ有改變但 c 沒有，都會直接返回之前緩存的計算結果
```js
c () {
	return parseInt(this.a, 10) + parseInt(this.b, 10)
}
```

#### function用法
-   value(val, oldVal)
```js
<template>
	<div id=“app”>
		<input v-model=“value” />
		<span>{{valueDouble}}</span>
	</div>
</template>

<script>

export default {
	data () {
		return {
			value: 0
		}
	},
	methods:{},
	computed:{
		valueDouble () {
			return parseInt(this.value, 10) * 2
		}
	},
	watch: {
		value(val, oldVal) {
			console.log(`${oldVal} -> ${val}`)
		}
	}
}
</script>
```
#### watch 的 Object 用法
-   handler(val, oldVal)
-   immediately: true —> 在初始化 vue 實例時，他就會跑有寫這個屬性的 watch
-   deep: true —> 假設監聽的是物件或是陣列的時候，他會監聽物件或是陣列底下的值有沒有改變，其中 handler 裡面的兩個參數(改變前和改變後)都指向同一個物件或陣列，他只會改變裡面的值，所以改變之後依然 val.a === oldVal.a
```js
<template>
	<div id=“app”>
		<input v-model=“value” />
	</div>
</template>

<script>
export default {
	data () {
		return {
			value: 0
		}
	},
	methods:{},
	computed:{
		valueDouble () {
			return parseInt(this.value, 10) * 2
		}
	},
	watch: {
		immediately: true,
		value: {
			handler(val, oldVal) {
				console.log(`${oldVal} -> ${val}`)
			}
		}
	}
}
</script>
```

deep: true —>
```js
<template>
	<div id=“app”>
		<input v-model=“input.a” />
	</div>
</template>

<script>
export default {
	data () {
		return {
			input: {
				a: ''
			}
		}
	},
	watch: {
		input: {
			handler(val, oldVal) {
				console.log(`${oldVal.a} -> ${val.a}`)
			}
		},
		deep: true
	}
}
</script>
```
---
### life-cycle hooks 生命週期    
![](./photo/Pasted%20image%2020221111143207.png)

Detail Notice —> beforeCreate() & created() 是唯二會在 server side rendering(在伺服器上跑 rendering)  
- beforeCreate ()
- created ()

其他都是client side rendering
- beforeMount()
- mounted() —> 很常用 mounted 來做初始的 ajax call，或者偵聽 window event，ex: getJSON, getData...
- beforeUpdate()
- updated()
- beforeDestroy()
- destroy() —> 在 mounted 偵聽 window，通常會在 destroy 清除

---
### 實作: 密碼強度檢查
computed 取代 watch 作法—>strength 是根據 password 所計算出來的字串，所以不需要寫一個 watch 去偵聽 password

```js
<template>
	<div id=“app”>
		<input type=“password” v-model=“password”/>
		<p>{{strength}}</p>
	</div>
</template>

<script>
export default {
	data () {
		return {
			password: ''
		}
	},
	computed: {
		strength () {
			let score = this.password.length
			if (/[A-Z]/.test(this.password)) score *= 1.25
			if (/[a-z]/.test(this.password)) score *= 1.25
			if (/[0-9]/.test(this.password)) score *= 1.25
			if (/[^A-Za-z0-9]/.test(this.password)) score *= 1.25
			if (score > 40) return ‘Perfect’
			if (score > 20) return ‘Great’
			if (score > 10) return ‘Good’
			return ‘Weak'
		}
	}
}
</script>
```

---
### 片尾彩蛋: 你聽過 single source of truth 嗎？
—> data 裡面是否有某個東西是不必要存在的，因為他的 value 完全是由另外一個 value 算出來的，就像下列例子裡面的 selected 一樣(他是用 a, b, c 算出來的)
```js
<template>
	<div id=“app”>
		<label>
			<input type=“checkbox” v-model=“a”/>
			<span>a</span>
		</label>
		<label>
			<imput type=“checkbox” v-model=“b”/>
			<span>b</span>
		</label>
		<label>
			<input type=“checkbox” v-model=“c”/>
			<span>c</span>
		</label>
	</div>
</template>

<script>
export default {
	data () {
		return {
			a: false,
			b: false,
			c: false
		}
	},
	computed: {
		selected () {
			return [‘a’, ‘b’, ‘c’].filter(item => this.[item])
		}
	}
}
</script>
```

tag: #Vue #mount #LifeCycle #watch #methods #computed #template