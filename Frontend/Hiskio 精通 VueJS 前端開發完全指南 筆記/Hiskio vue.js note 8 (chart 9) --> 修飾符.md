-  chap 1: 處理事件的兩種方式
	-   給他 function 的名字, ex1: add —> 盡量使用這一種, 可讀性高, html 也簡潔
	-   直接寫要做的事情, ex2: count+=1 —> 可讀性比較低

ex1:
```js
<template>
	<div id=“app”>
		<h1>{{count}}</h1>
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
ex2:
```js
<template>
	<div id=“app”>
		<h1>{{count}}</h1>
		<button @click=“count+=1”>Add</button>
	</div>
</template>

<script>
export default {
	data () {
		return {
		}
	}
}
</script>
```

-  chap 2: 偵聽滑鼠事件
	-   click —> 點擊
	-   mouseover —> hover 在這個 html 上面時
	-   mouseout —> hover 離開這個 html 上面時
	-   mousedown —> click 滑鼠點下去時
	-   mouseup —> click 滑鼠放開時
	-   mousemove —> 滑鼠在這個 html 上面移動時

```js
<template>
	<div id=“app”>
		<h1>{{count}}</h1>
		<button @mouseover=“count+=1” @mouseout=“count-=1”>Add</button>
	</div>
</template>

<script>
export default {
	data () {
		return {
			count: 0
		}
	}
}
</script>
```

-   chap 3: 偵聽圖片的 load 事件
	-   用 @load 去偵聽 img 的 on load event

	```js
	<template>
		<div id=“app”>
			<h4 v-if=“loading”>Loading</h4>
			<h4 v-else>Complete</h4>
			<img :class=“imgClass” @load=“loading = false” src=“https://xxx”/>
		</div>
	</template>
	
	<script>
	export default {
		data () {
			return {
				loading: true
			}
		},
		computed: {
			imgClass () {
				if (this.loading) {
					return ‘img hide'
				} else {
					return ‘img'
				}
			}
		}
	}
	</script> 
	
	<style lang=“scss” scoped>
	.img.hide {
		opacity: 0;
		transform: scale(3) rotate(180deg);
	}
	
	.img {
		transition: all 1s;
	}
	</style>
	```

- chap 4: 偵聽按鍵事件
	- @keydown 配合 keycode —> 鍵盤按下去觸發, ex1
	- @keyup 配合 keycode —> 鍵盤放開處發
	- 用修飾符代替 keycode 偵聽按鍵事件 —> ex2: @keydown.enter = “enter"

ex1:
```js
<template>
	<div id=“app”>
		<input v-model=“input” @keydown=“keydown"/>
		<ul>
			<li v-for=“item in list”>{{item}}</li>
		</ul>
	</div>
</template>

<script>
export default {
	data () {
		return {
			input: ‘',
			list: []
		}
	},
	methods: {
		keydown (event) {
			if (event.keycode === 13) {
				this.list.push(this.input)
				this.input = ‘'
			}
		}
	}
}
</script>
```

ex2: 
```js
<template>
	<div id=“app”>
		<input v-model=“input” @keydown.enter=“enter”/>
		<ul>
			<li v-for=“item in list”>{{item}}</li>
		</ul>
	</div>
</template>

<script>
export default {
	data () {
		return {
			input: ‘’,
			list: []
		}
	},
	methods: {
		enter () {
			this.list.push(this.input)
			this.input = ''
		}
	}
}
</script>
```

- chap 5: 有的吃甜何必吃苦？便利的修飾符語法糖
1. event.preventDefault() = @click.prevent —> click 事件的預設行為會被阻止
	```js
	<template>
		<div id=“app”>
			<a
				href=“https://google.com”
				target=“_blank”
				@click.prevent=“linkClick">google
			</a>
		</div>
	</template>
	
	<script>
	export default {
		data () {
			return {}
		},
		methods: {
			linkClick () {
				alert(‘Don’t')
			}
		}
	}
	</script>
	```

2. event.stopPropagation() = @click=stop —> click 事件只會發生在 DOM 的最裡面，不會傳出去 
3. @click.self —> 事件是自己本身發出的才執行
	```js
	<template>
		<div>
			<div class=“box” @click=“msg=‘C’">
				<div class=“box” @click=“msg=‘B’">
					<div class=“box” @click=“setA">
					</div>
				</div>
			</div>
			<h1>{{msg}}</h1>
		</div>
	</template>
	
	<script>
	export default {
		data () {
			return {
				msg: ''
			}
		},
		methods: {
			setA (event) {
				this.msg = ‘A'
			}
		}
	}
	</script>
	
	<style lang=“scss” scoped>
	.box {
		min-width: 80px;
		min-height: 80px;
		padding; 40px;
		border: 2px solid red;
		display: inline-block;
	}
	</style>
	```
4. @click.once —> click 事件只有第一次點下去會有效，之後的點擊都無效
```js
<template>
	<div id=“app”>
		<h1>{{count}}</h1>
		<button @click.once=“count+=1”>+1</button>
	</div>
</template>

<script>
export default {
	data () {
		return {
			count: 0
		}
	}
}
</script>
```

tag: #Vue #Modifier