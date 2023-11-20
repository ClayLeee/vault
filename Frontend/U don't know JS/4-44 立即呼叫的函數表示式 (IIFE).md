現在來說一個你常常會在大型框架和資源庫裡面看到的東西: IIFE(immediately invoked function expression)

他是怎樣運作的?
他為什麼很有用?
讓我們來看看

```js
// function statement
function greet (name) {
	console.log(‘Hello ‘ + name)
}
greet(‘John’)

// using a function expression
const greetFunc = function (name) {
	console.log(‘Hello ‘ + name)
}
greetFunc(‘John’)

// using a Immediately Invoked Function Expression (IIFE)
const greeting = function (name) {
	return ‘Hello ‘ + name
}(‘John’)
console.log(greeting)
```

IIFE 就是創造完後立即呼叫的函數，可以看到上面的例子裡的 greeting，之所以會在呼叫的時候使用 greeting 而不是 greeting()，是因為他立即呼叫以後函數執行完變成一個 string 了(Hello John)，所以如果你印出 greeting()，你會得到 error。    
![](./photo/Pasted%20image%2020221101223644.png)

接著來說一點很酷的東西，還記得表示式可以寫在程式碼裡面，即便他是毫無意義的嗎?
```js
3;

“I am a string.”;

{
	name: ’Tony'
};
```

這些都是有效的表示式，那我可以用函數的表示式嗎?
```js
function(name) {
	return ‘Hello ‘ + name
}
```
你會發現他會跑出這樣的 error，原因是當 JS 看到一個單獨的 function 字元在一行內，他會預期這是一個函數陳述句，所以不會讓你使用匿名函數，所以這個語法是有問題的，如果你寫了 function， 他不會是一個函數表示式，他是函數陳述句。    
![](./photo/Pasted%20image%2020221101223830.png)

所以我們要怎麼樣讓解析器了解，這個 function 帶頭的東西不要成為函數陳述句呢?

那就不要讓 function 當第一個字不就好了，沒錯，所以我們直接用 () 把它包起來。

還記得 () 的用途嗎? () 被用來包住一些表示式，像是 (3 + 4) * 2。

所以 JS 會知道你括號裡面的東西就是一坨表示式，他就會假設你寫的這個函數是函數表示式，你正在創造函數物件。

然後他就可以變成這樣~~
```js
var firstname = ‘John’

(function (name) {
	console.log(‘Inside IIFE: Hello ‘ + name)
}(firstname))
```
這樣你就完成了一個標準的 IIFE 了!！    
![](./photo/Pasted%20image%2020221101223950.png)

tag: #JS #IIFE