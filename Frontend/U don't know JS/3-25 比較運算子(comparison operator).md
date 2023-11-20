### 大於 & 小於

直接來看看一個例子，就能夠發現你自己是否了解比較運算子了
```js
console.log(1 < 2 < 3)
```
答案不會讓你很意外    
![](./photo/Pasted%20image%2020221029185344.png)

那如果現在是這樣呢?    
```js
console.log(3 < 2 < 1)
```
這就讓你意外了吧    
![](./photo/Pasted%20image%2020221029185424.png)

我們可以看到例子裡面的運算子都是一樣的，所以跳過優先性，直接來看看 “<“ 的相依性，而小於符號是左相依性。

所以 3 < 2 會先處理，他會變成 false，而 false 在 JS 裡面又可以被強制型轉成 0 (true 是 1)。

---
如果你不相信的話，可以在瀏覽器裡面試試這個。
```js
Number(‘3’)
```
和這個
```js
Number(false)
```
也就是說, 最後會變成 0 < 1，所以顯示出來會是 true。

我們了解背後運作的邏輯之後，可以重新回去看看第一個例子 console.log(1 < 2 < 3)。

即使他會回傳 true，但他肯定不是你想的那樣😂
  
那實際上是怎樣呢?

他會先處理 1 < 2，變成 true，這時候 JS 強制型轉 true 變成 1

然後 1 < 3，變成你最後看到的 true。

---
### undefined & null 的 coercion

然後我們來看看 JS 會怎麼強制型轉一些特例吧
```js
Number(undefined)
> NaN
```
NaN 表示 not a number，他的意思就是你有個東西想要轉成數值型別，但他不是數字，所以無法轉換成數值。

```js
Number(null)
> 0
```
null，JS 決定他是 0。

以上兩個特例告訴我們不是每次轉型的結果都很明顯和可以預測，所以雖然強制型轉的威力強大，但他可能很危險，會造成一些奇怪的 bug 出現。

---
### == & ===

== 會強制型轉兩個比較值，而 === 不會。

* == :
	這兩個例子都會是 true，因為當你用 "= ="的時候他偏偏就不轉成 0 
	```js
	3 == 3
    > true
	“3” == 3
    > true
	```
	所以也就是說
	```js
	false == 0
    > true
	```
	那 null 呢?
	null 的確會轉成 0，但是在使用 “= =“ 的時候他偏偏就不轉成 0
	```js
	null == 0
    > false
	null < 1
    > true
	```
	所以這邊其實有很多特殊情況，像是 undefined 和 null，不會照你的預期呈現!
* === :
	可以看到最後一個例子會變成 false，因為 “= = =“ 並不會幫你強制型轉
	```js
    3 === 3
    > true
    “3” === “3”
    > true
    “3” === 3
    > false
	```

---
> ### 總結: 為了避免出現奇怪的現象，當你在使用比較運算子的時候，遵守以下的原則吧
> 1.  試著比較那些你知道是同型別的東西
> 2.  用三等號(“===“) 來比較相等性, 不要用雙等號(“=="), 除非你是故意要讓他轉型
> 3.  JS 有一個很棒的東西, 叫做 [Object.is](http://object.is/), 可以玩玩看或是去 MDN 看看    

> 最後, 如果你想要知道那些奇怪的特例在比較的時候會變成什麼, 請看看偉大的 MDN 有一篇叫做[相等性比較](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Equality_comparisons_and_sameness)(equality comparison & sameness)

tag: #JS #ComparisonOperator
