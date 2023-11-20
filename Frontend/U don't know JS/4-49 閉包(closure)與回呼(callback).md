如果你有用過 jQuery 和 setTimeout，其實你已經不知不覺中就用過閉包了。

我們來談談閉包和回呼函數。

底下這個例子裡面包含了我們已經學到的許多東西，你看得出來嗎?
-   setTimeout 接受了一個函數，你把函數傳入另外一個函數，這樣就是一級函數(first-class function)([4-34 函數就是物件](4-34%20函數就是物件.md))的概念。
-   我們立刻就建立一個函數利用函數表示式([4-35 函數陳述句(function statement)與函數表示式(function expression)](4-35%20函數陳述句(function%20statement)與函數表示式(function%20expression).md))(setTimeout 裡面函數表示式的用法就像 IIFE 函數表示式([4-44 立即呼叫的函數表示式 (IIFE)](4-44%20立即呼叫的函數表示式%20(IIFE).md))的用法一樣)。
-   setTimeout 等了 3 秒然後才執行，這個就牽涉到非同步和 event queue([2-18 關於非同步回呼(asynchronous callbacks)](2-18%20關於非同步回呼(asynchronous%20callbacks).md))。
-   等到 sayHiLater 執行完離開後，setTimeout 裡面的 function 會到範圍鏈([2-16 範圍鏈(scope chain)](2-16%20範圍鏈(scope%20chain).md))找不在自己執行環境([2-10 執行環境-創造與提升](2-10%20執行環境-創造與提升.md))內的變數 greeting, greeting 這個變數就會被包在閉包([4-46 暸解閉包(closure)(一)](4-46%20暸解閉包(closure)(一).md))裡面。

```js
function sayHiLater () {
	var greeting = ‘Hi’
	
	setTimeout(function () {
		console.log(greeting)
	}, 3000)
} 

sayHiLater()
```

JQuery 也用到了同樣的東西 —> 函數表示式和一級函數
```js
// jQuery uses function expressions and first-class function
$(“button”).click(function () {

})
```

現在來說說回呼函數: 你呼叫一個函數，之後等他結束，那個函數回呼你給他的函數，簡單說就是我執行你，然後當你結束後執行另外一個函數，這個就叫做回呼函數。

例如: 我呼叫 tellMeWhenDone，等到 tellMeWhenDone 自己裡面的事情做完之後，就呼叫我傳給他的 function。
```js
function tellMeWhenDone (callback) {
	var a = 1000 // some work
	var b = 2000 // some work
	callback()
}

tellMeWhenDone(function () {
	console.log('I’m done')
})

tellMeWhenDone(function () {
	console.log(‘All done...')
})
```

tag: #JS #Closure #Callback #Function #Statement #Expression #IIFE #ScopeChain #Hoisting #AsyncChronous #Callback 