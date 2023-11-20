#### chap 1: 上層傳來的 prop
template
```html
<div id=“app”>
	<my-component message=“hello” test="test"></my-component>
</div>
```
```js
Vue.component(‘my-component’, {
	props: [‘message’, ’test'],
	template: ‘<h1>{{message}}{{test}}</h1>
})

new Vue({
	el: ‘#app'
})
```

prop 在 HTML 裡面不能用 camelCase: myMessage —> my-message
如果是在字串模板(template) 裡面的話就可以用 camelCase 了，跟組件裡面的規則一樣
template
```html
<div id=“app”>
	<my-component my-message=“hello”></my-component>
</div>
```
```js
Vue.component(‘my-component', {
	props: [‘myMessage’],
	template: ‘<h1>{{myMessage}}</h1>'
})

new Vue({
	el: ‘#app’,
	template: `
		<div>
			<my-component myMessage=“hello”></my-component>
		</div>
	`
})
```

#### chap 2: 動態綁定 prop
Object 分別綁定屬性值給 component 當做 props

template
```html
<div id=“app”>
	<post v-for=“post in posts” :text=“post.text” :author="post.author"></post>
</div>
```
```js
new Vue({
	el: ‘#app’,
	data: {
		posts: [
			{text: ‘hello’, author: ‘John’},
			{text: ‘world’, author: ‘Jack’}
		]
	},
	components: {
		post: {
			props: [‘author’, ’text’],
			template: `
				<div>
					{{author}}: {{text}}
				</div>
			`
		}
	}
})
```

特殊用法: 直接綁定整個物件給把物件內所有屬性指定給 component
template
```html
<div id=“app”>
	<post v-for=“post in posts” v-bind=“post”></post>
</div>
```

動態綁定的另外一種意思，把 number 指定為 1
template
```html
<div id=“app”>
	<count :number=“1”></count>
</div>
```
```js
new Vue({
	el: ‘#app’,
	components: {
		props: [’number’],
		count: {
			template: ‘<div>{{number + 1}}</div>'
		}
	}
})
```

#### chap 3: 單向數據流
vue 組件裡面的 prop 是一個單向的數據流，由上層往下層
點下層按鈕上層不會改變，點上層按鈕下層會一起變成 11

template
```html
<div id=“app”>
	<h1>{{count}}</h1>
	<button @click=“count+=1”>+</button>
	<counter :count=“count”></counter>
</div>
```
```js
new Vue({
	el: ‘#app’,
	data: {
		count: 10
	},
	components: {
		counter: {
			props: [‘count’],
			template: `
				<div>
					<h2>{{count}}</h2>
					<button @click=“count+=1”>+</button>
				</div>
			`
		}
	}
})
```

如果要讓上下層不互相影響，又只想用單向數據流的方法
template
```html
<div id=“app”>
	<h1>{{count}}</h1>
	<button @click=“count+=1”>+</button>
	<counter :start=“count”></counter>
</div>
```
```js
new Vue({
	el: ‘#app’,
	data: {
		count: 10
	},
	components: {
		counter: {
			props: [’start’],
			data () {
				return {
					count: this.start
				}
			}
		}
	},
	template: `
		<div>
			<h2>{{count}}</h2>
			<button @click=“count+=1”></button>
		</div>
	`
})
```

#### chap 4: prop 驗證
-   props 裡面可用屬性:
	-   type —> 型別
	-   default —> 預設值
	-   validator —> 檢驗傳入參數
-   props 發生在 counter 這個組件建立之前，所以 props 裡面不能夠使用 data 或 methods 裡面的 param 和 function

template
```html
<div id=“app”>
	<counter :start=“10”></counter>
</div>
```
```js
Vue.component(‘counter’, {
	props: {
		start: {
			type: Number,
			default () {
				return app.getSetting()
			},
			validator (value) {
				return value <= 10
			}
		}
	},
	data () {
		return {
			count: this.start
		}
	},
	template: `
		<div>
			<h1>{{count}}</h1>
			<button @click=“count+=1">
		</div>
	`
})

new Vue({
	el: ‘#app'
})
```

tag: #Vue #Props 