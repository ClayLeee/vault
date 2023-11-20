關於 Vue —> Vue 遵循 MVVM 架構: Model, View, View Model，藉由這三者之間的交互關係來進行開發    
![](./photo/Pasted%20image%2020221108092836.png)    
![](./photo/Pasted%20image%2020221108092851.png)

---
Hello World!
```js
<template>
	<div id=“app”>
		{{message}}
	</div>
</template>

<script>
export default {
	data () {
		return {
			message: ‘Hello World!'
		}
	}
}
</script>
```
---
Vue 是相對於 “命令式” 渲染的 “聲明式(宣告式)” 渲染 —> 按下 button 之後，add() 讓 count + 1，當 count 改變的時候，在 
```html
<h1>
``` 
裡面聲明的 count 也會隨之改變
- 命令式 (jQuery) —> 一個口令一個動作
	```html
	<div id=“counter”>
		<h1>0</h1>
		<button>+1</button>
	</div>
	```
	```js
	$(‘button’).click(() => {
		let count = parseInt($(‘h1’).text(), 10)
		count += 1
		$(‘h1’).text(count)
	})
	```
- 聲明式 (vue)
	```js
	<template>
		<div id=“counter”>
			<h1>{{count}}</h1>
			<button @click=“add”>+1</button>
		</div>
	</template>
	
	<script>
	export default {
		data () {
			return {
				count: 0
			}
		},
		methods: {
			add () {
				this.count += 1
			}
		}
	}
	</script>
	```

---
v-if, v-else —> 根據 see 是 true or false 來決定 span 要不要 show 出來
v-for —> 把陣列裡面的內容印出來
```js
<template>
	<div id=“app”>
		<span v-if=“see”>Now you see me.</span>
		<span v-else>Now you don’t.</span>
		<div v-for=“step in steps”>{{step}}</div>
	</div>
</template>

<script>
export default {
	data () {
		return {
			see: false,
			steps: [
				‘learn JS’,
				‘learn Vue’,
				‘million per year'
			]
		}
	}
}
</script>
```

---
處理使用者輸入 —> 單純操作 data 裡的 count，沒有對 dom 操作
```js
<template>
	<div id=“counter”>
		<h1>{{count}}</h1>
		<button @click=“add”>+1</button>
	</div>
</template>

<script>
export default {
	data () {
		return {
			count: 0
		}
	},
	methods: {
		add () {
			this.count += 1
		}
	}
}
</script>
```

v-model
—> input 輸入表單跟資料的雙向綁定方法
```js
<template>
	<div id=“app”>
		<input type=“text” v-model=“text” />
		<span>{{text}}</span>
	</div>
</template>

<script>
export default {
	data () {
		return () {
			text: ''
		}
	}
}
</script>
```

---
todo-list
```js
<template>
	<div id=“app”>
		<div>
			<input type=“text” v-model=“input"/>
			<button @click=“add">Add</button>
		</div>
		<ul>
			<li v-for=“(item, index) in items”
				@click=“removeItem(index)”>
				{{item}}
			</li>
		</ul>
	</div>
</template>

<script>
export default () {
	data () {
		return {
			input: ‘’,
			items: [
				‘I have a pen’,
				‘I have an apple’,
				‘ahh apple-pen’
			]
		}
	},
	methods: {
		add () {
			this.items.push(this.input)
			this.input = ''
		},
		removeItem (index) {
			this.items.splice(index, 1)
		}
	}
}
<script>
```
---
片尾彩蛋: Vue, React, Angular 比較
-   React —> Virtual DOM(MVC的V) —> 適合中大型專案
     Facebook 2013
-   Vue —> MVVM —> 適合小型專案
     尤雨溪 2014
-   Angular —> MVVM —> 適合中大型專案
     Google 2010    
![](./photo/Pasted%20image%2020221109093028.png)

tag: #Vue #v-model #v-if #v-for #v-else #MVVM