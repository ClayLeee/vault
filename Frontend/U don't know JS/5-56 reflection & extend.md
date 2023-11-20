reflection: 一個物件可以看到自己的東西，然後改變自己的屬性和方法。

JS 物件有能力可以看到自己的屬性和方法，我們可以藉由這樣使用一個很有用的模式，稱為 extend。

讓我們用一個例子來告訴你，reflection 和 extend 是什麼概念吧
```js
var person = {
	firstname: ‘Default’,
	lastname: ‘Default’,
	getFullName: function () {
		return this.firstname + ‘ ‘ + this.lastname
	}
}

var john = {
	firstname: ‘John’,
	lastname: ‘Doe'
}

// dont’t do this EVER! for demo purpose only!
john.__proto__ = person

for (var prop in john) {
	console.log(prop + ‘ ‘ + john[prop])
}
```

firstname 和 lastname 都看得清清楚楚，然後居然也看得到原型上面的 getFullName，所以這個 for in 去找屬性和方法不只會到物件去找，還會到原型裡面找。    
![](./photo/Pasted%20image%2020221103175957.png)

那如果我只想要知道物件裡面有什麼而已呢?
基本物件裡面有一個屬性叫做 hasOwnProperty 可以用
所以我們可以把 for in 改成這樣
```js
for (var prop in john) {
	if (john.hasOwnProperty(prop)) {
		console.log(prop + ‘ ‘ + john[prop])
	}
}
```

你就會發現 getFullName 不見了，但是其實重點是我們可以用這個方法檢視 john 物件，查看他的屬性，這就是 reflection。    
![](./photo/Pasted%20image%2020221103180333.png)

然後這個概念其實可以做一件很有意義的事情，可以補足原型繼承([5-53 觀念小叮嚀-古典(classical inheritance)和原型繼承(prototypal inheritance)](5-53%20觀念小叮嚀-古典(classical%20inheritance)和原型繼承(prototypal%20inheritance).md))，我們看看 underscore.js 是怎麼做到的吧

我們先把自己的程式碼改成這樣，最後一行的那個 extend 會將裡面包含的所有物件都結合起來，他會接受所有物件的屬性和方法，加到 john 物件裡面
```js
var person = {
	firstname: ‘Default’,
	lastname: ‘Default’,
	getFullName: function () {
		return this.firstname + ‘ ‘ + this.lastname
	}
}  

var john = {
	firstname: ‘John’,
	lastname: ‘Doe'
}

// dont’t do this EVER! for demo purpose only!
john.__proto__ = person

var jane = {
	address: ‘111 Main St.’,
	getFormalFullName: function () {
		return this.lastname + ‘ ‘ + this.firstname
	}
}

var jim = {
	getFirstName: function () {
		return firstname
	}
}

_.extend(john, jane, jim)
console.log(john)
```
你就可以看到 jane 物件和 jim 物件裡面的屬性和方法都被加到 john 物件裡面了    
![](./photo/Pasted%20image%2020221103180605.png)

那 underscore.js 是怎麼做到的呢?

所以其實這是一個閉包([4-46 暸解閉包(closure)(一)](4-46%20暸解閉包(closure)(一).md))，然後他還有用到參數(arguments)([4-39 參數(arguments) & spread](4-39%20參數(arguments)%20&%20spread.md))，跟一個我們從來沒看過的[void](https://kuro.tw/posts/2019/08/04/JS-%E5%86%B7%E7%9F%A5%E8%AD%98-%E4%BD%A0%E6%89%80%E4%B8%8D%E7%9F%A5%E9%81%93%E7%9A%84-void/)，不過他並不是很重要，這邊只要知道他是用來回傳 undefined 的就好了。

然後來講解一下這邊是在做什麼的，從第一個 if 開始，如果傳入的參數長度小於 2，就回傳原陣列，其實就等於是我原本只有一個物件，那我也不需要處理他就可以回傳了的意思，下一行 for loop 開始遍歷所有傳入的參數，source 是屬性，也就是 jane 和 jim，然後 keys 是原物件的屬性，像是 getFirstName 那些，l 就是所有元物件屬性的長度，最後再做一次 for loop，把 obj 用剛拿到的這些屬性按順序填進去，再回傳這個物件。
```js
var createAssigner function (keysFunc, undefinedOnly) {
	return function (obj) {
		var length = arguments.length
		if (length < 2 || obj == null) return obj
		for (var index = 1; index < length; index++) {
			var source = arguments[index],
			keys = keysFunc(source),
			l = keys.length
			for (var i = 0; i < 1; i++) {
				var key = keys[i]
				if (!undefinedOnly || obj[key] === void 0) obj[key] = source[key]
			}
		}
		return obj
	}
}
```

最後要提醒一件事情, 就是 JS 在 ES6 出了一個叫做 [extends](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Classes/extends) 的東西, 不要跟 underscore.js 裡面的 extend 搞混了!

tag: #JS #Extend #Reflection #HasOwnProperty #Closure #Arguments #Spread #ClassicalInheritance #PrototypalInheritance #Void