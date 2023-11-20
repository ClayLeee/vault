#### chap 1: 關於 vue-cli
node.js —> 可以讓電腦執行 JS 的環境
vue-cli 可以很快速地建立開發模板, 就可以利用 webpack 打包原始檔

1.  下載 node.js LTS(long term support)    
2.  終端機輸入下列
    ```sudo npm i -g vue-cli```

#### chap 2: 編輯器設定  
1.  套件搜尋 Vue
2.  安裝 Vue 2 Snippets
3.  完成！

#### chap 3: import 與 export
export
```js
const Counter = Vue.component(‘counter’, {
	...
})

export default Counter
```
import 
```js
import Counter from ‘./Counter.js’

new Vue({
	el: ‘#app’,
	components: {
		Counter
	},
	template: `
		<div>
			<Counter/>
		</div>
	`
})
```

#### chap 4: vue-cli
render: h => h(App) === render: function (createElement) { return createElement(App) }
```js
new Vue({
	el: ‘#app’,
	render: h => h(App)
})
```

#### chap 5: script, template, style 都在同一個檔案裡, 單一檔案組件
1.  如果在 template 裡面用像是 <MyCounter/>，不需要轉成 <my-counter/>，因為這個 template 裡面包的不是真正的 html
2.  script 一定要 export default 一個物件
```js
<template>
	<div></div>
</template>

<script>
export default {
}
</script>

<style lang=“scss">
	#app {}
</style>
```

#### chap 6: 解決 css 困境的 scoped style
1.  css 所有的選擇器都是全域有效 (global scope)
2.  css 取名很容易撞名 (collision)
3.  css specificity (權重)

```<style scoped></style>``` —> scoped 可以限制 css 只在加上的 component 生效

如果外面的 component 和裡面的 component 有相同的 class name，則在外面 component 的 style 加上 css，會同時套用到兩者
```js
<template>
	<div class=“block">
		<h1>Outer</h1>
		<Inner/>
	</div>
</template>

<script>
import Inner from ‘./Inner.vue'

export default {
	components: {
		Inner
	}
}
</script>

<style scoped>
	h1 {
		color: red;
	}
	.block {
		border: 2px solid red;
	}
</style>
```

Inner.vue
```js
<template>
	<div class=“block">
		<h1>Inner</h1>
	</div>
</template>

<style scoped>
h1 {
	color: blue
}
</style>
```

#### chap 7: 實作: 老梗值得一寫在寫, todo list 改
1.  script 和 template 哪一個要放在前面都可以，style 一定要放在最後面 
2.  一個單一檔案組件可以沒有 style，但是 script, template 至少要有一個
```js
<template>
	<div>
		<TodoInput @submit="addTodo"/>
			<ul>
				<TodoItem v-for=“(todo, idx) in todos” :data=“todo” @click.native="removeTodo(idx)"/>
			</ul>
	</div>
</template>

<script>
import TodoInput from ‘./TodoInput.vue’
import TodoItem from ‘./TodoItem.vue'

export default {
	data () {
		return {
			todos: []
		}
	},
	components: {
		TodoInput,
		TodoItem
	},
	methods: {
		addTodo (text) {
			this.todos.push(text)
		},
		removeTodo (idx) {
			this.todos.splice(idx, 1)
		}
	}
}
</script>
```

TodoInput.vue
```js
<template>
	<form @submit.prevent=“submit">
		<input v-model=“text">
		<button type=“submit">Submit</button>
	</form>
</template>

<script>
export default {
	data () {
		return {
			text: ''
		}
	},
	methods: {
		submit () {
			this.$emit(’submit’, this.text)
			this.text = ''
		}
	}
}
<script>
```

TodoItem.vue
```js
<template>
	<li class=“todo-item">
		{{todo}}
	</li>
</template>

<script>
export default {
	props: [‘data']
}
</script>

<style>
.todo-item {
	font-size: 1.2em;
	font-family: Arial;
	background-color: #fed;
	margin: 6px 0;
	padding: 4px 8px;
	cursor: pointer;
}
.todo-item:hover {
	background-color: #def;
}
</style>
```

tag: #Vue #Import #Export #ScopedStyle #Vue-cli
#ToDoList