> 數位簽章這個功能雖然平常不太會使用到，但是如果需要用到的時候，背後要理解的原理也不少，所以趁還有印象的時候趕快補上這篇筆記，降低大家學習的門檻~
>
> 然後提醒一下如果需要開發這個功能或是更多的延伸功能，請先下載 [HiCOS 卡片管理工具](https://api-hisecurecdn.cdn.hinet.net/HiCOS_Client.zip)，它的功能是輔助憑證使用，依照步驟完成安裝後，要重新開機讓設定值生效。還有就是不能用 mac 開發，因爲 mac 不知道爲什麼無法讀取讀卡機資料，所以當初我也是在 windows 電腦上面重新設置環境開發的。
>
> 此功能主要是參考 [Hipki localserver](https://gpkiapi.nat.gov.tw/PKCS7Verify/) 對我們所需的呼叫方法進行封裝，有興趣研究更多也可以參考經濟部工商憑證管理中心的 [MOEACA](https://moeaca.nat.gov.tw/other/index.html) 平臺[檢測工具](https://moeacaweb.nat.gov.tw/MoeaeeWeb/other/checker.aspx)裡面使用的一支程式碼([ChtICToken.js](https://moeacaweb.nat.gov.tw/MoeaeeWeb/js/ChtICToken.js?ver638084256911862984))，以下我們只會把重心放在介紹[線上計畫書專案](https://gitlab-iso3.iiidevops.org/root/proposal-editor)裡面的 [hipki.js](https://gitlab-iso3.iiidevops.org/root/proposal-editor/-/blob/master/app/src/utils/hipki.js)，以及背後用到的一些原理。

## RSA 簽章與驗證原理

![](./photo/Pasted%20image%2020230317121058.png)

在非對稱加密演算法中，會有兩把鑰匙，如同上圖中金色的鑰匙是私鑰，銀色的則是公鑰。

使用其中一把鑰匙加密過的資訊，必需用另一把鑰匙才能解開。一般的使用情況是:

1.  發送者使用公鑰加密訊息，接收者使用私鑰解開密文。
2.  發送者使用私鑰簽署訊息，接收者使用公鑰驗證簽章。

我們這次要用的是第二種情況，簽章製作方就是我們扮演的角色，我們用私鑰產生出一組 hash 值，然後簽章驗證方就可以用公鑰產生出的 hash 值去比對 hash 值有沒有匹配，以此驗證簽章是不是還在可以使用的有效期限之內。

## Hipki 架構

![](./photo/Pasted%20image%2020230317121125.png)

介紹架構之前我們先想像一下使用者的操作情境，通常是電腦接上讀卡機，插上晶片卡(工商憑證 or 自然人憑證)，接著輸入卡片的 pin 碼，確認 pin 碼無誤之後就可以開放使用者進行簽章。

所以實際上架構可以拆分爲兩個步驟，第一個步驟就是我們需要電腦能讀取到讀卡機和憑證的資料，第二個步驟則是產生出簽章。

上圖是簡介進行簽章的過程:

1.  瀏覽器送出讀卡機和晶片卡資料的 request
2.  在 [localhost:61616](http://localhost:61616) (實際上是 [localhost:61161](http://localhost:61161)) 這個網址進行簽章處理，這時候就會跳出一個小視窗來顯示處理階段(它會不斷發出處理階段的事件，瀏覽器監聽事件會拿到特定的 key，從而顯示出不同的階段)
3.  產生出簽章，裡面比較重要的是數位簽章和晶片卡簽章憑證

上述過程先看過有個大概的理解就行，後面會再詳細補充~

---

## hipki.js

在程式碼中封裝的兩個方法:

1.  HipkiSlotInfo → 讀取讀卡機和憑證的資料
2.  Hipki → 產生簽章

分別對應了架構的中提到的兩個步驟，下面就來看看封裝的方法如何使用以及會得到什麼資料。

![](./photo/Pasted%20image%2020230317121159.png)

HipkiSlotInfo 不需要 input，只要接上讀卡機和插入晶片卡之後，程式碼就會自動抓到如上圖的 cardList 物件，物件裡面就是讀卡機的型號或是晶片卡的卡號之類的資料。

Hipki 的用法有兩種:

1.  產生字串簽章 使用情境 → 登入帳號需要與某張晶片卡建立關聯。

	```jsx
	const hipki = new Hipki({
		tbs: PLAIN,
		pin: PIN_CODE,
		slotDescription: SLOT
	})
	```
	
	![](./photo/Pasted%20image%2020230317121225.png)
	
	![](./photo/Pasted%20image%2020230317121249.png)

2.  產生檔案的數位簽章 使用情境 → 對檔案簽章，也是[線上計畫書專案](https://gitlab-iso3.iiidevops.org/root/proposal-editor)實際用到的功能。
	
	```jsx
	const hipki = new Hipki({
	  tbs: genRaw(hash),
	  tbsEncoding: 'base64',
	  pin: PIN_CODE,
	  slotDescription: SLOT,
	  signatureType: 'RAW'
	})
	```
	
	![](./photo/Pasted%20image%2020230317121313.png)
	
	![](./photo/Pasted%20image%2020230317121338.png)

補充一下不管用上面兩種方法的哪一種 cardInfo 得到的參數都是三個: cardSN、certb64 和 signature，其中的 certb64 就是剛剛介紹架構的第三個過程中提到的晶片卡簽章憑證，然後 signature 則是數位簽章。

接著就直接來看看封裝好的 [hipki.js](https://gitlab-iso3.iiidevops.org/root/proposal-editor/-/blob/master/app/src/utils/hipki.js)，方法前面有加上 “_” 符號的表示是給 class 內部使用的，所以其實真正外部引用會用到的方法就是 HipkiSlotInfo 的 getCardList()、Hipki 的 getCardInfo() 和把 hash 值轉成 RAW 的 genRaw() 而已，如果有興趣其他程式碼的作用的話可以打印每個方法裡面的變數或是回傳值出來看看，你就能快速理解了。

```jsx
export class HipkiSlotInfo {
  constructor(errorCallBack = () => {}) {
    this._error = errorCallBack
    this._mockImageRequest()
  }

  _convertToCardList(strJson) {
    const data = JSON.parse(strJson)
    if (!data || !data.slots) return
    const slotInfos = data.slots.reduce((array, slot) => {
      const slotInfo = {
        value: slot.token instanceof Object ? slot.slotDescription : '',
        display: slot.token instanceof Object
          ? `${slot.token.serialNumber}`
          : `讀卡機：${slot.slotDescription} 未插入卡片`
      }

      array.push(slotInfo)

      return array
    }, [])
    return {
      version: this._getVersion(strJson) || null,
      data: slotInfos || []
    }
  }

  _getVersion(strJson) {
    const data = JSON.parse(strJson)

    return data.serverVersion
  }

  _translateImageInfo(context) {
    let response = ''
    for (let i = 0; i < 2000; i++) {
      const data = context.getImageData(i, 0, 1, 1).data
      if (data[2] === 0) break
      response += String.fromCharCode(data[2], data[1], data[0])
    }

    if (!response) {
      response = JSON.stringify({ ret_code: '1979711501', message: '執行檔錯誤或逾時' })
    }

    return response
  }

  _mockImageRequest() {
    const ua = window.navigator.userAgent

    if (ua.indexOf('MSIE') === -1 && ua.indexOf('Trident') === -1) {
      const img = document.createElement('img')
      img.crossOrigin = 'Anonymous'
      img.src = '<http://localhost:61161/p11Image.bmp>'

      const canvas = document.createElement('canvas')
      canvas.width = 2000
      canvas.height = 1
      const context = canvas.getContext('2d', { willReadFrequently: true })

      img.onload = () => {
        context.drawImage(img, 0, 0)
        const response = this._translateImageInfo(context)
        this._cardList = this._convertToCardList(response)
      }

      img.onerror = () => {
        this._error()
      }
    }
  }

  getCardList() {
    if (this._cardList) {
      return Promise.resolve(this._cardList)
    } else {
      return new Promise(resolve => {
        setTimeout(() => {
          resolve(this.getCardList())
        }, 500)
      })
    }
  }
}

export class Hipki {
  constructor({
    tbs = '',
    tbsEncoding = 'NONE',
    pin = '',
    hashAlgorithm = 'SHA256',
    signatureType = 'PKCS1',
    slotDescription = '' }, errorCallBack) {
    this._params = { tbs, tbsEncoding, pin, hashAlgorithm, signatureType, slotDescription, func: 'MakeSignature' }

    this._error = errorCallBack
    if (window.addEventListener) {
      window.addEventListener('message', (e) => {
        if (e.origin !== '<http://localhost:61161>') return
        this._listener(e)
      }, { passive: false })
    }

    this._mockHipkiRequest()
  }

  _listener(event) {
    if (event.origin !== '<http://localhost:61161>') return
    const resp = JSON.parse(event.data)
    switch (resp.func) {
      case 'getTbs':
        this._target.postMessage(this._parameter, '<http://localhost:61161>')
        break
      case 'sign':
        if ((resp.signature && resp.certb64) || resp.last_error !== 0) this._cardInfo = resp
        break
      default:
        this._error()
        break
    }
  }

  get _parameter() {
    return JSON.stringify(this._params).replace(/\\+/g, '%2B')
  }

  _mockHipkiRequest() {
    const ua = window.navigator.userAgent

    if (ua.indexOf('MSIE') === -1 && ua.indexOf('Trident') === -1) {
      this._target = window.open('<http://localhost:61161/popupForm>', '簽章中', 'height=200, width=200, left=100, top=20')
    }
  }

  getCardInfo() {
    if (this._cardInfo) {
      return Promise.resolve(this._cardInfo)
    } else {
      return new Promise(resolve => {
        setTimeout(() => {
          resolve(this.getCardInfo())
        }, 500)
      })
    }
  }
}

export function genRaw(hash) {
  const sha256PaddingHeader = new Uint8Array([48, 49, 48, 13, 6, 9, 96, -122, 72, 1, 101, 3, 4, 2, 1, 5, 0, 4, 32])

  const contentLength = hash.length / 2

  const content = new Uint8Array(contentLength)

  for (let i = 0; i < contentLength; i++) {
    content[i] = parseInt(hash.substr(i * 2, 2), 16)
  }

  return _toBase64(_mergeU8Arr(sha256PaddingHeader, content))
}

function _mergeU8Arr(arr1, arr2) {
  const result = new Uint8Array(arr1.length + arr2.length)

  result.set(arr1, 0)
  result.set(arr2, arr1.length)

  return result
}

function _toBase64(u8arr) {
  const charCodeArr = u8arr.reduce((data, byte) => {
    return data + String.fromCharCode(byte)
  }, '')

  return window.btoa(charCodeArr)
}
```

![](./photo/Pasted%20image%2020230317121418.png)

至於剛剛提到的 getCardList() 和 getCardInfo() 的使用方法如下，唯一特別的就是要記得用非同步事件(await) 去等這兩個方法的資料回傳，原因如同架構中第二點提到的它會不斷發出處理階段的事件~ 要看更詳盡的使用方式可以參考[線上計畫書專案](https://gitlab-iso3.iiidevops.org/root/proposal-editor)的 [GenerateSignature.vue](https://gitlab-iso3.iiidevops.org/root/proposal-editor/-/blob/master/app/src/views/ApplicationSystem/components/ApplicationForm/GenerateSignature.vue)

---

最後再補充一下一個小知識，[線上計畫書專案](https://gitlab-iso3.iiidevops.org/root/proposal-editor)中在開發階段時爲了方便測試(很難拿到工商憑證)，所以開放支援自然人憑證和工商憑證，判別晶片卡是自然人憑證還是工商憑證的方法是卡號前面的兩個英文字，然後工商憑證跟自然人憑證目前都有新版跟舊版的區別:

工商憑證: 舊版(MG), 新版(MT)

自然人憑證: 舊版(TT), 新版(TP)

```jsx
<template>
	<el-switch
    v-model="isBusinessCertificate"
		active-text="工商憑證"
    inactive-text="自然人憑證"
	/>
</template>

export default {
	data() {
		return {
			isBusinessCertificate: false
		}
	},
	methods: {
		checkCardReaderType() {
		  const type = this.cardList.data[0].display.substr(0, 2)
		  if (this.isBusinessCertificate) {
		    if (type === 'MG' || type === 'MT') {
		      this.redWord = ''
		    } else {
		      this.redWord = `請使用工商憑證。`
		    }
		  } else {
		    if (type === 'TP' || type === 'TT') {
		      this.redWord = ''
		    } else {
		      this.redWord = `請使用自然人憑證。`
		    }
		  }
		}
	}
}
```

tag: #Vue #CardReader #HICOS 