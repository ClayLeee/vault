非同步(asynchronous): 非同步表示在同一個時間點不止一個，可能有一段程式在執行時會開始執行另一段程式碼然後又會再執行別的程式碼，簡單說就是同時執行不同段的程式碼，但是要注意 JS 本身是同步的，他不會非同步的執行。    

既然 JS 是同步的，那要怎樣處理非同步事件呢?    

首先我們要談到 JS engine，我們知道他並不是獨自存在的，例如瀏覽器，在這個環境下，有其它引擎和在 JS 引擎外執行別的程式， 像是呈現引擎(rendering engine)，還有像是回應 http 的請求，JS engine 可以跟他們互相溝通，但這些相對於 JS 都是非同步執行，只有 JS 引擎是同步的，意思就是 rendering engine、 JS engine、 http request 同時可以在瀏覽器內執行(非同步)，但是一旦進入 JS engine，就會變成同步的。    
![](./photo/Pasted%20image%2020221029174624.png)

---
JS 是如何處理這些瀏覽器內的非同步事件呢?    

當執行堆(stack) 是空的，JS 才會注意事件佇列(event queue)， 而那些非同步事件會進到事件佇列裡面去等待執行。

所以像下圖的執行順序會是: b() —> a() —> Global execution context —> click event —> http request    

而 http request 會等到 click event 在執行堆處理的程式碼跑完之後才從事件佇列進入執行堆    
![](./photo/Pasted%20image%2020221029174827.png)

---
讓我們來看看一段程式碼
```js
function waitThreeSeconds () {
	var ms = 3000 + new Date().getTime()
	while (new Date() < ms) {}
	console.log(‘finished function')
}

function clickHandler () {
	console.log(‘click event!')
}

document.addEventListener(‘click’, clickHandler)

waitThreeSeconds()
console.log(‘finished execution')
```
現在的問題是，當 waitThreeSeconds 這個需要花三秒的函數還在執行時，我們觸發點擊事件，程式碼中的三個 console.log 的出現順序是什麼?    
![](./photo/Pasted%20image%2020221029175003.png)

首先，我們可以看到 waitThreeSeconds 函數先被呼叫，所以 finished function 會先出現，等到這個函數跑完，離開執行堆，回到全域執行環境，接著會去跑第二個 console，因此出現 finished execution，到此執行堆的東西處理完畢，開始去注意事件佇列裡面的 click event，所以最後出現 click event!

> ### 總結: JS 本身是同步事件，在瀏覽器內的其他引擎或回應相對於 JS 才是非同步事件，而這些非同步事件被 JS 處理時首先會進入事件佇列(event queue)，等到執行堆(stack) 的東西都處理完畢之後，JS 才會按照事件發生順序一一處理，這讓我們知道長時間的函數可以干擾事件。

tag: #JS #AsyncChronous #Callback

