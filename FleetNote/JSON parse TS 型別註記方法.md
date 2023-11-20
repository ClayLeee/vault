```js
let parsedJSON1 = JSON.parse(aJSONString) as { hello: string, luckyNumber: number }

let parsedJSON2 = <{ hello: string, luckyNumber: number}>JSON.parse(aJSONString)

let parsedJSON3: { hello: string, luckyNumber: number } = JSON.parse(aJSONString)
```

tag: #TS