> 前情提要一下，這個功能是[線上計畫書專案](https://gitlab-iso3.iiidevops.org/root/proposal-editor)爲了預防使用者離開太久，基於保護資料安全，而誕生出來的功能，並且在登出之前會把網站上面未儲存的表單都先儲存後才自動登出。
> 大致上的概念就是這個 mixin 會偵測和記錄滑鼠移動事件發生的時間，然後我們設定一個鬧鐘去定時檢查，確認自上一次滑鼠移動之後過了多久，也就是使用者到底閒置多久。 ***如果超過一定的時間，我們就讓系統自動登出，或是先做什麼事情再登出。

以下就是專案中的 [mixin](https://v2.vuejs.org/v2/guide/mixins.html?redirect=true) ([@/mixins/MIxinTimeout.vue](https://gitlab-iso3.iiidevops.org/root/proposal-editor/-/blob/master/app/src/mixins/MixinTimeout.vue))，說明也用註解的方式放在程式碼裡面了

```js
<script>
import { mapActions } from 'vuex'

export default {
  data () {
    this.timeout = 30 * 60 * 1000 // 自動登出的間隔時間，這邊我們設定爲 30 分鐘
    return {
      lastTime: new Date().getTime(), // 上一次滑鼠移動的時間
      currentTime: new Date().getTime(), // 現在的時間
      intervalTimeout: null
    }
  },
  mounted() {
	// mounted 的時候加入偵測滑鼠移動事件的 onLoad() 還有我們的鬧鐘 setInterval()
    this.onLoad()
    this.setInterval()
  },
  methods: {
    ...mapActions('user', ['logout']),
	// 偵測滑鼠移動的事件，使用者移動滑鼠就會刷新存在 sessionStorage 裡的 lastTime
    onLoad() {
      window.document.onmousemove = () => {
        sessionStorage.setItem('lastTime', new Date().getTime())
      }
    },
	// 確認閒置時間有沒有超過我們的設定值
    async checkTimeout() {
      this.currentTime = new Date().getTime()
      this.lastTime = sessionStorage.getItem('lastTime')

      if (this.currentTime - this.lastTime > this.timeout) {
		// 閒置時間超過設定值要執行的邏輯，像是我們這邊是先更新資料再登出，可以換成你們要用的邏輯
         await this.updateData()
          .then(() => {
            this.logout()
            this.clearInterval()
            this.$router.push({ name: 'Login' })
          })
      }
    },
	// 更新資料的邏輯，這邊可以換成你們要用的邏輯
    async updateData() {
		await this.$refs.main.handleSaveUpdate(true)
    },
	// 一個每分鐘都會叫的鬧鐘，並且在生命週期 beforeDestroy 的時候清除鬧鐘，避免 error 發生
    setInterval() {
      const time = 1 * 60 * 1000 // 鬧鐘的間隔時間，這邊設定爲每 1 分鐘觸發一次 checkTimeout()
      this.intervalTimeout = window.setInterval(async () => await this.checkTimeout(), time)
      this.$once('hook:beforeDestroy', () => {
        this.clearInterval()
      })
    },
	// 清除鬧鐘
    clearInterval() {
      window.clearInterval(this.intervalTimeout)
      this.intervalTimeout = null
    }
  }
}
</script>
```

tag: #Vue #Mixin #Logout
