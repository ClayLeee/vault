直接來看看一些使用 JS 內建小功能的程式碼吧(並不建議在程式碼裡面使用，但是對於學習是很有幫助的!)

以下這些東西都表示不存在，所以都轉換成 false
```js
Boolean(undefined)
> false

Boolean(null)
> false

Boolean(“”)
> false
```
我們可以利用強制型轉的特性來檢查變數有沒有值，這讓我們可以在程式碼裡面使用一個很常見的判斷式
```js
var a
if (a) {
	console.log(’Something is there')
}
```

可是有一個小陷阱，那就是 0 也會被當成 false，但是 0 通常會是一個有效的值
```js
Boolean(0)
> false
```

所以我們可以視情況改良上面的程式碼變成這樣，如果 0 被當成有效值，且很常出現的話
```js
var a
if (a || a === 0) {
	console.log(’Something is there')
}
```

tag: #JS #Boolean
