參數(arguments): 一般來說，參數指的就只是你傳入函數的變數的另外一個稱呼而已，所以你也可以稱他為 parameters，在 JS 裡，參數是它已經幫你設定好的東西。

還記得在執行環境裡面會創造的東西嗎? this, outer environment...，其實還有一個參數(arguments) 也是 JS 會幫你創造在執行環境裡面。    
![](./photo/Pasted%20image%2020221101093329.png)

讓我們來看看參數為何有用?
我們可以不呼叫，也可以只呼叫兩個，也可以全部都呼叫，還記得 JS 會幫你把沒呼叫的變數設定成 undefined 嗎?
```js
function greeting (firstname, lastname, language) {
	console.log(firstname)
	console.log(lastname)
	console.log(language)
	console.log(arguments)
	console.log(‘--------------')
}

greeting()
greeting(‘clay’, ‘lee’)
greeting(‘clay’, ‘lee’, ‘en')
```
這時候注意看 arguments，你會發現它包含了所有你傳入函數的值， 你可能注意到了，他看起來跟陣列很像，但是陣列使用的是中括號， 但是 arguments 使用的是微微傾斜的中括號，我們稱之為像陣列的 (array-like)，這表示他的動作跟陣列相似，看起來也很像，但他不是陣列，因為他只有一部分陣列的功能。    
![](./photo/Pasted%20image%2020221101093540.png)

---
所以我們可以怎麼使用它呢?
1.  我們可以在函數裡面用 arguments 判斷傳入幾個參數決定要讓他執行不同的功能。
2.  我們也可以像陣列一樣取用他。
```js
function greeting (firstname, lastname, language) {
	if (arguments.length === 0) {
		console.log(‘Missing parameters!’)
		console.log(‘-----------')
		return
	}
	console.log(arguments[1])
	console.log(firstname + lastname + language)
}

greeting()
greeting(‘clay’, ‘lee’)
```
![](./photo/Pasted%20image%2020221101093702.png)

---
接著我們來看看一個新東西，叫做 spread parameter，這表示如果我有傳入函數的參數，我可以用 “...” 增加一個參數。
```js
function greeting (firstname, lastname, language, …other) {
	console.log(firstname)
	console.log(lastname)
	console.log(language)
	console.log(…other)
}

greeting(‘clay’, ‘lee’, ‘en’, ‘111 Main St.')
```
他就會把你多的參數都放進這個 array-like 的中括號裡面了!    
![](./photo/Pasted%20image%2020221101093844.png)

---
最後來補充關於函數的預設值，在實務上常見的做法，前面我們已經學過在函數裡面放入預設值([3-27 預設值](3-27%20預設值.md))的做法了，現在出現了一種做法是你可以直接在函數參數的地方給他預設值，是不是很方便!
```js
function greeting (firstname, lastname, language = ‘en’) {
	console.log(firstname)
	console.log(lastname)
	console.log(language)
}
```

tag: #JS #Arguments #Spread #DefaultValue 