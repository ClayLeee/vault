* 運算子的優先性(precedence): 在同一行程式有不只一個運算子時，哪個運算子被優先運算
* 運算子的相依性(associativity): 當兩個運算子有相同的優先性時， 運算子計算的順序，左相依(left-to-right)或右相依(right-to-left)

也就是說，如果我們在同一行程式碼中有許多的運算子時，優先性能夠幫助判斷誰先運算，但如果全部的運算子優先性都相同呢?     

這時候就要看相依性    

現在讓我們來看看幾個例子
1. 不同優先性
	```js
	var a = 3 + 4 * 5
	console.log(a)
	```
	還記得國小在上數學課的時候老師有說過先乘除後加減嗎?     
	
	沒錯 JS 也有一套計算的優先順序，可以參考 MDN 的 [operator precedence](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Operators/Operator_Precedence)    
	乘法的優先度比加法還要高    
	所以答案會是 23    
2. 相同優先性 —> 就要考慮相依性
	```js
	var a = 2, b = 3, c = 4    
	a = b = c   
	console.log(a)    
	console.log(b)    
	console.log(c)    
	```
	最後答案是    
	![](./photo/Pasted%20image%2020221029184253.png)    
	我們可以去 MDN 的表裡面找 “=“ 這個運算子，會發現他是右相依性的    
	也就是說會從 b = c 開始處理，這時候 b 會等於 4    
	接著再來處裡 a = b，所以 a 也等於 4 —> a = b = 4 —> a = 4    
	所以最後大家都等於 4 了    

> ### 總結: JS 處理運算子的運算時，優先性高的會先處理，相同優先性的話，則會去看他們是左相依性還是右相依性。

tag: #JS #Associativity #Operator #Precedence
