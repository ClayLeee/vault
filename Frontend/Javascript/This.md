### 再次強調影響 this 的是在於函式呼叫的方法，並非宣告的時機。
Q:
```js
function callName() {
	console.log(this.name);
}

function callSomeone() {
	console.log("this is", this);
}

const name = "clay";
const auntie = {
	name: "ann",
	callName,
	watch: {
		name: "Magic Watch",
		callName
	}
};
const MingFamily = ["小明", "爸", "媽", callSomeone];
```
A:
```js
callName(); // undefinded

auntie.callName(); // ann

auntie.watch.callName(); // Magic Watch

MingFamily[3](); // this is ["小明", "爸", "媽", [Function: callSomeone]]
```

