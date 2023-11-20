* [symbol 入門](https://es6.ruanyifeng.com/?search=Symbol&x=0&y=0#docs/symbol)
* [symbol 詳解](https://codertw.com/%E5%89%8D%E7%AB%AF%E9%96%8B%E7%99%BC/239376/)
* [symbol 使用時機](https://ithelp.ithome.com.tw/articles/10220499?sc=rss.iron)

symbol
1.  是一種 js 的資料型別
2.  可以用 symbol 生成唯一的標識符來避免命名衝突
3.  symbol 是不能夠遍歷的, 使用 Object.keys 拿不到他的 key， js 提供 Object.getOwnPropertySymbols 讓我們可以拿到他的 key  

```js
const classRoom = {
  [Symbol("lily")]: { grade: 60, gender: "female" },
  [Symbol("nina")]: { grade: 80, gender: "female" },
  [Symbol("nina")]: { grade: 90, gender: "female" }
}

const syms = Object.getOwnPropertySymbols(classRoom).map(sym => classRoom[sym])    
const keys = Object.keys(classRoom)    
```