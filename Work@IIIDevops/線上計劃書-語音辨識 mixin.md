> 這個模組的主要功能就是辨識語音，然後生成文字顯示在畫面上，提供使用者更方便的輸入方式。
> 
> 原本的程式碼是一支純 js 檔案，爲了要完美融入[線上計畫書專案](https://gitlab-iso3.iiidevops.org/root/proposal-editor)中，參考了[大神的程式碼](https://www.youtube.com/watch?v=ST7PKGe1NzA&ab_channel=TylerPotts)，順便精簡了一些內容和用不到的程式碼，最後改寫成了這一個 mixin。
> 
> 用到的核心技術就是 [web speech API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Speech_API/Using_the_Web_Speech_API)，原來家裡那些酷酷的智能傢俱，我也能夠實作，雖然我們專案不是要做像是[我說開燈就開燈](https://ithelp.ithome.com.tw/articles/10187827)這樣的功能，但難得有機會開發到語音辨識功能，我覺得還是蠻新奇的，所以我只好筆記下來，推薦大家去玩玩看了😆

以下就是 [MixinVoiceRecognition.vue](https://gitlab-iso3.iiidevops.org/root/proposal-editor/-/blob/master/app/src/mixins/MixinVoiceRecognition.vue) 的內容還有說明~

```js
<script>
const Recognition = window.SpeechRecognition || window.webkitSpeechRecognition

export default {
  data () {
    this.language = 'cmn-Hant-TW'
    return {
      recognitionStatus: {
        isRecording: false,
        ignoreOnend: false,
        disabled: false,
        startTimeStamp: ''
      },
      speechRecognition: new Recognition()
    }
  },
	// mounted 的時候初始化 Recognition()，和四種事件的處理邏輯
  mounted() {
    this.initSpeechRecognition()
    this.onStart()
    this.onEnd()
    this.onResult()
    this.onError()
  },
  methods: {
		// 唯一給外部使用的方法，把點擊事件的 event 接到這個方法的參數就可以了
    toggleMic(event) {
      if (this.recognitionStatus.isRecording) {
        this.speechRecognition.stop()
      } else {
        this.speechRecognition.start()
        this.speechRecognition.lang = this.language
        this.recognitionStatus.ignoreOnend = false
        this.recognitionStatus.startTimeStamp = event.timeStamp
      }
    },
		// 初始化
    initSpeechRecognition() {
      if (!('webkitSpeechRecognition' in window)) {
        this.showWarningMessage('upgrade')
        this.recognitionStatus.disabled = true
        return
      }
      this.speechRecognition.continous = true
      this.speechRecognition.interimResults = true
    },
		// 開始偵測語音事件
    onStart() {
      this.speechRecognition.onstart = () => {
        this.recognitionStatus.isRecording = true
        this.showWarningMessage('speakNow')
      }
    },
		// 結束偵測語音事件
    onEnd() {
      this.speechRecognition.onend = () => {
        this.recognitionStatus.isRecording = false
        if (this.recognitionStatus.ignoreOnend) return
        if (!this.item.content) {
          this.showWarningMessage('noSpeech')
        }
      }
    },
		// 處理偵測結果事件
		// 在這個專案裡面主要的處理邏輯就是讓偵測到的字可以連接起來，不會因爲重新偵測語音把原本的字刷掉
		// function 中間的內容可以替換成符合專案需要的邏輯
    onResult() {
      this.speechRecognition.onresult = (event) => {
        for (const element of event.results) {
          if (element.isFinal) {
            this.item.content += element[0].transcript
          }
        }
        this.item.content = this.capitalize(this.item.content)
      }
    },
		// 各種錯誤事件及顯示錯誤提示
    onError() {
      this.speechRecognition.onerror = (event) => {
        if (event.error === 'no-speech') {
          this.showWarningMessage('noSpeech')
        }
        if (event.error === 'audio-capture') {
          this.showWarningMessage('noMicrophone')
        }
        if (event.error === 'not-allowed') {
          if (event.timeStamp - this.recognitionStatus.startTimeStamp < 100) {
            this.showWarningMessage('blocked')
          } else {
            this.showWarningMessage('denied')
          }
        }
        this.recognitionStatus.ignoreOnend = true
      }
    },
		// 把第一個字元替換成大寫
    capitalize(text) {
      const first_char = /\\S/
      return text.replace(first_char, m => m.toUpperCase())
    },
		// 畫面上顯示不同情況的提示
    showWarningMessage(key) {
      let message = ''
      switch (key) {
        case 'speakNow':
          message = '請講話。'
          break
        case 'noSpeech':
          message = '沒有語音被偵測到。'
          break
        case 'noMicrophone':
          message = '找不到麥克風裝置，請確定麥克風設定。'
          break
        case 'denied':
          message = '沒有取得麥克風裝置錄音權限。'
          break
        case 'blocked':
          message = '麥克風裝置沒有被允許使用，請變更設定。'
          break
        case 'upgrade':
          message = '此版 Chrome 沒有支援語音辨識。'
          break
      }
      this.$message({
        title: this.$t('General.Alert'),
        message,
        type: 'warning'
      })
    }
  }
}
</script>
```

外部的使用方法的很簡單，就是把點擊事件的 event 接給 mixin 的 toggleMic() 就行了~ 詳細可以參考 [BasicTree.vue](https://gitlab-iso3.iiidevops.org/root/proposal-editor/-/blob/master/app/src/components/ContentTree/components/BasicTree.vue) 的使用方式

```js
<template>
	<el-button 
		@click="handleClick($event)"
	/>
</template>

<script>
import MixinVoiceRecognition from '@/mixins/MixinVoiceRecognition'

export default {
	mixins: [MixinVoiceRecognition]
	methods: {
		handleClick(event) {
			...
			this.toggleMic(event)
			...
		}
	}
}
</script>
```

tag: #Vue #WebSpeechAPI #Mixin 