#### Vue-router
  
SPA —> single page application 一頁式網頁
更 complex 的 router application reference —> [path-to-regexp](https://github.com/pillarjs/path-to-regexp)

##### 基本配置
-   main.js —> Import VueRouter and use it by Vue.use(VueRouter), you can also do this in APP.vue. When using it, we’ll have these two <router-view/> and ```<router-link>``` available
```js
import Vue from ‘vue’
import VueRouter from ‘vue-router’
import App from ‘./App.vue’

Vue.use(VueRouter)

new Vue({
	render: h => h(App)
}).$mount(‘#app’)
```

-   App.vue —> 
	-   ```<router-view/>``` —> 會 render 出定義在 routes 裡面的組件內容
	-   ```<router-link>``` —> 出現切換網址的 link

	```js
	<template>
		<div class=“menu”>
			<router-link to=“/about”>About</router-link>
			<router-link to=“/products”>Products</router-link>
		</div>
		<div>
			<router-view />
		</div>
	</template>
	
	<script>
	import VueRouter from ‘vue-router’
	import Products from ‘./Products.vue’
	import About from ‘./About.vue’
	
	export default {
		router: new VueRouter({
			routes: [
				{ path: ‘/about’, component: About },
				{ path: ‘/products’, component: Products }
			]
		})
	}
	</script>
	```
---
##### 動態比對
- App.vue —>
	- params: ex: localhost:8080/#/products/26 —> 在 path 後面加上動態參數 :id ，名字可以自己取，不一定要叫 id， id 後面加上 ?(/products/:id?)表示 id 可有可無，若 id 沒有給值，頁面仍然可以載入 products 組件，id 有給值，兩者都會加入路徑 render 出來
	- query: ex: localhost:8080/#/products/?id=26
```js
<template>
	<div class=“menu”>
		<router-link to=“/about”>About</router-link>
		<router-link to=“/products”>Products</router-link>
	</div>
	<div>
		<router-view />
	</div>
</template>

<script>
import VueRouter from ‘vue-router’
import Products from ‘./Products.vue’
import About from ‘./About.vue’

export default {
	router: new VueRouter({
		routes: [
			{ path: ‘/products/:id?’, component: Products },
			{ path: ‘/about’, component: About }
		]
	})
}
</script>
```

-   Products.vue —> 
	-   params: $route.params.id => 26
	-   query: $route.query.id => ?id=26
	```js
	<template>
		<div>
			<h1>Products</h1>
			<h2>{{ id }}</h2>
		</div>
	</template>
	
	<script>
	const prosucts = {
		18: ’Shoes’,
		26: ’T-shirts’,
		37: ‘Pants'
	}
	
	export default {
		computed: {
			id () {
				return products[this.$router.params.id]
			}
		}
	}
	</script>
	```

Warning:如果路徑的設定會讓兩個組件同時匹配到的話，則會 render 出定義在前面的組件

url: localhost:8080/#/products --> 會套用到 '/:pages'
```js
routes: [
	{ path: ‘/:page’, component: ‘Page’},
	{ path: ‘/products/:id', component: ‘Products'}
]
```
---
##### 巢狀結構
-   App.vue —> 
	-   除了 import 之外，加上 children: [{…}]
	-   裡面的 us 和 you 不能使用根目錄: /us, /you
	-   如果想要讓不是選到 us 和 you 的時候顯示預設 component，就給 path: ‘'
	-   在想要 render 出 component 的地方加上 ```<router-view/>```
	```js
	<template>
		<div class=“menu”>
			<router-link to=“/about”>About</router-link>
			<router-link to=“/products”>Products</router-link>
		</div>
		<div>
			<router-view />
		</div>
	</template>
	
	<script>
	import VueRouter from ‘vue-router’
	import Products from ‘./Products.vue’
	import About from ‘./About.vue’
	import AboutHome from ‘./AboutHome.vue'
	import AboutUs from ‘./AboutUs.vue’
	import AboutYou from ‘./AboutYou.vue'
	
	export default {
		router: new VueRouter({
			routes: [
				{ path: ‘/products/:id?’, component: Products },
				{ path: ‘/about’, component: About, children: [
					{ path: ‘’, component: AboutHome },
					{ path: ‘us’, component: AboutUs },
					{ path: ‘you’, component: AboutYou }
				]}
			]
		})
	}
	</script>
	```

About.vue —> 補上一個 ```<router-link/>```
```html
<template>
	<div>
		<h1>About</h1>
		<router-link to=“/about/us”>Us</router-link>
		<router-link to=“/about/you”>You</router-link>
	</div>
</template>
```
main.js —> 補上 App 的 ```<router-view/>```
```js
<template>
	<router-view/>
</template>

import Vue from ‘vue’
import router from ‘./router’

new Vue({
	router,
	render: h => h(App)
}).$mount(‘#app')
```
Router.js —> 從 App.vue 拆出來，以方便管理路徑
```js
import App from ‘./App.vue'
import VueRouter from ‘vue-router’
import Products from ‘./Products.vue’
import About from ‘./About.vue’
import AboutHome from ‘AboutHome.vue'
import AboutUs from ‘./AboutUs.vue’
import AboutYou from ‘./AboutYou.vue’

Vue.use(VueRouter)

new VueRouter({
	routes: [{
		path: ‘/‘,
		component: App,
		children: [{
			path: ‘products/:id?’,
			component: Products
		},
		{
			path: ‘about’,
			component: About,
			children: [
				{ path: ‘’, component: AboutHome },
				{ path: ‘us’, component: AboutUs },
				{ path: ‘you’, component: AboutYou }
			]
		}]
	}]
})
```

--- 
##### 具名路由 & 具名視圖
-   routes 裡面給他一個 name: ‘xxx’，router-link 就可以動態綁定 :to=“{ name: ‘xxx’}” 以讀取到 xxx 這個 component
-   如果使用兩個一樣名稱的 name ，他一樣會讀取到第一個

Router.js
```js
{ path: ‘us’, name: ‘us’, component: AboutUs }
{ path: ‘products/:id’, name: ‘prod’, component: Products }
```
About.vue
```html
<template>
	<div>
		<h1>About</h1>
		<router-link :to=“{name: ‘us’}”>Us</router-link>
		<router-link :to=“{name: ‘prod’, params: {id: 18}}”>shoes</router-link>
	</div>
</template>
```

##### 小孩子才選擇，大人全都要 —> 同時 render 出兩個以上的 components 
-   使用 components:{ x: xxx, y: yyy }，要注意 key 並非 component
-   如果沒有給他 name，name 預設為 defualt，也就是說 ```
<router-view/>``` 等同於 ```<router-view name=“default"/>```
Router.js —> add { default: AboutUs, another: AboutYou }
```js
{ path: ‘us’, component: AboutUs },
{ path: ‘you’, component: AboutYo },
{ path: ‘both’, components: {
	default: AboutUs,
	another: AboutYou
}}
```
About.vue —> add ```<router-view/> & <router-view name=“another"/>```
```html
<template>
	<div>
		<router-link to=“/about/us”>Us</router-link>
		<router-link to=“/about/you”>You</router-link>
		<router-link to=“/about/both”>Both</router-link>
		<router-view/>
		<router-view name=“another”/>
	</div>
</template>
```

--- 
##### 轉址與別名
- redirect —> 網址會改變成 redirect 裡面設定的網址，path 可以有以下幾種形式:
	-  object
	-  function
```js
{
	path: ‘aboutus’,
	redirect: ‘/about/us'
}
```
不管輸入什麼都會自動轉址到 redirect 的路徑
```js
{
	path: ‘*’,
	redirect: ‘/about/us'
}
```
redirect 可以使用 object
```js
children: [
	{ path: ‘us’, name: ‘home’, component: AboutUs }
],
...
{
	path: ‘*’,
	redirect: {name: ‘home'}
}
```
redirect 也可以使用 function
```js
{
	path: ‘*’,
	redirect: (from) => {
		return (name: ‘home')
	}
}
```
alias —> 網址並不會改變，網址會保留原本的，但內容會是 alias 設定的網址
—> 網址會是 …/story, 內容是 About
```js
{
	path: ‘about’,
	alias: ’story’,
	component: About,
	...
}
```
-   alias 可以是以下幾種形式: 
	-   相對路徑 —> 從此目錄出發的路徑
	-   絕對路徑 —> 從根目錄出發的路徑
	-   陣列 —> 會有多個 alias
根目錄底下的 2，此目錄底下的 2 或是 3 都會相當 AboutBoth
```js
{
	path: ‘both’,
	alias: [‘/2’, ‘2', ‘3’],
	components: {
		default: AboutUs,
		another: AboutYou
	}
}
```
---
##### 組件的 props
如果在 router 裡面使用 component，component 在 render 出來的時候就會帶有 this.$route 這個屬性，vue-router 會自己注入這個屬性到組件裡面
	-   props 可以有三種:
		-   boolean (true or false)
		-   object {id: 3} —> 給他固定的屬性
		-   function route=> ({ id: route.params.id })—> 回傳是一個物件
    
設定 props 為 true 的時候，就會把 path 裡面所有設定的 params(:id) 全部當成 Products 這個組件的 props 傳進去，在組件裡面就可以不用用到 this.$router
Products.vue
```js
<script>
export default {
	props: [‘id']
}
</script>
```
router.js
```js
{
	path: ‘products/:id?’,
	name: ‘home’,
	component: Products,
	props: true
}
```
直接指定 props 的東西
```js
{
	path: ‘products’,
	name: ‘home’,
	component: Products,
	props: {id: 3}
}
```
使用 function， props 裡面的東西等同於 this.$router
```js
{
	path: ‘products/:id’,
	name: ‘home’,
	component: Products,
	props: (route) => {
		return {
			id: route.params.id
		}
	}
}
```

tag: #Vue #VueRouter