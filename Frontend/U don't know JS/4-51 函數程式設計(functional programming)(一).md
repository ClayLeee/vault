這一章我們來示範要怎樣設計你的函數，JS 有一級函數([4-34 函數就是物件](4-34%20函數就是物件.md))，表示我們可以實作函數程式設計，所以我們就從一些範例看看函數程式設計之美吧!

作為程式設計師，我們要最小化我們的工作，盡量少做重複的事情，所以我們會把事情放到函數裡面去做，假設我們想要做到像下面這樣子的功能: 得到一個新陣列，把原陣列的每一個參數都乘以 2 之類的功能
```js
var arr1 = [1, 2, 3]
var arr2 = [1]

for (var i = 0; i < arr1.length; i++) {
	arr2.push(arr1[i] * 2)
}
```
我們就來設計可以的達成上面功能的函數
```js
function mapForEach (arr, fn) {
	var newArr = []
	for (var i = 0; i < arr.length; i++) {
		newArr.push(fn(arr[i]))
	}
	return newArr
}

var arr1 = [1, 2, 3]

var arr2 = mapForEach(arr1, function (item) {
	return item * 2
})

var arr3 = mapForEach(arr1, funciton (item) {
	return item > 2
})
```
然後這個時候你可能想說，我想讓 arr3 裡面的判斷的數值也是我自己可以定義的，所以我就新增了一個 checkPastLimitm，接著用剛學到的 bind 去預設 limiter 參數。
```js
function mapForEach (arr, fn) {
	var newArr = []
	for (var i = 0; i < arr.length; i++) {
		newArr.push(fn(arr[i]))
	}
	return newArr
}

var arr1 = [1, 2, 3]

var checkPastLimit = function (limiter, item) {
	return item > limiter
}

var arr4 = mapForEach(arr1, checkPastLimit.bind(this, 1))
console.log(arr4)
```
我們就可以得到我們要的結果了    
![](./photo/Pasted%20image%2020221102181353.png)

然後這個時候你又可能想說，我只想要傳入限制值當作他唯一的參數， 所以我就新增了一個 checkPastLimitSimplified，裡面放跟 checkPastLimit() 一樣的 function 然後一樣用 bind 去設定 limiter 的預設值，最後用 arr5 去呼叫 mapForEach。
```js
function mapForEach (arr, fn) {
	var newArr = []
	for (var i = 0; i < arr.length; i++) {
		newArr.push(fn(arr[i]))
	}
	return newArr
}

var arr1 = [1, 2, 3]

// var checkPastLimit = function (limiter, item) {

// return item > limiter

// }

var checkPastLimitSimplified = function (limiter) {
	return function (limiter, item) {
		return item > limiter
	}.bind(this, limiter)
}

var arr5 = mapForEach(arr1, checkPastLimitSimplified(2))

console.log(arr5)
```    
![](./photo/Pasted%20image%2020221102181545.png)

所以寫到最後，我們其實就用 arr5 這樣一行的程式碼就可以得到我們要的結果，而且他是可以重複利用的。

> ### 總結:
> 1.  重點就是利用一級函數的功能，去分割我的程式碼，用更簡潔的方法寫出程式碼。
> 2.  當你把 data 傳入其他函數時，盡量在層級高一點的地方去改變，或是更理想的一點的話不要去改變它，像是上面的例子是回傳一個新陣列，而沒有去改變原本的陣列。

tag: #JS #FunctionalProgramming #Function