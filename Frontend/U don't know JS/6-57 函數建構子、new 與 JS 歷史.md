我們已經了解物件和原型，原型繼承和原型鏈、物件屬性和方法，現在應該更深入的討論建立物件。

有一些方法可以建立物件，尤其是遇到要設定原型時，所以我們來談談有關函數建構子 "new" 和 JS 的歷史:

JS 被 Brendon Eich 創造，在那時候，很像現在，Google 和 Apple 在進行科技戰爭，而 Netspace、Microsoft、Oracle、Sun 則是程式語言之戰，JS 也是到最後才應用到瀏覽器程式語言，並不是一開始就這樣，但是最後成為他的主要用途。

其中一個行銷元素就是 JS 的這段程式碼
```js
var john = new Person()
```

這就跟我們現在要談的函數建構子 "new"  有關了

還記得前幾堂課我們用了錯誤的方法去設定原型(__proto__[5-54 暸解原型](5-54%20暸解原型.md))，現在這個方法就是正確在 JS 中建立物件，然後增加屬性的方法、和設定原型。
```js
function Person () {
	console.log(this)
	this.firstname = 'John'
	this.lastname = 'Doe'
	console.log('This function is invoked.')
}

var john = new Person()
console.log(john)
```

當我們用 new 的時候，一個空的物件被建立了，就像是 var a = {} 一樣，然後他用 Person() 呼叫函數，函數被呼叫以後，我們知道執行環境會產生 this 給我們，而這個 new 會改變 this 的指向，把 this 指向這剛剛建立的新物件，然後 firstname 和 lastname 會新增到這個物件上。

我們可以看到如果我們還沒對這個物件裡面的屬性賦值的話，他就會是一個空的物件(Person())    
![](./photo/Pasted%20image%2020221104131150.png)

```js
function Person () {
	this.firstname = 'John'
	this.lastname = 'Doe'
	return { greeting: 'i got in the way' }
}

var john = new Person()
console.log(john)
```

只要我們用函數運算子 "new" 的話，他就不會回傳值，改為回傳被 “new“ 運算子建立的物件，但是如果我們使用 return 回傳物件的話， 他就會改為回傳這個物件，所以其實他還是有執行 Person 這個函數，只是 "new" 運算子的關係讓他會回傳物件。
![](./photo/Pasted%20image%2020221104131255.png)

既然知道了上面提到的這些事情，我們就可以把程式碼改良，變成下面這樣
```js
function Person (firstname, lastname) {
	this.firstname = firstname
	this.lastname = lastname
}

var john = new Person('John', 'Doe')
var jane = new Person('Jane', 'Doe')

console.log(john)
console.log(jane)
```
這樣我們就可以新增不同的物件了    
![](./photo/Pasted%20image%2020221104172245.png)

所以這時候我們知道函數建構子的作用了: 
一個正常的函數用來建立物件，當你在呼叫函數前面放了 new 的話，在執行環境的創造階段被產生的 this 會指向新物件，當函數結束執行時，該物件會被函數自動回傳，這就是函數建構子。

tag: #JS #New #Proto #Prototype 