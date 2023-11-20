typeof 是 JS 的一個功能，它可以幫助我們辨認一個變數的型別是什麼

instanceof 則是可以幫我們檢查某個物件是否在另外一個物件的原型鏈上

但是他們在辨別變數的時候會出現一些奇怪的現象，或甚至是存在 bug，所以我們都把他們列出來看看。

作者錄製影片的時候 null 還是有 bug 存在，用 typeof 會是 object， 但是現在測試會是 null。
```js
var a = 3
console.log(typeof a)

var b = 'Hello'
console.log(typeof b)

var c = {}
console.log(typeof c)


var d = []
console.log(typeof d) // weird
console.log(Object.prototype.toString.call(d)) // better

function Person (name) {
	this.name = name
}

var e = new Person('Jane')
console.log(typeof e)
console.log(e instanceof Person)
console.log(undefined) // makes sense
console.log(null) // a bug since, like, forever...

var z = function () {}
console.log(typeof z)
```

附上結果    
![](./photo/Pasted%20image%2020221107133028.png)

tag: #JS #Instanceof #Typeof