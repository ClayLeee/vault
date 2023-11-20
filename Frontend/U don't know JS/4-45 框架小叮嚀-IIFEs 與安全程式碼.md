所以我們假設我們有三個檔案 index.html, app.js, greet.js:
index.html
```html
<html>
	<head>
	</head>
	<body>
		<script src=“greet.js”></script>
		<script src=“app.js”></script>
	</body>
</html>
```
greet.js
```js
var greeting = ‘Hola’
```
app.js
```js
// IIFE
(function(name) {
	var greeting = ‘Hello’
	console.log(greeting + ‘ ‘ + name)
}(‘John’))
```
所以這個時候 greet.js 會堆在 app.js 上面，變成這樣:
```js
var greeting = ‘Hola’

// IIFE
(function(name) {
	var greeting = ‘Hello’
	console.log(greeting + ‘ ‘ + name)
}(‘John’))
```

我們來看看執行堆，當程式第一次被載入，我會有全域執行環境，他會執行 greeting.js 的那行，把一個 greeting 變數放在全域變數環境裡面，值是 Hola，而 app.js 裡面不會被看到因為他沒有函數陳述句([4-35 函數陳述句(function statement)與函數表示式(function expression)](4-35%20函數陳述句(function%20statement)與函數表示式(function%20expression).md))被提升，然後直到 IIFE 這一行，當他到了函數表示式的部分，他就會創造一個新的執行環境，然後 greeting 會放在這個執行環境裡面，值為 Hello，所以會存在兩個 greeting 在不同的執行環境和記憶體裡。    
![](./photo/Pasted%20image%2020221101224636.png)

---
這讓我們知道可以將程式碼包在 IIFE 裡面，就可以保證他不和其他東西衝突了!

你只要傳進去一個參數就可以了，因為物件是 call by reference，所以你把 window 傳進去賦值給另外一個變數就可以了，不了解的話建議你去複習 this 的 self ([4-37 物件、函數與 this](4-37%20物件、函數與%20this.md)) 概念。
```js
(function(global, name) {
	var greeting = ‘Hello’
	global.greeting = greeting
	console.log(greeting + ‘ ‘ + name)
}(window, ‘John’))

console.log(greeting)
```

這樣全域的 Hola 就被這個執行環境內的 Hello 給蓋掉了    
![](./photo/Pasted%20image%2020221101224959.png)

> ### 總結: 所以經過兩章的學習，我們可以為 IIFE 整理出幾個重點:
> 1.  在 () 裡面用函數表示式去創造並立馬執行就是 IIFE 。
> 2. 講程式碼包在 IIFE 裡面，可以讓裡面的變數和全域執行環境裡的變數不會產生衝突，因為 IIFE 會自己創造自己的執行環境。

tag: #JS #IIFE #this #Statement #Expression #ExecutionStack 
