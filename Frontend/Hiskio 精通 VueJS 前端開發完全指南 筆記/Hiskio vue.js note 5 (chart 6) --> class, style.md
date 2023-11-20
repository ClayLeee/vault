### 綁定資料為一個元素的 class
- btnClass() return String
```js
<template>
	<div id=“app”>
		<input type=“text” v-model=“text">
		<button :class=“btnClass”>Submit</button>
	</div>
</template>

<script>
export default {
	name: ‘app’,
	data () {
		return {
			text: ''
		}
	},
	computed: {
		btnClass () {
			if (this.text.length === 0) {
				return ’submit disabled'
			} else {
				return ’submit'
			}
		}
	}
}
</script>

<style lang=“scss” scoped>
#app {
	text-align: center;
	.submit {
		background-color: #28a745;
		color: white;
		border: none;
		border-radius: 8px;
		padding: 4px 8px;
	}
	.submit.disabled {
		background-color: #aaa;
	}
}
</style>
```
- btnClass() 回傳值也可以是物件
```js
computed: {
	btnClass () {
		return {
			submit: true,
			disabled: this.text.length === 0
		}
	}
}
```
- btnClass() 也可以是陣列
```js
<template>
	<div id=“app”>
		<input type=“text” v-model=“text">
		<button :class=“btnClass”>Submit</button>
	</div>
</template>

<script>
export default {
	name: ‘app’,
	data () {
		return {
			text: ‘’,
			btnClass: [
				’submit’,
				‘disabled'
			]
		}
	}
}
</script>
```
---
### 綁定資料為一個元素的 style
- h1Style is String
```js
<template>
	<div id=“app”>
		<h1 :style=“h1Style">Hello</h1>
	</div>
</template>

<script>
export default {
	name: ‘app’,
	data () {
		return {
			h1Style: ’color: red'
		}
	}
}
</script> 

<style lang=“scss” scoped>
#app {
	text-align: center
}
</style> 
```

```js
// h1Style can also be a Object
data () {
	return {
		h1Style: {
			color: ‘green'
		}
	}
}
```
-   h1Style can also be a Array, it’ll be used by pattern like the words in this case will be green instead of a red one

   caution: 在物件裡面必須要用駝峰式命名(camel case), ex: fontSize instead of font-size
```js
const commonHeadStyle = {
	fontSize: ‘20px’,
	color: ‘red'
}

const greenWord = {
	color: ‘green'
}

export default {
	name: ‘app’,
	data () {
		return {
			h1Style: [
				commonHeadStyle,
				greenWord
			]
		}
	}
}
```

example:
```js
<template>
	<div id=“app”>
		<h1 :style=“h1Style”>Hello world</h1>
		<button @click=“shrink">OK</button>
	</div>
</template>

<script>
export defualt {
	name: ‘app’,
	data () {
		return {
			size: 40
		}
	},
	computed: {
		h1Style () {
			return {
				fontSize: `${this.size}px`
			}
		}
	}
	methods: {
		shrink () {
			this.size--
		}
	}
}
</script>
```

tag: #Vue  #Class #style

