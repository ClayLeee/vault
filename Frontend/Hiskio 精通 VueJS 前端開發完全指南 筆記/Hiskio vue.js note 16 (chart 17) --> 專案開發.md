#### chap 1: 不要輸隔壁棚!我們也來寫井字遊戲
App.vue
```js
<template>
	<div class="app">
		<div class="board">
			<div
				class=“grid”
				v-for=“(grid, index) in grids”
				@click=“setGrid(index)”
			>	
				{{setSymbol(grid)}}
			</div>
		</div>
		<div class=“info”>
			<span>Player: {{setSymbol(player)}}</span>
			<br/>
			<span>Winner: {{setSymbol(winner)}}</span>
			<br/>
			<button @click=“reset”>Reset</button>
		</div>
	</div>
</template>

<script>
const lines = [
	[0, 1, 2], [3, 4, 5], [6, 7, 8],
	[0, 3, 6], [1, 4, 7], [2, 5, 8],
	[0, 4, 8], [2, 4, 6]
]

export default {
	data () {
		return {
			// O = 1, X = -1, null = 0
			grids: [
				0, 0, 0,
				0, 0, 0,
				0, 0, 0
			],
			player: 1,
			winner: 0
		}
	},
	computed: {
		// winner 完全可以由 grids 算出來, 所以不用另外寫在 data
		winner () {
			return lines.reduce((winner, [a, b, c]) => {
				if (winner !== 0) return winner
				const sum = this.grids[a] + this.grids[b] + this.grids[c]
				if (sum === 3) return 1
				if (sum === -3) return -1
				return 0
			}, 0)
		}
	},
	methods: {
		setGrid (index) {
			if (this.grids[idx] !== 0) return
			if (this.winner !== 0) return
			// 如果是陣列必須要使用 set 賦值, 不能直接用 this.grids[index] = this.player 賦值
			Vue.set(this.grids, index, this.player)
			this.player = -this.player
			// 同上 Vue.set()
			// this.$set(this.grids, index, this.player)
		},
		setSymbol (num) {
			return num === 0 ? ‘’ : num === -1 ? ‘O’ : ‘X'
		},
		reset () {
			this.grids = [0, 0, 0, 0, 0, 0, 0, 0, 0]
			this.player = 1
		}
	}
}
</script> 

<style>
.board {
	display: flex;
	flex-flow: row wrap;
	width: 400px;
	height: 400px;
	align-content: flex-start;
}

.grid {
	width: 33%;
	height: 33%;
	border: 1px solid gray;
	box-sizing: border-box;
	font-size: 60px;
	text-align: center;
	line-height: 130px;
}

.info {
	font-size: 40px;

}
</style>
```
#### chap 2: 找資料要快狠準: 搜尋與過濾
main.js
```js
import Vue from ‘vue’
import SearchLesson from ‘./SearchLesson.vue’

new Vue({
	el: ‘#app’,
	render: h => h(SearchLesson)
})
```
SearchLesson.vue
```js
<template>
	<div>
		<input type=“text” v-model=“text"/>
		<div class=“lesson-list">
			<div class=“lesson” v-for=“lesson in lessons”>
				<img :src=“lesson.cover”/>
				<h2>{{lesson.title}}</h2>
				<label>{{lesson.owner.username}}</label>
			</div>
		</div>
	</div>
</template>

<script>

// 只 import debounce
import debounce from ‘lodash/debounce’
// import 整個 lodash
import { debounce } from ‘lodash’

const SEARCH_API = ‘xxx'

export default {
	data () {
		return {
			text: ‘’,
			lessons: []
		}
	},
	methods: {
		search (val) {
			fetch(`${SEARCH_API}${val}`)
				.then(res => res.json())
				.then(data => {
					this.lessons = data.courses
				})
		}
	},
	watch: {
		text (val) {
			this.searchDebounced(val)
		}
	},
	created () {
		this.searchDebounced = debounce(this.search, 500)
	}
}
</script>

<style>
.lesson img {
	width: 100px;
}
.lesson span, .lesson label {
	display: block;
}
</style>
```

#### chap 3: 那個數字能不能跳一點, 大器一點: 跳動數字
main.js
```js
import Vue from ‘vue’
import App from ‘./App'

new Vue({
	el: ‘#app’,
	render: h => h(App)
})
```
App.vue
```js
<template>
	<div>
		<h1>App</h1>
		<h2>{{count}}</h2>
		<RunningNumber :number=“count”/>
		<br/>
		<button @click=“count+=100”>+</button>
		<button @click=“count-=100”>-</button>
	</div>
</template>

<script>
import RunningNumber from ‘./RunningNumber’

export default {
	components: {
		RunningNumber
	},
	data () {
		return {
			count: 0
		}
	}
}
</script>
```
RunningNumber.vue
```js
<template>
	<h2 :class=“{running: visibleNumber !== number}”>
		{{visibleNumber}}
	</h2>
</template>

<script>
export default {
	props: [’number’],
	data () {
		return {
			visibleNumber: this.number
		}
	},
	methods: {
		run () {
			const diff = Math.floor((this.number - this.visibleNumber) * 0.1)
			if (diff === 0) {
				this.visibleNumber = this.number
			} else {
				this.visibleNumber += diff
				// 瀏覽器繪製完的下一個 frame 就跑 this.run
				window.requestAnimationFrame(this.run)
				// 用 setTimeout 也可以達到同樣的效果
				// setTimeout(this.run, 1)
			}
			this.visibleNumber += diff
		}
	},
	watch: {
		number () {
			this.run()
		}
	}
}
</script> 

<style>
h2 {
	display: inline-block;
	transition: transform .2s;
}
h2.running {
	transform: scale(1.2);
	color: red;
}
</style>
```

#### chap 4: 站在巨人的肩膀上: Facebook Connect
google key words —> facebook js sdk

index.html
```html
<script src=“https://connect.facebook.net/en_US/sdk.js”></script>

// 在 dist 上面引入 facebook js sdk
<script src=“/dist/build.js”></script>
```
main.js
```js
import Vue from ‘vue’
import FBLogin from ‘./FBLogin’

new Vue({
	el: ‘#app’,
	render: h => h(FBLogin)
})
```
FBLogin.vue
```js
<template>
	<div>
		<span>{{connected}}</span>
		<button
			v-if=“!connected”
			@click=“connect”
		>
			Connect
		</button>
		<div v-else>
			<h4>{{username}}</h4>
			<img :src=“picture”/>
		</div>
	</div>
</template>

<script>
export default {
	data () {
		return {
			connected: false,
			userID: ‘',
			username: ‘’,
			picture: ‘’,
			email: ''
		}
	},
	methods: {
		connect () {
			FB.login(this.statusGet)
		},
		statusGet (result) {
			// result.status: 1. connected 2. not-authorized 3. unknown
			if (result.status === ‘connected’) {
				this.connected = true
				FB.api(‘/me’, ‘GET’, {
					fields: [‘picture’, ‘email']
				}, res => {
					this.userID = res.id
					this.username = res.name
					this.picture = res.picture.data.url
					this.email = res.email
				})
			}
		}
	},
	mounted () {
		FB.init({
			appId: 182706955649650,
			version: ‘v2.12’
		})
		FB.getLoginStatus(this.statusGet)
	}
}
</script>
```

#### chap 5: 分頁固然好, 無限更加潮: 無限捲動
index.html 一樣引入 fb js sdk

main.js
```js
import Vue from ‘vue’
import InfiniteScrollApp from ‘./InfiniteScrollApp’

new Vue({
	el: ‘#app’,
	render: h => h(InfiniteScrollApp)
})
```
InfiniteScrollApp.vue
```js
<template>
	<div>
		<span>{{connected}}</span>
		<InfiniteScroll :picture=“picture” :next=“getAlbum"/>
	</div>
</template>

<script>
import InfiniteScroll from ‘./InfiniteScroll'

export default {
	components: {
		InfiniteScroll
	},
	data () {
		return {
			connected: false,
			pictures: [],
			after: ''
		}
	},
	methods: {
		connect () {
			FB.login(this.statusGet)
		},
		statusGet (result) {
			if (result.status === ‘connected’) {
				this.connected = true
				this.getAlbum()
			}
		},
		getAlbum () {
			return new Promise(resolve => {
				FB.api(‘/738251022885507/photos’, {
				fields: [‘picture’],
				after: this.after
			}, result => {
				this.after = result.paging.cursors.after
				this.pictures = this.picture.comcat(
					result.data.map(item => item.picture)
				)
				resolve()
			})
		}
	},
	mounted () {
		FB.init({
			appId: 182706955649650,
			version: ‘v2.12’
		})
		FB.getLoginStatus(this.statusGet)
	}
}
</script>
```
InfiniteScroll.vue
```js
<template>
	<div class=“list">
		<div
			class=“item"
			v-for=“picture in pictures”
			style=“getStyle(picture)"
		/>
		<div v-if=“loading” class="item">Loading</div>
	</div>
</template>

<script>
export default {
	props: [‘pictures’, ’next’],
	data () {
		return {
			loading: false
		}
	},
	methods: {
		getStyle (picture) {
			backgroundImage: `url(‘${picture}’)`
		},
		onScroll () {
			// 視窗上緣在頁面的位置是哪裡: window.scrollY
			// 整個視窗的高度: window.innerHeight
			// 整個文件的高度: document.body.scrollHeight
			if (this.loading) return
			if (window.scrollY + window.innerHeight > document.body.scrollHeight - 50) {
				this.loading = true
				//Promise 語法: 執行完 this.next() 之後就把 this.loading = false
				this.next().then(() => this.loading = false)
			}
		}
	},
	mounted () {
		// addEventListener 要在 mounted 裡面做, 在 beforeDestroy 裡面 remove
		// 不 remove 運氣好不會怎樣, 運氣不好的話會造成效能上很大的損耗
		window.addEventListener(’scroll’, this.onScroll)
	},
	beforeDestroy () {
		window.removeEventListener(’scroll’, this.onScroll)
	}
}
</script>

<style scoped>
.list {
	display: flex;
	flex-flow: row wrap;
}

.item {
	flex: 0 0 100px;
	background: center center no-repeat #ccc;
	background-size: cover;
	height: 100px;
}
</style>
```

#### chap 6: 片尾彩蛋: 建立 FB App
[facebook developer](https://developers.facebook.com/)

tag: #Vue #Query #Filter #FB #InfiniteScroll