- v-if —> 會直接把 v-if= false 的 element 拿掉，而不是用 css 讓 element 消失
```js
<template>
	<div id=“app”>
		<input type=“checkbox” v-model=“checked”/>
		<template v-if=“checked”>
			<h1>跳樓大拍賣</h1>
			<h2>跳樓大拍賣</h2>
			<h3>跳樓大拍賣</h3>
		</template>
	</div>
</template>

<script>
export defualt {
	data () {
		return {
			checked: false
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

- v-else —> 一定要是 v-if 的 sibling(要和 v-if 同一層)，且中間不能有其他的 element，v-if 和 v-else 不會共存
```js
<template>
	<input type=“checkbox” v-model=“checked”/>
	<h1 v-if=“checked”>跳樓大拍賣</h1>
	<h1 v-else>老闆不在家</h1>
</template>
```
- v-else-if —> v-if, v-else 是二選一, v-else-if 提供我們一個除了二元之外的另一個選擇
```js
<template>
	<input type=“checkbox” v-model=“checked1”/>
	<input type=“checkbox” v-model=“checked2”/>
	<h1 v-if=“checked1”>跳樓大拍賣</h1>
	<h1 v-else-if=“checked2”>夥計隨便賣</h1>
	<h1 v-else>老闆不在家</h1>
</template>

<script>
export default {
	data () {
		return {
			checked1: false,
			checked2: false
		}
	}
}
</script>
```

-   v-show —> 希望 element 存在，但又不希望他 show 出來時可以使用，跟 v-if 有兩個地方不一樣:
	-   第一個是 v-show 後面不能加上 v-else
	-   第二個是 v-show 不能搭配 template 使用
```js
<template>
	<input type=“checkbox” v-model=“checked”/>
	<h1 v-show=“checked”>跳樓大拍賣</h1>
</template>
```

tag: #Vue #v-if #v-else-if #v-else #v-show
