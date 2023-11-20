我們已經知道用函數建構子建立物件的方法，我們知道函數建構子是為了模仿其他不能實作原型繼承的程式語言而設計，許多人認為在 JS 使用原型繼承比較好，而不要使用古典繼承。

現在說一種另外建立物件的方法，這沒有模仿其他程式語言，而且較新的瀏覽器都有內建: Object.create()。

Object.create() 會用原型建立空物件，他的原型就是你傳入 Obejct.create() 的東西。
```js
var person = {
	firstname: 'Default',
	lastname: 'Default',
	greet: function () {
		return 'Hi ' + this.firstname
	}
}

var john = Object.create(person)
john.firstname = 'John'
john.lastname = 'Doe'
console.log(john)
```

而這個模式你可以覆寫任何東西，只要新增屬性和方法到被建立的物件就好，這樣一來你就可以看到 john 物件裡面有 firstname 和 lastname，而且依然保有原型。      
![](./photo/Pasted%20image%2020221107092210.png)

然後用 john.greet() 也可以看到被覆寫的結果    
![](./photo/Pasted%20image%2020221107092231.png)

這就是純粹的原型繼承，沒有別的建立物件的概念，你就只是做出物件，然後在這物件之上做出新物件。

但我們說這是比較現代的瀏覽器可以使用的功能，如果你的專案需要支援較舊的瀏覽器或環境的話怎麼辦呢？

  

有個東西叫做 [polyfill](https://medium.com/@alexian853/polyfill%E6%98%AF%E4%BB%80%E9%BA%BC-bc07fd6eb880)，這個東西的功能間單說就是增加引擎缺少的功能到程式中，我們可以用程式去檢查引擎有沒有某個功能，如果沒有就幫他加上，讓他擁有新型瀏覽器的功能。

所以我們幫原本的程式加上下面這一段
```js
// polyfill

if (!Object.create) {
	Object.create = function (o) {
		if (arguments.length > 1) {
			throw new Error('Object.create implementation' + ' only accept the first parameter.')
		}
	
		function F () {}
		F.prototype = o
		return new F()
	}
}
```

> ### 結論:
> 1.  原型繼承就是建立一個物件，然後把他當作其他物件的原型，你可以依據需求覆寫屬性、新增功能。
> 2. polyfill 的功能是增加引擎缺少的功能到程式中，新手常常忽略這個東西是因為有一個很好用的套件叫做 babel-polyfill 幫你做好這些事情了。

tag: #JS #Polyfill #Prototype 