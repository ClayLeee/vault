* undefined 是一個 JS 內建特殊的值，是所有變數在創造階段會被設定的值也表示這個變數還沒被設定其他的值。

如果沒有宣告一個變數而呼叫他的話，因為這個變數沒有被設定到記憶體裡面, 所以會出現 error (參考 [2-10 執行環境-創造與提升](2-10%20執行環境-創造與提升.md) )。
```js
console.log(a)
```
![](./photo/Pasted%20image%2020221029155959.png)

---
反之，如果我在呼叫之前就有宣告一個變數，這個變數在創造階段的時候就被放進記憶體中，所以儘管呼叫的時候沒有設值，這個變數也會被 JS engine 賦值成一個特殊的值: undefined。
```js
var a   
console.log(a)   
```
![](./photo/Pasted%20image%2020221029160015.png)

---
如何證明 undefined 是一個 JS 的值呢？
```js
var a
console.log(a)
if (a === undefined) {
	console.log(‘a is undefined!')
} else {
	console.log(‘a is defined!')
}
```
會發現他跑進判斷式 if 的那一段裡面，證明了他的確是一個值。    
![](./photo/Pasted%20image%2020221029160035.png)

---
> ### 總結: 不要把一個變數設值成 undefined，這麼做對除錯有很大的幫助，原因是最好讓 undefined 這個特殊的值保有 JS 原有的意義: 表示我從未設定這個值，不然你會很難分辨這個 undefined 是你設定的還是 JS 幫你設定的。

tag: #JS #undefined
