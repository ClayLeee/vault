首先，我們要先搞懂兩個名詞的意義，傳值(by value) & 傳參考(by reference)(參考是到記憶體的位置)。
- 傳值:
	-   傳值就是當你把一個變數(a)設定給另外一個(b)時，b 並不會記住 a 記憶體的位置，而是拷貝a的值到另外一個新記憶體中，改變 a 並不會連帶改變 b 的值。
	-   JS 中的純值(primitive)[3-20 純值(primitive types aka 基本型別)](3-20%20純值(primitive%20types%20aka%20基本型別).md)適用。    
		![](./photo/Pasted%20image%2020221031230106.png)

- 傳參考:
	- 傳參考就是當你把一個變數(a)設定給另外一個(b)時， b 會直接記住 a 記憶體的位置，所以不管你後來改變 a 還是 b，他們兩個都會一起被改變。
	- JS 中的所有物件(包含函數)適用。    
		![](./photo/Pasted%20image%2020221031230227.png)

---
接著來看一段程式碼，更清楚的了解這兩個名詞
- 傳值 —> 改變 a 並不會連帶改變 b 的值
	```js
	// by value (primitive)
	var a = 3
	var b
	
	b = a
	a = 2
	
	console.log(a) // 2
	console.log(b) // 3
	```
- 傳參考 —> 不管你後來改變 a 還是 b，他們兩個都會一起被改變
	mutate —> 表示改變一個東西
	immutable —> 表示不可改變的
	```js
	// by reference (all object (including function))
	var c = { greeting: ‘hi’ }
	var d
  
	d = c
	c.greeting = ‘hello’ // mutate

	console.log(c) // Object { greeting: "hello" }
	console.log(d) // Object { greeting: "hello" }
  
	// by reference (even as parameters)
	function changeGreeting (obj) {
		obj.greeting = ‘Hola'
	}
	changeGreeting(d)
	console.log(c) // Object { greeting: "Hola" }
	console.log(d) // Object { greeting: "Hola" }
	```

--- 
特例: 等號運算子會設定一個新的記憶體空間，所以 c 被指向一個新的記憶體空間，c 跟 d 就不再相同了。
```js
// equals operator sets up new memory space (new address)
c = { greeting: ‘howdy’ }

console.log(c) // Object { greeting: "howdy" }
console.log(d) // Object { greeting: "Hola" }
```

---
> ### 總結:
> 1.  傳值就是當你把一個變數(a)設定給另外一個(b)時，b 並不會記住 a 記憶體的位置，而是拷貝a的值到另外一個新記憶體中，改變 a 並不會連帶改變 b 的值。
> 2.  傳參考就是當你把一個變數(a)設定給另外一個(b)時，b 會直接記住 a 記憶體的位置，所以不管你後來改變 a 還是 b，他們兩個都會一起被改變。
> 3.  JS 的純值都是 by value，所有的物件(包含函數)都是 by reference。
> 4.  等號運算子會設定一個新的記憶體空間。

tag: #JS #ByReference #ByValue #PrimitiveType 