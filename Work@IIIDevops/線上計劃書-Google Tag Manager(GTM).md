> 聽到要埋 [GTM](https://tagmanager.google.com/) 到專案裡面的時候，心裡想說以前好像有做過，但後來發現以前埋的那是 GA (google analytics)😅    
> 
> 於是，就去看了一些有關 GTM 的介紹文，想更深入了解的話推薦以下兩篇，第一篇是[行銷人看到的 GTM](https://doordata.tw/blog/gtm-tutorial-for-beginner) ，第二篇是[工程師看到的 GTM](https://dotblogs.com.tw/wasichris/2018/09/28/012643)，兩篇的內容都能理解的話，你就已經具備 GTM 的基礎開發能力了！    
> 
> 用一句話形容 GTM 就是 GTM 使網站追蹤數據變得更容易。

## Install Vue-gtm

vue2 → 重點是版本號後面要加上 vue2，當初開發的時候就踩了[這個坑](https://github.com/mib200/vue-gtm/issues/98)

```bash
npm install vue-gtm@3.0.0-vue2
```

vue3

```bash
npm install vue-gtm
```

## Use Vue Gtm

[vue-gtm github](https://github.com/gtm-support/vue-gtm) → 如果你需要更多的功能可以參考官方文件的參數設定 專案裡面我們用到的就是簡易的功能，並且不使用 vueRouter 這個參數，而是自己開發這一段，原因在[工程師看到的 GTM](https://dotblogs.com.tw/wasichris/2018/09/28/012643)裡面有提到，就是不希望與該套件的耦合性太高~

```jsx
import VueGtm from 'vue-gtm'

Vue.use(VueGtm, {
  id: process.env.VUE_APP_BASE_GTM_ID,
  // enabled: process.env.NODE_ENV === 'production',
  enabled: true,
  defer: false,
  debug: false,
  loadScript: true,
  trackOnNextTick: false
})
```

接著我們新增一個處理追蹤使用者軌跡的模組: trackService.js 這邊會做的事情就是拿到 `router.meta.trackedTitle` 的值，然後搭配自定義的 event 傳給 GTM 而已

```jsx
import Vue from 'vue'
import router from '@/router'

/**
 * @description 紀錄使用者軌跡
 * @param {String} trackedTitle 頁面標題
 */
const pageTracker = async ({ trackedTitle = '' } = {}) => {
  const { meta } = router.currentRoute
  const pageTitle = trackedTitle || meta.trackedTitle

  if (Vue.gtm && pageTitle) {
    Vue.gtm.trackEvent({
      event: 'content-view',
      pageTitle
    })
  }
}

export default pageTracker
```

在路由的 meta 裡面加上 trackedTitle，這邊可以依照專案需要設定自定義的值，基本上 GTM 也都只會是內部人員才能看得到，所以其實負責看 GTM 的人知道設定值代表的意義即可

```jsx
{
  path: '/login',
  name: 'Login',
  component: () => import('../views/LoginPage'),
  meta: { trackedTitle: 'Login' }
}
```

最後用 afterEach 這個鉤子去呼叫我們剛剛寫好的 function 就能完成這個功能了

```jsx
import pageTracker from '@/utils/trackService.js'

const createRouter = () =>
  new VueRouter({
    scrollBehavior: () => ({ y: 0 }),
    routes
  })

const router = createRouter()

router.afterEach(route => {
  setTimeout(() => {
    pageTracker()
  }, 500)
})
```

## P.S. this.$gtm

補充一下，由於我們在 main.js 使用了 `Vue.use(VueGtm)`，所以如果我們需要在專案裡面的某個組件例如一個按鈕或者一個表單上面加上像是 `pageTracker()` 的功能，我們其實也可以用以下的方法，就看大家怎麽管理自己的專案

```jsx
methods: {
	pageTracker() {
		this.$gtm.trackEvent({
	      event: ...,
	      ...
	    })
	}
}
```

tag: #Vue #GoogleTagManager 