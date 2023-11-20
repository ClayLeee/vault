### v-for
#### chap 1: 用 v-for 把陣列轉換成一組元素(html element)
ex1: v-for —> item 為陣列的每一個 element，todo 為陣列名字
```js
<template>
	<div id=“app”>
		<ol>
			<li v-for=”item in todo” :class=“item">{{item}}</li>
		</ol>
	</div>
</template>

<script>
export default {
	data () {
		return {
			todo: [
				‘學會 JS’,
				‘學會 Vue’,
				‘投遞履歷’,
				‘年薪百萬’
			]
		}
	}
}
</script>
```
ex2:
```js
<template>
	<div id=“app”>
		<select>
			<option v-for=“month in months”>{{month}}</option>
		</select>
	</div>
</template>

<script>
export default {
	data () {
		return {
			months: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12]
		}
	}
}
</script>
```
ex3: v-for 特殊用法 —> 如果要從 1 數到某一個數字
```js
<template>
	<div id=“app”>
		<select>
			<option v-for=“month in 12”>{{month}}</option>
		</select>
	</div>
</template>
```
ex4: 使用 v-for 裡的 index，index 為索引值
```js
<template>
	<div id=“app”>
		<select>
			<option v-for=“(month, index) in months” :value=“index + 1">{{month}}</option>
		</select>
	</div>
</template>

<script>
export default {
	data () {
		return {
			months: [‘Jan’, ‘Feb’, ‘Mar’, ‘Apr’, ‘May’, ‘Jun']
		}
	}
}
</script>
```

#### chap 2: 用 v-for 把物件轉換成一組元素
ex: 用 v-for 去轉換一個物件，轉出來的順序，不見得會跟我們放在物件裡面的順序一樣，他不保證先宣告的就會在最前面，例如使用數字當作 key 的時候他會把數字比較小的排在前面，數字排完之後才會去排單字，所以如果希望要求輸出順序的話，就使用陣列，像是底下例子的 me 可以改成 meToArr
```js
<template>
	<div id=“app”>
		<div v-for=“(value, key) in me”>{{key}} - {{value}}</div>
		<div v-for=“item in meToArr”>{{item.prop}} - {{item.value}}</div>
	</div>
</template>

<script>
export default {
	data () {
		return {
			me: {
				name: ‘clay’,
				id: ’scars’,
				age: 22,
				password: ‘1234567’
				13: '大學畢業’,
				8: ‘訂婚’,
				4: ’創辦公司‘
			},
			meToArr: [
				{ props: ’name’, value: ‘clay’ },
				{ props: ‘id’, value: ’scars’ },
				{ props: ‘age’, value: 22},
				{ props: 8, value: ‘訂婚’ },
				{ props: 4, value: ‘創辦公司‘}
				…
			]
		}
	}
}
</script>
```

#### chap 3: 修改陣列或物件的注意事項
caution: 以下 function 修改陣列或物件的時候，可以把改變反應在 element 上 —> 
1.  push()
2.  pop() —> 從後面扣除一個
3.  shift() —> 從前面扣除一個
4.  unshift() —> 從前面塞一個進去
5.  splice(index, length)
6.  sort()
7.  reverse()

```js
<template>
	<div id=“app”>
		<ul>
			<li v-for=“num in nums”>{{num}}</li>
		</ul>
		<button @click=“action”>Ya</button>
	</div>
</template>

<script>
export default {
	data () {
		return {
			nums: [1, 2, 3, 4, 5]
		}
	},
	methods: {
		action () {
			this.nums.push(this.nums.length + 1)
		},
		even () {
			this.nums = this.nums.filter(ele => (ele % 2 === 0))
		}
	}
}
</script>
```

不會反應的操作和取代方法: 
1.  vue 無法偵聽到對陣列索引值的變更, ex: this.num[0] = 10, 此例可以使用 this.nums.splice(2, 1, 10) 
2.  指定陣列的 length 也不會反映到元素上, ex: this.nums.length = 0, 可以使用 this.nums.splice(3) 取代，他會把索引值 3 之後的元素都砍掉，或者使用 this.nums = this.nums.slice(1, 3)
3.  陣列裡面新增一個項目 vue 無法偵聽到(本來就在陣列裡面的值則可以): this.$set(this.nums, ‘z’, 30) —> 設定 this.nums 這個物件的 key(‘z’) 的 value 是 30, this.nums.z 不能使用是因為物件裡面本來沒有 z 這個 key, 如果是 this.nums.x = 30 就可以改的到 x
4.  Vue.set(this.nums, ‘z’, 30) = this.$set(this.nums, ‘z’, 30)

```js
<template>
	<div id=“app”>
		<ul>
			<li v-for=“num in nums”>{{num}}</li>
		</ul>
		<button @click=“action”>Ya</button>
	</div>
</template>

<script>
export default {
	data () {
		return {
			nums: {
				x: 10,
				y: 20
			}
		}
	},
	methods: {
		action () {
			this.nums.z = 30
			this.$set(this.nums, ‘z’, 30)
			Vue.set(this.nums, ‘z’, 30)
		}
	}
}
</script>
```

#### chap 4: 列表的過濾與排序
-   sort() 會修改本來的陣列
-   使用 this.nums = orgNums.slice().sort() 去排序
```js
<template>
	<div>
		<ul>
			<li v-for=“num in nums”>{{num}}</li>
		</ul>
		<button @click=“all”>Original</button>
		<button @click=“even”>Even</button>
		<button @click=“sort”>Sort</button>
	</div>
</template>

<script>
const orgNums = [5,4,6,1,7,9,8,2,3]

export default {
	data () {
		return {
			nums: orgNums
		}
	},
	methods: {
		all () {
			this.nums = orgNums
		},
		even () {
			this.nums = this.nums.filter(num => num % 2 === 0)
		},
		sort () {
			this.nums = orgNums.slice().sort()
		}
	}
}
</script>
```

#### chap 5: 用 v-for 來渲染 <template>
想要 render 出兩個以上的 html 時可以使用 template

```js
<template>
	<div id=“app”>
		<template v-for="header in headers">
			<h1>{{header}}</h1></hr>
		</template>
	</div>
</template>

<script>
export default {
	data () {
		return {
			headers: [
				‘About’,
				‘Products’,
				‘Contact'
			]
		}
	}
}
</script>```

#### chap 6: 實作: 從 json 資料渲染課程列表
實用技巧: 在 div 中使用 v-style 配合 css backgroundImage 取代  達成可調式 image

```js
<template>
	<div id=“app”>
		<div class=“image” :style=“imgStyle(xxx.img)”></div>
	</div>
</template>

<script>
export default {
	data () {
		xxx: [...]
	},
	methods () {
		imgStyle (img) {
			return {
				backgroundImage: `url(${img})`
			}
		}
	}
}
</script>  

<style>
.image {
	width: 100%;
	height: 100px;
	background-size: cover;
	background-position: center center;
}
</style>
```

-  片尾彩蛋: 每個人都認識一個 Jason, 卻不是每個人都認識 json
	-   stringify
		-   JSON.stringify(data) —> 將 json 轉換成字串
		-   JSON.stringify(data, replacer) —> JSON.stringify(data, [‘x’, ‘y’], 2) —> 將 json 轉成只有 x, y 為 key 的字串，每個 key & value 前面有 2 個空格
	-   parse
		-   JSON.parse(data) —> 將字串轉成 json
		-   JSON.parse(data) —> 使用 try & catch 去處理 error 情況

```js
const dataJSON = `
	{
		“name”: “point”,
		“x”: 2,
		“y”: 4
	}
`

let data;

try {
	data = JSON.parse(dataJSON)
} catch (err) {
	data = {}
}
```

tag: #Vue #set #v-for #Array #Object 