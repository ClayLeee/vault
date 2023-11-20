先稍微把兩個字拆開來試著理解他是什麼意思
 * 範圍(scope): 代表我能夠取用這變數的地方
 * 鏈(chain): 外部環境參照的連結


是不是感覺很抽象    
沒關係，我們直接用三段程式碼搞清楚範圍鏈是什麼東西吧XD    

1. 在 b() 裡面不宣告 myVar，但是把它 console 出來，你覺得會是多少呢?
	```js
	function b() {
		console.log(myVar)
	}
	function a() {
		var myVar = 2
		b()
	}
	var myVar = 1
	a()
    ```
	答案是1，是你預期的嗎?    
	每一個執行環境都有特殊的東西被創造給你，記得還有一個東西我們還沒有提到嗎?     
	外部環境([2-9 全域環境與全域物件](2-9%20全域環境與全域物件.md) )的參照(the reference to the outer environment)，他會是 1 的原因就跟這個外部環境(outer environment) 有關。
	![](./photo/Pasted%20image%2020221029172220.png)    

	每一個執行環境有一個參照到它的外部環境，那麼在這個例子裡 a() 和 b() 的外部環境分別是什麼呢?    
	a() 和 b() 的外部環境都是全域執行環境!        
	
	我們可以用詞彙環境([2-6 觀念小叮嚀-語法解析器、執行環境與詞彙環境](2-6%20觀念小叮嚀-語法解析器、執行環境與詞彙環境.md))(lexical environment)去判斷它的外部環境，a() 和 b() 都是寫在全域執行環境，所以他們的外部環境都是全域執行環境。    
	
	當你需要某個執行環境內的程式碼的變數，如果他無法找到變數，他會到外部環境尋找變數，因為執行環境堆可以堆的很高，他需要一個個看他們的外部環境去尋找變數，直到全域等級，而這整條鏈子就叫做範圍鏈。    
	![](./photo/Pasted%20image%2020221029172625.png)

2. 現在改變 b() 的詞彙環境，看看會變得怎麼樣?
	```js
	function a() {
		function b() {
			console.log(myVar)
		}
		var myVar = 2
		b()
	}
	var myVar = 1
	a()
	b()
	```
	b() 的詞彙環境跑到 a() 裡面了，全域執行環境不再有 b()，所以會跑出 2 跟 error。    
	因為當創造階段([2-10 執行環境-創造與提升](2-10%20執行環境-創造與提升.md))(creation phase)，在全域執行環境沒有找到 b()，所以跑出那行 error。    
	![](./photo/Pasted%20image%2020221029173050.png)

	這個時候 b() 的物理位置(或詞彙環境)在 a() 裡，JS 會認定他的外部參照是 a()，所以他會在 a() 裡面尋找 myVar，也就是 2 囉～    
	![](./photo/Pasted%20image%2020221029173200.png)

3. 讓我們看最後一個例子，來驗收今天學習的成果
	```js
	function a() {
		function b() {
			console.log(myVar)
		}
		b()
	}
	var myVar = 1
	a()
	```
	答案是 1, 你答對了嗎?    
	b(）的外部參照是 a()，但在 a() 裡面找不到 myVar，沿著範圍鏈(scope chain)往執行堆下找，到全域執行環境裡面終於找到 myvar，所以出現 1。    
	![](./photo/Pasted%20image%2020221029173417.png)

tag: #JS #ScopeChain #Hoisting 