#### chap 1: Vue 組件的註冊與使用
分成兩種，global component or local component
-   global component 宣告之後，所有的組件都可以使用這個 my-component
-   local component 宣告之後，只有 new Vue 的那個組件可以使用 my-component

template
```html
<div id=“app”>
	<my-component></my-component>
</div>
```
vue
—> global component
```js
Vue.component(‘my-component’, {
	template: ‘<div>My Component</div>'
})	
```
—> local component
```js
new Vue({
	el: ‘app’,
	components: {
		‘my-component’: {
			template: ‘<div>My Component</div>'
		}
	}
})
```

#### chap 2: DOM 模板解析注意事項
html 解析後會把結果傳給 new Vue 來做使用，過程中如果瀏覽器發現一些不符合格式的宣告，像是 select 裡面瀏覽器會以為要放 option，而不是 my-component，他會把他移掉，此情況在不同的溜覽器會有不同的結果，會受到影響的 html element 有下列幾種:
-   `<select> - <option>`
-   `<ul> - <li>`
-   `<table> - <tr> - <td>`

template
```html
<div id=“app”>
	<select>
		<my-component>Test</my-component>
	</select>
</div>
```
```js
Vue.component(‘my-component’, {
	template: ‘<option>Hello</option>'
})

new Vue({
	el: ‘app'
})
```

—> 所以如果會發生上面例子的情況，可以把 html 定義在 new Vue 的 template 裡面
template
```html
<div id=“app”>
</div>
```
```js
Vue.component(‘my-component’, {
	template: ‘<option>Hello</option>
})

new Vue ({
	el: ‘#app’,
	template: ‘<div id=“app”><select><my-component>Test</my-component></select></div>'
})
```

#### chap 3: data 必須是函數
Vue 實例的 data 和 Vue 組件的 data 不一樣，Vue 組件的 data 必須是一個函數

template
```html
<div id=“app”>
	<counter></counter>
</div>
```
```js
Vue.component(‘counter’, {
	data () {
		return {
			count: 0
		}
	},
	template: `
		<div>
			<h1>{{count}}</h1>
			<button @click=“count+=1”>+1</button>
		</div>
	`
})

new Vue({
	el: ‘#app’
})
```

#### chap 4: 合體！組件組合！
-   上層 component 傳給下層 component 用 props
-   下層 component 傳給上層 component 用 emit

template
```html
<div id=“app”>
	<todo-list></todo-list>
</div>
```
```js
Vue.component(’todo-item’, {
	props: [‘label']
	template: `<li>{{label}}</li>`
})

Vue.component(’todo-input’, {
	data () {
		return {
			text: ''
		}
	},
	methods: {
		submit () {
			this.$emit(‘input’, this.text)
			this.text= ''
		}
	}
	template: `
		<form @submit.prevent="submit">
			<input type=“text” v-model=“text”/>
			<button type=“submit”/>Submit</button>
		</form>
	`
})

Vue.component(’todo-list’, {
	data () {
		return {
			todos: [‘a’, ‘b’, ‘c’]
		}
	},
	methods: {
		addTodo (text) {
			this.todos.push(text)
		}
	}
	template: `
		<div>
			<todo-input @input="addTodo">
				<ul>
					<todo-item v-for=“todo in todos” :label="todo"></todo-item>
				</ul>
			</todo-input>
		</div>
	`
})

new Vue ({
	el: ‘#app'
})
```

#### chap 5: 話不要說死，組件也別寫死。動態組件
component 加上 :is 可以動態使用組件 —> lessons or apply

template
```html
<div id=“app”>
	<button @click="content=‘lessons'”>Lessons</button>
	<button @click=“content=‘apply'">Apply</button>
	<br/>
	<component :is=“content”></component>
</div>
```
```js
Vue.component(‘apply’, {
	template: `
		<form>
			<textarea></textarea>
			<br/>
			<button>Submit</button>
		</form>
	`
})

Vue.component(‘lesson’, {
	template: `
		<ul>
			<li>Vue 課程</li>
			<li>React 課程</li>
			<li>Angular 課程</li>
		</ul>
	`
})

new Vue ({
	el: ‘#app’,
	content: ‘lessons'
})
```

#### chap 6: 看不見但依舊存在的 keep-alive
template
```html
<div id=“app”>
	<button @click="content=‘lessons'”>Lessons</button>
	<button @click=“content=‘apply'">Apply</button>
	<br/>
	<keep-alive>
		<component :is=“content”></component>
	</keep-alive>
</div>
```
```js
Vue.component(‘apply’, {
	template: `
		<form>
			<textarea></textarea>
			<br/>
			<button>Submit</button>
		</form>
	`
})

Vue.component(‘lesson’, {
	template: `
		<ul>
			<li>Vue 課程</li>
			<li>React 課程</li>
			<li>Angular 課程</li>
		</ul>
	`
})

new Vue ({
	el: ‘#app’,
	content: ‘lessons'
})
```

#### chap 7: 組件姓名學
-   Vue 支援的命名 —> 最後會被轉成 kebab-case
	-   ‘my-first-component’ —> kebab-case 串燒
	-   ‘myFirstComponent’ —> camelCase 駝峰式
	-   ‘MyFirstComponent’ —> PascalCase
-   使用限制
	-   使用 html 當成 vue 的模板時裡面只能使用 kebab-case
	-   在 template 則不受限制，三種命名方式都可以使用
	-   在 template 裡面還可以使用 self closing: <my-first-component/>

template
```html
<div id=“app”>
	<my-first-component></my-first-component>
</div>
```
```js
Vue.component(‘my-first-component’, {
	template: ‘<h1>Hello</h1>
})

new Vue ({
	el: ‘#app'
})
```

#### chap 8: 實作: 結合 axios 製作 會員登入
使用 ajax 的時候最好加上 loading 讓使用者能夠辨認狀態

template
```html
<div id=“app”>
	<div>
		<button @click=“component=‘login-form’”>Login Form</button>
		<button @click=“component=‘user-check’”>User Check</button>
	</div>
	<hr/>
	<keep-alive>
		<component :is=“component”></component>
	</keep-alive>
</div>
```
```js
import lodash from ‘...'

const API = ‘…'

Vue.component(‘user-check’, {
	data () {
		username: ‘’,
		exists: false
	},
	methods: {
		submit () {
			axios.get(`${API}/exists/${this.username}`)
				.then(response => {
					this.exists = response.data.exists
				})
			}
		}
	},
	watch: {
		username () {
			this.submitDebounced()
		}
	},
	// lodash used
	created () {
		this.submitDebounced = _.debounce(this.submit, 500)
	},
	template: `
		<div>
			<label>Username</label>
			<input type=“text” v-model="username"/>
			<br/>
			<span>{{exists}}</span>
		</div>
	`
})

Vue.component(‘login-form’, {
	data () {
		return {
			username: ‘’,
			password: ‘’,
			loggedUser: null,
			error: ‘’,
			loading: false
		}
	},
	methods: {
		submit () {
			this.loading = true
			axios.post(`${API}/login`, {
				username: this.username,
				password: this.password
			}).then(response => {
				this.loading = false
				if (response.data.success) {
					this.loggedUser = response.data.name
				} else {
					this.error = ‘Incorrect username/password'
				}
			})
		}
	},
	watch: {
		username () {
			this.error: ''
		},
		password () {
			this.error: ''
		}
	},
	template: `
		<div>
			<h1 v-if="loggedUser">Welcome! {{loggedUser}}</h1>
			<form v-else @submit.prevent="submit">
				<label>Username</label>
					<input type=“text” v-model=“username” :disabled="loading"/>
					<br/>
				<label>Password</label>
					<input type=“password” v-model=“password” :disabled="loading"/>
					<br/>
				<button :disabled="loading">Submit</button>
			</form>
			<br/>
			<span v-if=“error”>{{error}}</span>
		</div>
	`
})

new Vue({
	el: ‘#app’,
	data: {
		component: ‘login-form'
	}
})
```

#### 片尾彩蛋: 談談 callback 與 promise
success 的 function 就是 callback
callback function 是一個非同步的呼叫

-   非同步呼叫方法介紹
	-   getJSON
	-   ajax
	-   Promise —> 可以用 .then 來做串接
	-   es8 async / await —> 把 Promise 當成同步呼叫使用

getJSON
```js
$.getJSON(‘https://randomuser.me/api', (data) => {
	console.log(data)
})
```

ajax
```js
$.ajax({
	url: 'https://randomuser.me/api’,
	dataType: ‘json’,
	success: function(data) {
		console.log(data)
	}
})
```

- error1: 下面例子會得到 undefined 的結果 

原因是雖然 getJSON 裡面有 return，但是 return 是給他自己的 function，並不是 return 給 getRandomEmail()
```js
function getRandomEmail () {
	$.getJSON(‘https://randomuser.me/api', (data) => {
		return data.results[0].email
	})
}

const email = getRandomEmail()
```
- error2: 若是改成以下例子也還是會得到 undefined

因為非同步執行完成之前，也就是 email 得到值之前， return email 就會先被執行到了，所以會得到 undefined
所以不要把同步和非同步混在一起使用
```js
function getRandomEmail () {
	let email
	$.getJSON(‘https://randomuser.me/api', (data) => {
		email = data.results[0].email
	})
	return email
}

const email = getRandomEmail()
```

解決方法:
```js
function getRandomEmail (callback) {
	$.getJSON(‘https://randomuser.me/api', (data) => {
		callback(data.results[0].email)
	})
}

const emails = [] 

getRandomEmail((email) => {
	emails.push(email)
	getRandomEmail((email) => {
		emails.push(email)
		getRandomEmail((email) => {
			emails.push(email)
			console.log(emails)
		})
	})
})
```

Promise 取得多個 emails，避免使用上面的巢狀結構(callback hell)
```js
function getRandomEmail () {
	return new Promise((resolve, reject) => {
		$.getJSON(‘https://randomuser.me/api', (data) => {
			resolve(data.results[0].email)
		})
	})
}

const emails = [] 

getRandomEmail()
	.then((mail) => {
		emails.push(email)
		return getRandomEmail()
	})
	.then((mail) => {
		emails.push(email)
		return getRandomEmail()
	})
	.then((mail) => {
		emails.push(email)
		console.log(emails)
	})
```

async / await —> 如果 function 是回傳一個 Promise 的時候，可以在 async 裡面去 await 這個 function，這樣的用法會讓非同步呼叫的使用非常接近同步呼叫: await 執行完才會去執行 push，然後一行一行執行下去
```js
function getRandomEmail () {
	return new Promise((resolve, reject) => {
		$.getJSON(‘https://randomuser.me/api', (data) => {
			resolve(data.results[0].email)
		})
	})
}

const emails = [] 

async function getEmails () {
	let email
	emails.push(await getRandomEmail())
	emails.push(await getRandomEmail())
	emails.push(await getRandomEmail())
	console.log(emails)
}

getEmails()
```

tag: #Vue #Component #keep-alive #Ajax #Promise #Async #Await #GetJson 