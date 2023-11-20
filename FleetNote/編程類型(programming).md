- 指令式編程(imperative programming): 詳細的命令機器怎麽(How)去處理一件事情以達到你想要的結果(What)。
- 聲明式編程(declarative programming): 只告訴你想要的結果(What)，讓機器自己去摸索過程(How)。
- [函數式編程(functional programming)](4-51%20函數程式設計(functional%20programming)(一).md): 函數式編程與聲明式編程是有所關聯的，他們的思想是一致的，都只關注想要的結果而不是怎麽做能達到這個結果，但函數式編程不僅僅侷限於聲明式編程，函數式編程是面向數學的抽象，將計算描述爲一種表達式求值。

### 指令式編程 vs 聲明式編程
#### 以生活中搭車到動物園爲例
- 指令式編程:
	1. 下個路口左轉
	2. 下個有紅燈的路口右轉
	3. 前進 100 公尺
	4. 在下個路口掉頭
	5. 前進 1500 公尺
	6. 到達動物園停車場
- 聲明式編程:
	1. 帶我到動物園

#### 以 Javascript 爲例
- 指令式編程 --> 會一步一步告訴機器程式該怎麽進行
```js
var array = [1,2,3]
var output = []
for(var i = 0; i < array.length; i++) {
  var tmp = array[i] * 2
  output.push (tmp)
}
console.log (output) //=> [2,4,6]
```
- 聲明式編程 or 函數式編程 --> 不需要詳細命令機器要做什麼，只告訴它我們需要一個 2 倍的輸出
```js
var array = [1,2,3,]
var output = array.map (function (n) {
  return n * 2
})
console.log (output) //=> [2,4,6]
```

tag: #編程 #JS #ImperativeProgramming #DeclarativeProgramming #FunctionalProgramming 