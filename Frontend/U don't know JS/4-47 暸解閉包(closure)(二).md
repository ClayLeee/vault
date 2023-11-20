如果你在網路上面搜尋閉包，你一定會找到下面的這個經典例子

要特別注意在 push 裡面的 function 只是創造了，但是真正呼叫是在最底下的那三行 
```js 
fs[0]()
fs[1]()
fs[2]()
```
 如果你忘記為什麼會這樣的話，請回頭看看 閉包(一)([4-46 暸解閉包(closure)(一)](4-46%20暸解閉包(closure)(一).md)) 和 創造與提升([2-10 執行環境-創造與提升](2-10%20執行環境-創造與提升.md))
```js
function buildFunctions () {
	var arr = []
	for (var i = 0; i < 3; i++) {
		arr.push(
			function () {
				console.log(i)
			}
		)
	}
	return arr
}

var fs = buildFunctions()

fs[0]()
fs[1]()
fs[2]() 
```
當你看到這些程式碼，你可能會預期他會印出 1, 2, 3, 但是...   
![](./photo/Pasted%20image%2020221101230615.png)

這時候我們又要回到執行堆來看看他到底是怎麼一回事了:
1.  首先，全域執行環境先被創造，裡面包含了 buildFunctions() 和 fs。
2.  接著跑到 var fs = buildFunctions() 的時候呼叫這個函數，所以 buildFunctions 的執行環境被創造出來，裡面有兩個變數，i 和 arr，然後 for loop 執行， i 從 0 執行到 3，要特別注意這邊 push 進去 arr 的都是還沒有被呼叫的 function，所以最後會有三個 function 在 arr 裡面，不信的話你可以在瀏覽器隨便呼叫一個 fs[0]，你會看到像是下面那張圖的結果，所以當這個函數執行完畢，離開執行堆，記憶體中的 i 會停留在 3，然後 arr 裡面有三個 function。    
![](./photo/Pasted%20image%2020221101230758.png)
3. 接著到三個呼叫函數的地方，他們會一個一個創造執行環境，可是執行環境裡面並沒有 i，所以他就往範圍鏈去找，找到存在記憶體中的 i = 3，最後三個都會印出像上圖顯示的 3 個 3。

![](./photo/Pasted%20image%2020221101230853.png)

---
那如果我想要讓他能成功印出 0, 1, 2 的話該要怎麼做呢?

有兩種做法:
1.  使用 ES6 的 let —> 每次 for loop 執行時，let 讓 j 會變成記憶體中的新變數，所以當這個函數被呼叫的時候，每次都會指向不同的記憶體位置
	```js
	function buildFunctions () {
		var arr = []
		for (var i = 0; i < 3; i++) {
			let j = i
			arr.push(
				function () {
					console.log(j)
				}
			)
		}
		return arr
	}

	var fs2 = buildFunctions()

	fs2[0]()
	fs2[1]()
	fs2[2]()
	``` 
2. 使用 IIFE —> 另外一種思維是原本函數是因為被創造完沒有呼叫就被 push 進去 arr，導致呼叫的時候變數的執行堆早就離開，只剩下記憶體可以用，那我們創造完就立即呼叫不就好了嗎?是不是很熟悉，沒錯，這個就是 IIFE!
	```js
	function buildFunctions () {
		var arr = []
		for (var i = 0; i < 3; i++) {
			arr.push(
				(function (j) {
					return function () {
						console.log(j)
					}
				}(i))
			)
		}
		return arr
	}

	var fs2 = buildFunctions()

	fs2[0]()
	fs2[1]()
	fs2[2]()
	```

然後我們都可以得到一樣的結果了:
0, 1, 2, 萬歲!    
![](./photo/Pasted%20image%2020221101231250.png)

tag: #JS #Closure #IIFE #Hoisting 