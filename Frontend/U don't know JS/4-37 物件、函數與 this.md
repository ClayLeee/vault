還記得我們在執行環境中的這張圖嗎? 裡面還有一個 this 是我們還沒有學過的。
![](./photo/Pasted%20image%2020221031231304.png)

---
### this 這個東西會依據函數如何被呼叫而有所改變
1. 直接在瀏覽器中印出 this —> 指向全域環境中的 Window
	如果忘記了, 可以參考全域環境([2-9 全域環境與全域物件](2-9%20全域環境與全域物件.md)])
	```js
	console.log(this)
	```    
	![](./photo/Pasted%20image%2020221031231453.png)
2. 使用陳述句或是表示式創造函數，在裡面印出 this，然後呼叫他 —> 指向全域環境中的 Window
	```js
	function a () {
		console.log(this)
		this.newvariable = ‘hello'
	}
	a()
	console.log(newvariable)

	var b = function() {
		console.log(this)
	}
	b()
	```
	所以我也可以在函數裡面設定一個新的變數，然後在全域環境中呼叫他，他可以拿到你設定的值。    
	![](./photo/Pasted%20image%2020221031231638.png)
3. 在物件裡的方法(函數)印出 this —> 指向物件，也就是 c，簡單一點的判斷方法就是 “.” 前面的東西。
	```js
	var c = {
		name: ’The c object’,
		log: function () {
			this.name = ‘Updated c object'
			console.log(this)
		}
	}
	c.log()
	```
	所以你也可以在函數裡面使用 this 去改變物件裡面的其他屬性，你會發現他不是原本的 The c object。    
	![](./photo/Pasted%20image%2020221031231812.png)
4. JS 決定的特例(也是很重要的): 物件裡面的函數裡面的函數印出 this —> 指向全域環境中的 Window
	```js
	var c = {
		name: ’The c object’,
		log: function () {
			var setname = function (newname) {
				this.name = newname
			}
			setname(‘Updated again! The c object')
			console.log(this)
		}
	}
	c.log()
	```
	你會發現他沒有被改變，而且如果你跑去全域環境的 window 看，你會在裡面看到他😂    
	![](./photo/Pasted%20image%2020221031232012.png)
	
	window.name    
	![](./photo/Pasted%20image%2020221031232038.png)


---
所以出現了一種在實務上很常見到的做法，概念是源於物件是用 by reference([4-36 觀念小叮嚀-傳值(by value)和傳參考(by reference)](4-36%20觀念小叮嚀-傳值(by%20value)和傳參考(by%20reference).md)) 設定，我們可以在物件裡面的函數中宣告一個變數，有些人喜歡叫他 that，有些人會叫他 self，然後把他設值成 this，這樣一來 self 和 this 都會指向一樣的記憶體位置，也就是這個物件了!

補充: 物件裡面的函數裡面的函數的 self 並沒有在函數裡面被宣告， 所以他會到範圍鏈([2-16 範圍鏈(scope chain)](2-16%20範圍鏈(scope%20chain).md))上一層一層往外找，他會在物件的函數這一層找到 self，也就是 this，然後 this 會指向這個物件，所以才有辦法改到 c.name 的值。
```js
var c = {
	name: ’The c object’,
	log: function () {
		var self = this
		var setname = function (newname) {
			self.name = newname
		}
		setname(‘Updated again! The c object’)
		console.log(self)
	}
}
c.log()
```
這樣一改之後，就可以改到這個物件裡面的屬性了。    
![](./photo/Pasted%20image%2020221031232451.png)

最後解個小題目([This](../Javascript/This.md)), 確認自己都懂了!!

> ### 總結: 整篇都很重要，如果你不熟悉的話最好在複習一遍!

tag: #JS #Object #this #ByValue #ByReference #Global #ScopeChain #Function 