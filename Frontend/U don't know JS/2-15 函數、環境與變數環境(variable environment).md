變數環境(variable environment): 描述創造變數的位置，還有他在記憶體中和其他變數的關係，簡單說就是這個變數在哪裡?
1. 每個執行環境都有自己的變數環境
2. 每次呼叫函數, 就會得到這函數自己的執行環境

來看看底下的程式碼
```js
function b() {
	var myVar
	console.log(myVar)
}

function a() {
	var myVar = 2
	console.log(myVar)
	b()
}

var myVar = 1
console.log(myVar)
a()
console.log(myVar)
```

先執行全域執行環境裡面的宣告 myVar = 1 和 a()，然後 a 執行環境會堆到執行堆上，接著執行 a() 裡面的 myVar = 2 和 b()，b 執行環境再堆到執行堆的最上方，然後執行 b() 裡面的宣告 myVar。    
![](./photo/Pasted%20image%2020221029170442.png)

所以我們最後會得到以下的結果，要特別注意的是雖然 myVar 被宣告三次，他們是不同的、不一樣的、而且彼此沒有關聯的，因為每次呼叫函數，就會得到這函數自己的執行環境，所以可以看到在全域執行環境中，呼叫 a()，也不會影響到他原本在全域環境中設定的 myVar，最後的 console.log(myVar) 依然是 1。    
![](./photo/Pasted%20image%2020221029170512.png)

tag: #JS #VariableEnvironment
