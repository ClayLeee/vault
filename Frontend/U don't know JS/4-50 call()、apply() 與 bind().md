函數就是物件，所以他可以有自己的屬性(property)和方法(methods or functions)([4-31 物件和物件實體](4-31%20物件和物件實體.md))，所有的函數都有 call()、apply()、bind()，而這三個函數都和你的 this 還有你傳入的參數有關。    
![](./photo/Pasted%20image%2020221102123009.png)

複習一下還記得物件裡面的方法裡面的 this 會指向誰嗎? 答案就是物件本身，如果忘記的話，建議你回去複習 this([4-37 物件、函數與 this](4-37%20物件、函數與%20this.md))，所以這裡的 this 就等於是指向 person。

然後我們來認真看一下 call(), apply(), bind() 有什麼不同:
-   bind() —> 傳入的第一個參數是你想要讓 this 指向的物件(person)，剩下的參數是你想要讓被綁定的函數裡的參數永久被設成什麼值(依序設定)，跟其他兩者不同的是，bind() 只會複製被綁定函數成為另外一個函數，然後把 this 指向傳進 bind 裡面的物件而已，他並不會執行出結果，所以通常會把它賦值給另外一個變數(logPersonName)，讓另外一個變數去呼叫(logPersonName())。
-   call() —> 傳入的第一個參數一樣是你想要讓 this 指向的物件，剩下的參數是你想要傳給函數的參數，跟 apply() 不同的地方是， call() 裡面的參數是依序傳入，相同的地方是他們兩者都會直接執行函數，這跟 bind() 不同。
-   apply() —> 傳入的第一個參數也還是一樣是你想要讓 this 指向的物件，但是 apply 只接受陣列作為參數，他就只有這一點跟 call() 不一樣而已，也就是說他也會直接執行函數，而陣列在數學運算比較強大，所以依據不同情況，你可以自行選擇要用 call() 或是 apply()。

```js
var person = {
	firstname: ‘John’,
	lastname: ‘Doe’,
	getFullName: function () {
		var fullname = this.firstname + ‘ ‘ + this.lastname
		return fullname
	}
}

var logName = function (lang1, lang2) {
	console.log(‘Logged: ‘ + this.getFullname())
	console.log(‘Arguments: ‘ + lang1 + ‘ ‘ + lang2)
	console.log(‘---------------')
}

// bind()
var logPersonName = logName.bind(person)
logPersonName(‘en’, ’tw')

// call()
logName.call(person, ‘en’, ‘es’)

// apply()
logName.apply(person, [‘es’, ’tw'])
```

然後我們還可以這樣做: 用函數表示式加上上面那三者其中之一立刻呼叫它
```js
(function(lang1, lang2) {
	console.log(‘Logged: ‘ + this.getFullname())
	console.log(‘Arguments: ‘ + lang1 + ‘ ‘ + lang2)
	console.log(‘-------------')
}).apply(person, [‘es’, ’tw'])
```

但你可能跟我一樣納悶，到底什麼時候會用到它啦😂

所以我們介紹兩個範例
- 第一個叫做借用函數(function borrowing) —> 用 apply() 或 call() 都可以
	```js
	// function borrowing
	var person = {
		firstname: ‘John’,
		lastname: ‘Doe’,
		getFullName: function () {
			var fullname = this.firstname + ‘ ‘ + this.lastname
			return fullname
		}
	}

	var person2 = {
		firstname: ‘Jane’,
		lastname: ‘Doe'
	}

	person.getFullName.apply(person2) // Jane Doe
	```
- 第二個是柯里函數(function currying) —> 跟 bind() 有關
	function currying 的意思就是建立一個函數的拷貝，並設定預設的參數，這讓你可以有基本的函數(multiply)，然後根據他放入預設參數(a)。

	bind 後面給的參數會設定成拷貝函數的永久參數值，例如下面例子的 a 參數就會永久被設定成 2，其實他做的事情就很像是下面例子註解掉的部分，當然你也可以設定不只一個參數變成永久參數值。
	```js
	// function currying
	function multiply (a, b) {
		return a * b;
	}

	function multipleByTwo (b) {
		var a = 2;
		return a * b;
	}

	var multipleByTwo = multiply.bind(this, 2);
	multipleByTwo(4); // 8
	var multipleByThree = multiply.bind(this, 3);
	multipleByTwo(4); // 12
	```

tag: #JS #Apply #Bind #Call #Currying