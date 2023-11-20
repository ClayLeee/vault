閉包(closure): 他是一個功能，確保你在執行函數的時候，他會正常運作，可以取用到外部變數，他不會在乎外部函數是不是已經運行完畢，跳出執行堆了。

簡單判定閉包在何種情況下會出現: 當一個函數裡面會回傳另外一個函數，而且裡面的函數擁有不在自己執行環境內的變數，而外面函數的執行環境內有這個變數，這個變數就會被包在閉包裡。

我們來段程式碼了解閉包的威力，然後再來探討他背後運行的機制
```js
function greet (whattosay) {
	return function(name) {
		console.log(whattosay + ‘ ‘ + name)
	}
}

console.log(greet(‘Hi’)(’Tony’))

var sayHi = greet(‘Hi’)
sayHi(’Tony’)
```
不管是直接把變數給 greet() 還是分兩次給它，他都可以成功執行    
![](./photo/Pasted%20image%2020221101225414.png)

---
但是這時候奇怪的事情已經發生了，為什麼 sayHi() 仍然知道 whattosay 是什麼?

因為他是閉包!

讓我們來看一下當程式執行時發生什麼事情:
1.  當程式開始時，會有全域執行環境，到 var sayHi = greet(‘Hi’) 的時候，他會呼叫 greet，這時候新的執行環境被創造出來， whattosay 這個變數就被設定在裡面，設值為 Hi。
2.  然後到 return 那行就立刻創造一個匿名函數並回傳他，回傳之後 greet 的執行環境就離開執行堆了。
3.  重點來了，whattosay 的記憶體空間仍然留在這裡。
4.  然後這時候回到全域執行環境的 sayHi(’Tony’)，他就創造了另外一個執行環境，name 這個變數被設定在裡面，設值為 Tony。
5.  接著執行到 console.log(whattosay + ‘’ + name) 那行，他看到 whattosay，他在這個函數裡面找不到，所以就跑去外部環境找，即使 greet 執行環境已經沒了，sayHi 執行環境依然還是能夠參考到 whattosay 留在那裡的記憶體空間，JS 會確保我的函數仍然能在範圍鏈([2-16 範圍鏈(scope chain)](2-16%20範圍鏈(scope%20chain).md))中找到這個變數，執行環境可以把它的外部變數包住，而這個包住所有可以取用的變數的現象，就叫做閉包。    
![](./photo/Pasted%20image%2020221101225646.png)

tag: #JS #ScopeChain #Closure #Global #ExecutionContext 