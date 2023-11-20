- Hoisting(提升)([2-10 執行環境-創造與提升](2-10%20執行環境-創造與提升.md))
—> 照理來說在宣告之前是不能用 a ( )，但 JS 允許在 function 和 var 宣告的情況下可以使用，而這個現象就叫做 Hoisting (提升)
```js
// 先使用名為a的function
a( )

// 再宣告
function a ( ) {
}
```

- let & const reassign([2-17 範圍(scope)、ES6與let](2-17%20範圍(scope)、ES6與let.md))
—> let 宣告出來的變數可以 reassign，const 不行 reassign
```js
let a = 1
a = 2 // true

const b = 1
b = 2 // error

const c = { x: 1 } // const 宣告的 object 就像房子一樣，他不允許你搬家，但允許你換傢俱

c.x = 2 // true —> 可以給你換傢俱
c = { x: 3 } // error —> 不給你搬家
```

- es6 shorthands 屬性縮寫
—> 沒有指定 x 和 y 的值的時候，他預設會自己往上找，就會找到 makePoint (x, y) 裡面的 x 和 y
```js
function makePoint (x, y) {
	return {
		x: x,
		y: y
	}
}
```
->
```js
function makePoint (x, y) {
	return {
		x, y
	}
}
```

- es6 shorthands 計算屬性
—> 在宣告一個物件的時候，假設他的 key 是動態的，可以用中括號”[ ]” ，代表中括號裡面是一個計算後的屬性
```js
function createObj(key, value) {
	const obj = {
		[key]: value
	}
}

const person = createObj(‘name', ‘Jason')
// {
// name: 'Jason'
// }
```

- es6 shorthands 函式縮寫
—> 在 Object 裡面的 function 可以直接省略 ": funciton “，用 “( )” 代替
```js
const options = {
	name: ‘Options’,
	level: 10,
	created: function () {},
	mounted: function () {}
}
```
->
```js
const options = {
	name: ‘Options’,
	level: 10,
	created (),
	mounted ()
}
```

---
#### es6 destructuring assignment 解構
—> 要解構陣列的時候用 ”[ ]” 將要解構的元素包起來，同理，要解構物件的時候用 “{ }” 將要解構的元素包起來

- 陣列解構
```js
const nums = [1, 2, 3]

// method 1
const first = nums[0]
const second = nums[1]
-------------------------------------
// method 2
const [first, second] = nums // first = 1, second = 2
```

—> 預設值 (default value) —> 長度不滿 3 的時候，讓第三位的預設值為 0
```js
const [first, second, third = 0] = nums
```

—> 忽略元素 —> “,” 前面留空就會忽略 array 裡對應的項目，可以直接取到沒有忽略的值
```js
const [, , third] = nums
```

—> 變數交換
```js
let a = 1
let b = 2

[a, b] = [b, a] // a = 2, b = 1
```

—> 剩餘部分 —> 用 ”…” 解構出來的，就會是除了此例中的 first 之外， array 剩餘的
```js
const nums = [1, 2, 3, 4]

const [first, ...others] = nums // first = 1, others = [2, 3, 4]
```

- 物件解構
```js
const point = {
	x: 100,
	y: 150
}

// method 1
const x = point.x
const y = point.y
---------------------------------------------
// method 2
const {x, y} = point // x = 100, y =150
```

—> 預設值
```js
const {x, y, z = 0} = point
```

—> 指派新名稱
```js
const {x: px, y: py} = point // px = 100, py = 150
```

—> 解構函式參數
```js
function distance (point) {
	return Math.sqrt(point.x * point.x + point.y * point.y)
}
```
->
```js
function distance (point) {
	const {x, y} = point
	return Math.sqrt(x * x + y * y)
}
```
->
```js
function distance ({x, y}) {
	return Math.sqrt(x * x + y * y)
}
```

---
#### string template 字串模板
1. 基本介紹    
	—> 使用 backtick(“`”)將字串模板包住，變數的地方則用 ”${ }” 包起來
	```js
	function greet (name) {
		console.log(‘Hello’ + name + ‘!')
	}
	```
	->
	```js
	function greet (name) {
		console.log(`Hello ${name}!`)
	}
	
	greet(‘Jack’) // Hello Jack!
	```
	
	—> 插入表達式
	-  Expression (表達式) —> 用小括號包起來可以使用的東西
	```js
	(1 + 2)
	```
	- Statement (陳述式) —> 用小括號包起來沒有意義的東西
	```js
	if (1 < 2), continue, break, return
	```
	ex:
	```js
	const a // statement
	(a + 2) // expression
	```

2. 字串模板中塞入表達式(普通表達式和三元運算)
	```js
	function greet (name, day) {
		console.log(`Hello, ${name}. It’s been ${days * 24} hours.`)
	
		console.log(`Hello, ${name}. ${(day < 7) ? ‘’ : ‘Long time no see.'}`)
	}
	
	greet(‘Jack’, 3) // Hello, Jack.
	greet(‘Jack’, 14) // Hello, Jack. Long time no see.
	```
	 —> 多行字串
	```js
	const words = ‘dddddddddddddddddd\n’ +
	‘dddddddddddddddddddddddd\n’ +
	‘dddddddddddddddddddddd’
	
	const words = `
		dddddddddddddd
		ddddddddddddddddd
		dddddddddddddddddddd
	` 
	```

--- 
#### arrow function 箭頭函式
—> 語法剪短
```js
var double = function (x) {
	return x * 2
}
```
-> 
```js
const double = (x) => {
	return x * 2
}
```

1. 傳入參數只有一個的時候可以把旁邊的括號省略，沒有參數或有多個參數的時候都不能省略
2. 函式本體是 return 且只有一行的時候，可以直接接在箭頭的後面
```js
const double = x => x * 2
```
ex1(map):
```js
arr.map(function(elm, idx){
	return elm + idx
})
```
—> 
```js
arr.map((elm, idx) => elm + idx)
```
ex2(addEventListener):
```js
btn.addEventListener(‘click’, () => console.log(10))
```

—> 自動綁定 —> 箭頭函式內部的 this 與外部相同
a 裡面的 this = aa 裡面的 this = 最外面 console.log 的 this
至於 b 裡面的 this 則要看情況才有可能等於 console.log 的 this
```js
const a = () => {
	console.log(this);
	const aa = () => {
		console.log(this);
	};
};

var b = function() {
	console.log(this);
};

console.log(this);
```
—> this
1.  是 function 的 context(情境)， this 是什麼東西取決於這個函式執行的情境
	-   直接執行: this 指向 window(global)
	-   作為物件的成員函式執行: this 指向該物件
	-   作為 dom 的偵聽函式執行: this 指向該 dom
	可以理解成 this 會指向調用的 function 前面一個元素 
2.  如果把 function 變成 arrow function 的話，三個例子中的 this 就都會指向 window(global)，所以結果都會顯示 Heisenburg
```js
var name = ‘Heisenburg’

var sayMyName = function () {
	console.log(this.name)
}

var teacher = {
	name: 'White'
}

teacher.sayMyName = sayMyName  

// <button id =“btn” name=“God damn right">Response</button>
sayMyName() // Heisenburg
teacher.sayMyName() // White
btn.addEventListener(‘click’, sayMyName) // God damn right

var sayMyName = () => {
	console.log(this.name)
}

sayMyName() // Heisenburg
teacher.sayMyName() // Heisenburg
btn.addEventListener(‘click’, sayMyName) //Heisenburg
```

tag: #JS #ArrowFunction #DestructuringAssignment #ES6 #Shorthands #StringTemplate #this #Hoisting #let 