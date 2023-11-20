我們花一點時間了解 JS 沒有的，但很多程式語言有的東西，以及他為何不重要，他就是[重載函數](https://zh.wikipedia.org/wiki/%E5%87%BD%E6%95%B0%E9%87%8D%E8%BD%BD)(function overloading)。    
```js
function greet (firstname, lastname, language) {
	language = language || ‘en’
	if (language === ‘en’) {
		console.log(‘Hello ‘ + firstname + ‘ ‘ + lastname)
	}
	if (language === ‘es’) {
		console.log(‘Hola ‘ + firstname + ‘ ‘ + lastname)
	}
}

greet(‘John’, ‘Doe’, ‘en’)
greet(‘John’, ‘Doe’, ‘es')
```
我們可以把它改成下面這一種模式(pattern)，這樣一來我們就可以讓函數可以在不同情況下被不同的預設參數呼叫了。
```js
function greet (firstname, lastname, language) {
	language = language || ‘en’
	if (language === ‘en’) {
		console.log(‘Hello ‘ + firstname + ‘ ‘ + lastname)
	}
	if (language === ‘es’) {
		console.log(‘Hola ‘ + firstname + ‘ ‘ + lastname)
	}
}

function greetEnglish (firstname, lastname) {
	greet(firstname, lastname, ‘en')
}

fucntion greetSpanish (firstname, lastname) {
	greet(firstname, lastname, ‘es')
}

greetEnglish(‘John’, ‘Doe’)
greetSpanish(‘John’, ‘Doe')
```

tag: #JS #FunctionOverloading #Pattern

