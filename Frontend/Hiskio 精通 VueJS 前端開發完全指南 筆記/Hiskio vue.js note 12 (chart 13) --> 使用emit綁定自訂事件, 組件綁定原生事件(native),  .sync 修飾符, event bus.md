#### chap 1: 使用 v-on 綁定自訂事件
-   props 是上層 component 往下傳 component 傳遞
-   emit 則是下層 component 往上層 component 傳遞

template
```html
<div id=“app”>
	<my-button @my-click="clicked"></my-button>
	<my-counter @count-cmit="countEmit"></my-counter>
	<h1>{{count}}</h1>
</div>
```
```js
Vue.component(‘my-counter’, {
	template: `
		<div>
			<h1>{{count}}</h1>
			<button @click=“count+=1”>+1</button>
			<button @click=“emit”>emit</button>
		</div>
	`,
	data () {
		return {
			count: 0
		}
	},
	methods: {
		emit () {
			this.$emit(‘count-emit’, this.count)
		}
	}
})

Vue.component(‘my-button’, {
	template: ‘<button @click=“myClick”>my-button</button>’,
	methods: {
		myClick () {
			this.$emit(‘my-click’, 1, 2, 3, 4)
		}
	}
})

new Vue({
	el: ‘#app’,
	data: {
		count: 0
	}
	methods: {
		clicked (w, x, y, z) {
			alert(`${w},${x},${y},${z}`)
		},
		countEmit (count) {
			this.count = count
		}
	}
})
```

#### chap 2: 給組件綁定原生事件
-   用 native 綁定才能監聽到原生 HTML button 所發出的 click 事件，反之，如果不用 native 綁定，則不會 alert 東西出來

template
```html
<div id=“app”>
	<my-button @click.native=“btnClicked”></my-button>
</div>
```
```js
Vue.component(‘my-button’, {
	template: ‘<button>My Button</button>'
})

new Vue({
	el: ‘#app’,
	methods: {
		alert(‘clicked')
	}
})
```

#### chap 3: .sync 修飾符
```html
<xxx :outer-count.sync=“outerCount”></xxx>
```

等於以下
```html
<xxx
	:outer-count=“outerCount”
	@update:outer-count=“val => outerCount = val”
</xxx>
```
還要加上一個 emit event，其中 emit name 要加上帽號後面加上綁定的參數名
```js
this.$emit(‘update:outerCount’, this.innerCount)
```

template
```html
<div id=“app”>
	<my-counter :outer-count.sync=“outerCount”></my-counter>
	<h1>{{outerCount}}</h1>
</div>
```
```js
Vue.component(‘my-counter’, {
	props: [‘outerCount']
	data () {
		return {
			innerCount: this.outerCount
		}
	},
	template: `
		<div>
			<h1>{{innerCount}}</h1>
			<button @click=“innerCount+=1”>+1</button>
			<button @click=“sync”>sync</button>
		</div>
	`,
	methods: {
		sync () {
			this.$emit(‘update:outerCount’, this.innerCount)
		}
	}
})

new Vue({
	el: ‘#app’,
	data: {
		outerCount: 0
	}
})
```

#### chap 4: 自訂組件的 v-model
template
```html
<xxx v-model=“outerCount”></xxx>

<xxx :value=“outerCount: @input=“val => outerCount = val”></xxx>
```
```html
<div id=“app”>
	<my-counter :value="outerCount" @input=“val => outerCount = val”></my-counter>
	<h1>{{outerCount}}</h1>
</div>
```
```js
Vue.component(‘my-counter’, {
	props: [‘value’],
	data () {
		return {
			innerCount: this.value
		}
	},
	template: `
		<div>
			<h1>{{innerCount}}</h1>
			<button @click=“innerCount+=1”>+1</button>
			<button @click=“sync”>sync</button>
		</div>
	`,
	methods: {
		sync () {
			this.$emit(‘input’, this.innerCount)
		}
	}
})

new Vue({
	el: ‘#app’,
	data: {
		outerCount: 0
	}
})
```

#### chap 5: 跨組件的溝通
- event bus —> 宣告一個共用的 bus component，需要偵聽其他組件來的事件時，直接偵聽這個 event bus

template
```html
<div id=“app”>
	<my-count :count="count"></my-count>
	<my-button @click.native="count+=1"></my-button>
</div>
```
```js
Vue.component(‘my-count’, {
	props:[‘count’],
	template: ‘<h1>{{count}}</h1>'
})

Vue.component(‘my-button’, {
	template: ‘<button>+1</button>'
})

new Vue({
	el: ‘#app’,
	data: {
		count: 0
	}
})
```
template
```html
<div id=“app”>
	<my-count></my-count>
	<my-button></my-button>
</div>
```
```js
const bus = new Vue()

Vue.component(‘my-count’, {
	data () {
		return {
			count: 0
		}
	},
	template: ‘<h1>{{count}}</h1>’,
	mounted () {
		bus.$on(‘add’, () => {
			this.count += 1
		})
	}
})

Vue.component(‘my-button’, {
	template: ‘<button @click=“click">+1</button>’,
	methods: {
		click () {
			bus.$emit(‘add')
		}
	}
})

new Vue({
	el: ‘#app’,
	data: {
		count: 0
	}
})
```

#### chap 6: 實作: 你一定寫過的 “鄉鎮市區下拉選單” 之 Vue 版
-   重點一: v-model 要小心使用，不能讓他去修改 props 裡面的屬性 —> component Select 裡面的 v-model = “index” 和 props ‘value’, 可以改用 computed 另外存入一個變數
-   重點二: 在 new Vue 裡面的 data 不宣告 areas: [], 因為 areas 完全可以用 computed 從 ```
cities[this.cityIdx].areas``` 拿到

template
```html
<div id=“app”></div>
```
```js
const cities = [{
	name: ‘基隆市’,
	areas: [
		{ name: ‘仁愛區’, zip: ‘200’ },
		{ name: ‘信義區’, zip: ‘201’ },
		{ name: ‘中正區’, zip: ‘202’ },
		{ name: ‘中山區’, zip: ‘203’ },
		{ name: ‘安樂區’, zip: ‘204’ },
		{ name: ‘暖暖區’, zip: ‘205’ },
		{ name: ‘七堵區’, zip: ‘206’ }
	]
},
{
	name: ‘台北市’,
	areas: [
		{ name: ‘中正區’, zip: ‘100’ },
		{ name: ‘大同區’, zip: ‘103’ },
		{ name: ‘中山區’, zip: ‘104’ },
		{ name: ‘松山區’, zip: ‘105’ },
		{ name: ‘大安區’, zip: ‘106’ },
		{ name: ‘萬華區’, zip: ‘108’ },
		{ name: ‘信義區’, zip: ‘110’ }
	]
}] 

Vue.component(’Select’, {
	props: [‘value’, ‘options’],
	computed: {
		index: {
			get () {
				return this.value
			},
			set (val) {
				this.$emit(‘input’, val)
			}
		}
	},
	template: `
		<select v-model=“index">
			<option v-for=“(item, idx) in options” :value=“idx”>
				{{item.name}}
			</option>
		</select>
	`
})

new Vue({
	el: ‘#app’,
	data: {
		cityIdx: 0,
		areaIdx: 0
	},
	computed: {
		cities () {
			return cities
		},
		areas () {
			return cities[this.cityIdx].areas
		},
		zip () {
			return this.areas[this.areaIdx].zip
		}
	},
	watch: {
		cityIdx () {
			this.areaIdx = 0
		}
	},
	template: `
		<div>
			<Select v-model=“cityIdx" :options=“cities”/>
			<Select v-model=“areaIdx” :options=“areas”/>
			<br/>
			{{cityIdx}} - {{areaIdx}} - {{zip}}
		</div>
	`
})
```

tag: #Vue #emit #eventBus 