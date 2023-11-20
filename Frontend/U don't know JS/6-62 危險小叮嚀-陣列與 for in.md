我們再來看一個陣列與原型有關的危險用法
```js
var arr = ['John', 'Jane', 'Jim']

for (var prop in arr) {
	console.log(prop + ': ' + arr[prop])
}
```
JS 的陣列和其他程式語言有點不同，我參考到 0, 1, 2 然後取用特定的陣列成員，這個 0, 1, 2 其實是個名字，所以我可以用中括號取用到他, 0, 1, 2 是名稱，那些英文名字是值。    
![](./photo/Pasted%20image%2020221107002620.png)

假設這個時候我新增一個和陣列有關的方法，我就會把原型裡面的屬性一起秀出來了，囧!
```js
Array.prototype.myCustomFeature = 'Cool!'

var arr = ['John', 'Jane', 'Jim']

for (var prop in arr) {
	console.log(prop + ': ' + arr[prop])
}
```    
![](./photo/Pasted%20image%2020221107002706.png)

結論就是遍歷所有的屬性並不是一件安全的事情，因為可能會遍歷到原型的屬性。

tag: #JS #Proto #Prototype 