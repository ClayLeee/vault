### LocalStorage && SessionStorage
兩者的共同點:
1. 儲存大小都是 5MB 左右
2. 都有同源策略限制
3. 僅在==客戶端==中保存，不參與和服務器的通信
4. 用法:
	- [localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)
	- [sessionStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage)

兩者不同點:
1. 生命週期: 數據可以儲存多少時間
	- localStorage: 儲存的數據是永久性的，除非人爲刪除否則會一直存在。
	- sessionStorage: 一旦窗口或者標籤頁被關閉，所有通過 sessionStorage 儲存的數據都會被刪除。
2. 作用域: 誰擁有數據的訪問權
	- localStorage: 在同一個瀏覽器內，同源文檔之間共享 localStorage 數據，可以互相讀取、覆蓋。
	- sessionStorage: 與 localStorage 一樣在同一瀏覽器內可以共享同源文檔的 sessionStorage 數據，差別在於 sessionStorage 的作用域被限定在窗口中，也就是說，只有同一瀏覽器、==同一窗口==的同源文檔才能共享數據。

例如你在瀏覽器中打開了兩個相同地址的頁面A, B，雖然這兩個頁面的源完全相同，但是他們還是不能共享數據，因爲他們是在==不同窗口中==。

但是如果在同一個窗口中，有兩個同源的 iframe 元素的話，這兩個 iframe 的 sessionStorage 是可以互通的。

### Cookie
基本特點:
1. cookie 的大小大概只有 4KB 左右
2. 一般由==服務器==生成，可以設置失效時間，如果在瀏覽器生成 cookie，默認是關閉瀏覽器後失效
3. 每次都會攜帶在 HTTP 中，如果保存過多數據會帶來性能問題
4. 用法:
	- [cookie](https://developer.mozilla.org/en-US/docs/Web/API/Document/cookie)

> 總結:
> ![](./photo/Pasted%20image%2020221124171059.png)

tag: #LocalStorage #SessionStorage #Cookie