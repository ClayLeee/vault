v-once —> 設定 v-once 的模板只會 render 第一次，後面資料變更他都不會再改變
```js
<template>
	<div id=“app”>
		<span v-once>{{message}}</span><br/>
		<span>{{message}}</span><br/>
		<button @click=‘append’>Append</button>
	</div>
</template>

<script>
export default () {
	data () {
		return {
			message: ‘Hello world!'
		}
	},
	methods: {
		append () {
			this.message += ‘!'
		}
	}
}
</script>
```

v-html —> 可以把 html 的字串 render 出來，但是用 v-html 可能是危險的，因為 v-html 可以把字串render 出來，如果有人在裡面包一些危害社會的東西...，簡稱為 XSS(cross site script)，因此要使用 v-html 要確保資料來源是可靠的，例如資料是從自己的資料庫輸入，不能是使用者輸入的值
```js
<template>
	<div id=“app”>
		<span v-html=“message"></span>
	</div>
</template>

<script>
export default () {
	data () {
		return {
			message: ‘<h1>Hello</h1>'
		}
	}
}
</script>
```

v-bind —> 把屬性的值動態綁定到變數 
v-on —> 偵聽 DOM 事件的方法，除了 click，還有 load, mouseover
```js
<template>
	<div id=“app”>
		<input type=“checkbox” :checked=“selected” />
		<button @click=“toggle”>Toggle</button>
	</div>
</template>

<script>
export default {
	data () {
		return {
			selected: false
		}
	},
	methods: {
		toggle () {
			this.selected = !this.selected
		}
	}
}
</script>
```
v-on 還可以放表達式在裡面
```js
<template>
	<div id=“app”>
		<span>{{count}}</span>
		<input type=“checkbox” :checked=“count % 2 === 0” />
		<button @click=“add”>Add</button>
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

v-model —> 用來雙向綁定
```js
<template>
	<div id=“app”>
		<input type=“text” v-model=“text” /><br />
		{{text}}
	</div>
</template>

<script>
export default {
	data () {
		return {
			text: “Hello"
		}
	}
}
</script>
```

tag: #Vue #v-bind #v-html #v-model #v-on #v-once