我們之前已經看過如何用 JS 語言的功能，讓函數可以在不同情況下被不同的預設參數呼叫，就像是重載函數([4-40 框架小叮嚀-重載函數(function overloading)](4-40%20框架小叮嚀-重載函數(function%20overloading).md))

這堂課我們要展示如何利用閉包寫出更有威力、更有彈性的程式碼
  
底下的這個 makeGreeting function 就像一個工廠一樣，工廠的工作就是幫我回傳做事的函數，我們給這樣型態的函數一個名字，就是 function factories
```js
function makeGreeting (language) {
	return function (firstname, lastname) {
		if (language === ‘en’) {
			console.log(‘Hello ‘ + firstname + lastname)
		}

		if (language === ‘es’) {
			console.log(‘Hola ‘ + firstname + lastname)
		}
	}
}

var greetEnglish = makeGreeting(‘en’)
var greetSpanish = makeGreeting(‘es’)

greetEnglish(‘John’, ‘Doe’)
greetSpanish(‘John’, ‘Doe')
```

根據閉包的概念，我們知道 makeGreeting 裡面的 language 變數會被包在閉包裡面，如果忘記為什麼的話，可以去看看暸解閉包的簡單判定([4-46 暸解閉包(closure)(一)](4-46%20暸解閉包(closure)(一).md))，而且我們還知道 greetEnglish 和 greetSpanish 會創造出兩個記憶體空間不同的執行環境，所以最後 greetEnglish 會自己包著一個 language = ‘en’ 的閉包，然後 greetSpanish 會自己包著一個 language = ‘es’ 的閉包。    
![](./photo/Pasted%20image%2020221101232135.png)

tag: #JS #Closure #FunctionOverloading #FunctionFactories