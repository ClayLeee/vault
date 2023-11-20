#### chap 1: 簡論 Vue 的 SEO
##### Vue 與 SEO
- Vue 是 client side rendering, 搜尋引擎爬不到
現代的搜尋引擎其實已經會跑 JS render(不含 ajax)
	-   google
	-   bing 
	-   yahoo
但只有 Google 會主動爬 JS render 出來的 link
- SEO
	1.  高互動性, 最適合做成 SPA 的頁面, 如會員系統, 記帳系統, 本來就要登入才能用, 不需 SEO    
	2.  行銷用頁面, 無動態資料, 直接 serve 靜態檔案, 或者使用 prerenderer
	3.  大量動態資料 server-side rendering(SSR)

##### Prerender
預先用工具將所有的頁面 render 為靜態 html

伺服器與搜尋引擎透過特別的協定

讓伺服器把這些 html 送給搜尋引擎，讓搜尋引擎藉此來做索引
-   Prerender.io
-   自己用例如 PhantomJS 等工具實作

##### Server-side rendering(SSR)
1.  Vue 是用 JS 跑的
2.  伺服器也可以跑 JS(NodeJS)
3.  伺服器也可以跑 Vue 並 render 出結果

#### chap 2: Prerender
```npm html-webpack-plugin prerender-spa-plugin```

把 index.html 裡面的
```html
<script src=“/dist/build.js”></script>
```
刪掉

webpack.config.js
add:
```js
var HTMLWebpackPlugin = require(‘html-webpack-plugin’)

var PrerenderSPAPlugin = require(‘prerender-spa-plugin’)

plugins: [
	new HTMLWebpackPlugin({
		title: ‘xxx’,
		filename: index.html,
		template: path.resolve(__dirname, ‘./index.html’) // 要取代哪一個檔案
	})
]

// build 的時候才需要
if (process.env.NODE_ENV === ‘production’) {
	new PrerenderSPAPlugin(
		path.resolve(__dirname, ‘./dist’), // prerender 出來到哪個路徑
		[
			‘/‘,
			‘/about’,
			‘/course’,
			‘/course/1’,
			‘/course/2’,
			‘/course/3’,
			‘/booking'
		], // render 哪些路徑
	)
}
```
router.js
```js
mode: ‘history’ // 不能使用 ‘hash'
```

#### chap 3: Server Side Rendering: Nuxt
保留 SPA 的特性，且在每一個路徑下有做 SSR 的動作

本來的 ```<router-view/>``` 變成 ```<nuxt-child/>```, 本來的 ```<router-link/>``` 要變成 ```<nuxt-link/>```

pages: 放一整個頁面用
components: 放一般的組件用, 例如: 列表, menu, footer
static: 放靜態檔案, 例如: 圖片, 如果在組建裡面需要引用時一律使用: ‘/kv.jpg’ 

[https://localhost:3000/test](https://localhost:3000/test)

pages/test.vue
```html
<template>
	<h1>TEST</h1>
</template>
```
要放在伺服器上面跑 —>
```npm run build```
```npm run start```

想要跑在別的 port —>
package.json
```json
“config”: {
	“nuxt”: {
		“host”: “127.0.0.1”,
		“port”: “8081"
	}
}
```

tag: #Vue #SEO #Prerender #SSR #Nuxt
