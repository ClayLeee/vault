JSON(JavaScript Object Notation): JSON 長得很像物件實體語法(object literal syntax)([4-31 物件和物件實體](4-31%20物件和物件實體.md))，因為事實上他就是受到物件實體語法啟發而創造出來的，但是他們兩個並不是同一個東西。

JSON 和 物件實體語法 的不同之處:
JSON 的屬性一定要被包在引號("”)裡面，而物件實體語法包或不包都是可以的，技術上來說，JSON 是物件實體語法的子集合，意思就是只要在 JSON 是有效的，在物件實體語法就是有效的，但不是所有的物件實體語法在 JSON 格式都是有效的。

幾年前，網路資料的傳輸格式很多，像是 XML，伺服器會得到類似下面的資訊，並且解析他，作為傳輸資料，他沒什麼問題，只是有一個致命的缺陷，就是當你處理下載的時候，下載速度和使用的頻寬是很重要的，而 XML 裡面的前後 tag 就是很不必要的符號(只傳送了一點點資料就要傳送屬性名兩次)，他會讓你的資料變得很龐大，因此大家開始使用 JSON，因為他是一個傳送資料的好格式。
```xml
// XML
<object>
	<firstname>Mary</firtstname>
	<isAProgrammer>true</isAProgrammer>
</object>
```
  
```json
// JSON
{
	"firstname": "Mary",
	"isAProgrammer": true
}
```

```js
// object literal syntax
const objectLiteral = {
	firstname: ‘Mary’,
	isAProgrammer: true
}
```

---
JS 內建功能 stringify 和 parse:
```js
const objectLiteral = {
	firstname: ‘Mary’,
	isAProgrammer: true
}
console.log(JSON.stringify(objectLiteral))
```

```js
const jsonValue = JSON.parse(‘{“firstname”: “Mary”, “isAProgrammer”: true}’)
console.log(jsonValue)
```
在瀏覽器得到的結果如下    
![](./photo/Pasted%20image%2020221031215539.png)

> ### 總結:
> 1.  JSON 和 物件實體語法雖然很像，但他們是不同的東西，JSON 比較嚴格。
> 2.  JS 內建功能 —> JSON.stringify(objectLiteral) 可以把物件轉換成 JSON 字串。
> 3.  JS 內建功能 —> JSON.parse(JSON 字串) 可以把 JSON 字串轉換成物件。

tag: #JS #JSON #JSONStringify #JSONParse #ObjectLiteral  
