我們來看看幾個被大量使用的開源資源庫: 
-   [underscore.js](https://underscorejs.org/)
-   [lodash.js](https://lodash.com/)
-   [ramda.js](https://ramdajs.com/)

如果可以的話，建議你花點時間去看看他們的原始碼，你會學到很多!

如果你在你的專案裡面下載了 underscore，你就可以嘗試來用用看
index.html
```html
<html>
	<head></head>
	<body>
		<script src=“underscore.js”></script>
		<script src=“app.js”></script>
	</body>
</html>
```
你可以這樣使用它來建立一些功能，
-  ```_```  代表的就是 underscore 的簡寫
- ```_.``` 之後的 map 或是其他方法都是 underscore 提供的

```js
var arr1 = [1, 2, 3];
var arr2 = _.map(arr1, function(item) {
	return item * 3;
});

var arr3 = _.filter([2, 3, 4, 5, 6, 7], function(item) {
	return item % 2 === 0;
});

console.log(arr2); // [3, 6, 9]
console.log(arr3); // [2, 4, 6]
```

tag: #JS #FunctionalProgramming #Lodash #Ramda #Underscore
