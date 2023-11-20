在 JS 裡面，我有函數陳述句和函數表示式，這兩者都很強大。

* 表示式(expression): 表示式是程式碼的單位，會回傳一個值。
	以下都是表示式，他們都會回傳一個值，你可以在瀏覽器裡面試試看
	```js
	a = 3
    > 3
	1 + 2
    > 3
	a = { greeting: ‘hi’ }
    > Object { greeting: ‘hi’ }
	```
* 陳述句(statement): 在執行階段被執行，他不會做任何的事情，也可以說不會回傳一個值的就是陳述句，在陳述句裡面，你可以有表示式。
    就像是 if 陳述句，他可以在裡面放 a === 3 這種表示式
    ```js
    if (a === 3) {}
	```

---
接著來看一個例子，我們分別用陳述句和表示式來做一樣的事情
- 陳述句:
```js
// 陳述句
function greet () {
	console.log(‘hi')
}

greet()
```    
![](./photo/Pasted%20image%2020221031224756.png)

- 表示式:
```js
// 表示式
const anonymousGreet = function () {
	console.log(‘hi')
}

anonymousGreet()
```
表示式可以等於函數是因為函數就是一種物件([4-34 函數就是物件](4-34%20函數就是物件.md))，還記得嗎?

而這個寫法我們沒有為 function 命名(function 和括號中間沒有名字)，他就是匿名函數。

有用到宣告這個動作產生出來的就一定是表示式
例如:
```js
var arr = [
	1,
	false,
	function(name) {
		console.log(name)
	}
]
```    
![](./photo/Pasted%20image%2020221031225132.png)

---
補充: 那如果這時候我們改變一下程式碼的順序，猜猜看會印出什麼(提示: 提升(hoisting)[2-10 執行環境-創造與提升](2-10%20執行環境-創造與提升.md))
```js
greet()

function greet () {
	console.log(‘hi')
}

anonymousGreet()

const anonymousGreet = function () {
	console.log(‘hi')
}
```
答錯的話代表你要回去複習提升了!!!

要記得函數就是一種物件，所以當一個變數被呼叫在他還沒被設值之前，他都會是 JS 給他的初始值 undefined。

總之，函數陳述句可以被執行在宣告前，而函數表示式要等到宣告他以後才能被呼叫。    
![](./photo/Pasted%20image%2020221031225358.png)

---
最後來看一點酷的東西，就是你可以把函數傳進另外一個函數，因為物件也可以傳進一個函數，那函數也一定可以!

這個就是一級函數([4-34 函數就是物件](4-34%20函數就是物件.md))的概念，你可以把函數傳入別處，把函數給另外一個函數，就像使用變數一樣，引入一個全新的型態到程式語言中，稱為函數程式語言(functional programming)。
```js
function log(a) {
	a()
}

log(function () {
	console.log(‘hi')
})
```

tag: #JS #FunctionalProgramming #Expression #Statement #FirstClassFunction #Hoisting #Object 