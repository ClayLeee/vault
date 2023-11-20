#### chap 1: text input & textarea
單行用 input, 多行用 textarea
-  ``` v-model="xxx” === :value=“xxx” ``` 
```@input=“setMessage” (ex1 === ex2)```

input ex1 —>
```js
<template>
	<div id="app">
		<input type=“text” v-model=“message”/>
		<h1>{{message}}</h1>
	</div>
</template>

<script>
export default {
	data () {
		return {
			message: ''
		}
	}
}
</script>
```

input ex2 —>
```js
<template>
	<div id=“app”>
		<input type=“text” :value=“message” @input=“setMessage”/>
		<h1>{{message}}</h1>
	</div>
</template>

<script>
export defualt {
	data () {
		return {
			message: ''
		}
	},
	methods: {
		setMessage (event) {
			this.message = event.target.value
		}
	}
}
</script>
```

textarea ex1 —>
-   用 `<h1>` 不會換行，如果要換行要用 `<pre>`, 或是在 css 裡面加上 white-space: pre
```js
<template>
	<div id=“add”>
		<textarea v-model=“message”></textarea>
		<h1>{{message}}</h1>
	</div>
</template>

<script>
export default {
	data () {
		return {
			message: ''
		}
	}
}
</script>
```

#### chap 2: 打勾按鈕: checkbox
單選: 綁定 boolean 到 單個 checkbox 上
```js
<template>
	<div id=“app”>
		<input type=“checkbox” id=“chk” v-model="agree"/>
		<label for=“chk”>Agree</label>
		<h1>{{agree}}</h1>
		<button @click=“submit”> Submit</button>
	</div>
</template>

<script>
export default {
	data () {
		return {
			agree: false
		}
	},
	submit () {
		if (this.agree !== true) {
			alert(“Please agree”)
			return
		}
		...
	}
}
</script>
```

多選: 綁定同一個 v-model，陣列裡面就可以存放打勾項目的 value
```js
<template>
	<div id=“app”>
		<input type=“checkbox” id=“ck1” value=“morning” v-model="times"/>
		<label for=“chk1”>Morning</label>
		<input type=“checkbox” id=“ck2” value=“afternoon” v-model="times"/>
		<label for=“chk2”>Afternoon</label>
		<input type=“checkbox” id=“ck3” value=“evening” v-model="times"/>
		<label for=“chk3”>Evening</label>
		<h1>{{times}}</h1>
	</div>
</template>  

<script>

export default {
	data () {
		return {
			times: []
		}
	}
}
</script>
```

#### chap 3: 圓點按鈕: radio
綁定 radio button: value 不同, 綁定同一個 v-model
```js
<template>
	<input type=“radio” id=“r1” value=“male” v-model=“gender"/>
	<label for=“r1”>Male</label>
	<input type=“radio” id=“r2” value=“female” v-model=“gender"/>
	<label for=“r2”>Female</label>
	<input type=“radio” id=“r3” value=“others” v-model=“gender”/>
	<label for=“r3”>Others</label>
	<h1>{{gender}}</h1>
</template>

<script>
export default {
	data () {
		return {
			gender: ‘male'
		}
	}
}
</script>
```

補充: radio button 多選
```js
<template>
	<div id=“app”>
		<input type=“radio” id=“r1” :checked=“male” @click=“male !== male”/>
		<label for=“r1”>Male</label>
		<input type=“radio” id=“r2” :checked=“female” @click=“female !== female”/>
		<label for=“r2”>Female</label>
		<input type=“radio” id=“r3” :checked=“others” @click=“others !== others”/>
		<label for=“r3”>Others</label>
	</div>
</template>

<script>
export default {
	data () {
		return {
			male: false,
			female: false,
			others: false
		}
	}
}
</script>
```

#### chap 4: 下拉選單: select & option
單選下拉式選單: option 裡面的 value 可以綁定也可以不綁定, 不綁定的話就會直接用 option 中間的值當成 value
```js
<template>
	<div id=“app”>
		<select v-model=“year">
			<option disable value=“">Select Year</option>
			<option value="15">2015</option>
			<option>2016</option>
			<option>2017</option>
		</select>
		<h1>{{year}}</h1>
	</div>
</template>
  
<script>
export default {
	data () {
		return {
			year: ''
		}
	}
}
</script>
```

多選選單
```js
<template>
	<div id=“app”>
		<select v-model=“years” multiple>
			<option value=“15”>2015</option>
			<option value=“16”>2016</option>
			<option value=“17”>2017</option>
		</select>
		<h1>{{years}}</h1>
	</div>
</template>

<script>
export default {
	data () {
		return {
			years: []
		}
	}
}
</script>
```

多選選單使用 v-for
```js
<template>
	<div id=“app”>
		<select v-model=“selectedMonth”>
			<option v-for=“month in 12” :value=“month”>{{month}}</option>
		</select>
		<h1>{{selectedMonth}}</h1>
	</div>
</template>

<script>
export default {
	data () {
		return {
			selectedMonth: 1
		}
	}
}
</script>
```

#### chap 5: 檔案上傳與圖片預覽
沒辦法用 v-model 偵聽, 只能用 @change 偵聽他的改變事件, 使用 FileReader() 去取得預覽圖, 最後在綁定給 `<img>`

```js
<template>
	<div id=“app”>
		<input type=“file” @change=“fileSelected”/>
		<img v-if=“image” :src=“image” width=“200”/>
		<button @click=“upload”>Upload</button>
	</div>
</template>

<script>
export default {
	data () {
		return {
			image: ‘’,
			file: null
		}
	},
	methods: {
		fileSelected (event) {
			this.file = event.target.files.item(0)
			const reader = new FileReader()
			reader.addEventListener(‘load’, this.imageLoaded)
			reader.readAsDataURL(this.file)
		},
		imageLoaded (event) {
			this.image = event.target.result
		},
		upload () {
			const form = new FormData()
			form.append(this.file, this.file.name)
			axios.post(‘/upload’, form)
			or
			axios.post(‘/upload’, {image: this.image})
		}
	}
}
</script>
```

#### chap 6: 再來顆語法糖吧，修飾符！
v-model 的三種修飾符
-   lazy—> 打字時不會把 msg show 出來，等到打字完成 focus 消失，才會顯示出來
-   number —> 把 v-model 綁定的參數當成數字，不加的話會當成字串
-   trim —> 把 v-model 綁定的參數裡面的空白裁掉

lazy
```js
<template>
	<div id=“app”>
		<input type=“text” v-model.lazy=“msg”/>
		<h1>{{msg}}</h1>
	</div>
</template>

<script>
export default {
	data () {
		return {
			msg: ''
		}
	}
}
</script>
```
number
```js
<template>
	<div id=“app”>
		<input type=“text” v-model.number=“num”/>
		<h1>{{num + 1}}</h1>
	</div>
</template>

<script>
export default {
	data () {
		return {
			num: 0
		}
	}
}
</script>
```
trim
```js
<template>
	<div id=“app”>
		<input type=“text” v-model.trim=“text”/>
		<h1>{{text}}</h1>
	</div>
</template>

<script>
export default {
	data () {
		return {
			text: ''
		}
	}
}
</script>
```

#### chap 7: 你有後台我也有後台，來寫個後台上稿吧
html
```html
<div id=“app”>
	<form @submit.prevent="submit">
		<label>標題</label>
		<input type=“text” v-model=“title”/>
		<br/>
		
		<label>URL</label>
		<input type=“text” v-model=“url”/>
		<br/>

		<label>講師</label>
		<select v-model="teacher">
			<option v-for=“teacher in teachers”>{{teacher}}</option>
		</select>
		<br/>

		<label>完成</label>
		<input type=“checkbox” v-model=“completed”/>
		<br/>
  
		<label>圖片</label>
		<input type=“file” accept=“image/*” @change="fileSelected"/>
		<br/>
		<img v-if=“image” :src=“image” width=“200”/>

		<button type=“submit”>Submit</button>
	</form>
	<hr/>
	<div>
		<div v-for=“lesson in lessons”>
			<img :src=“lesson.image” width=“100”/>
			<a :href=“lesson.url”>
				<span>{{lesson.title}}</span>
			</a>
			<span>{{lesson.teacher}}</span>
			<span>{{lesson.completed ? ‘完成’: '未完成’}}</span>
		</div>
	</div>
</div>
```

js
```js
const API = ‘https://xxx'

new Vue({
	el: ‘#app’,
	data: {
		title,
		url: ‘’,
		teachers: [’scars’, ‘eason’, ‘jack’, ‘claire'],
		teacher: ‘’,
		completed: false,
		image: ‘’,
		lessons: []
	},
	methods: {
		fileSelected (event) {
			const file = event.target.files.item(0)
			const reader = new FileReader()
			reader.addEventListener(‘load’, this.imageLoaded)
			reader.readAsDataURL(file)
		},
		imageLoaded (event) {
			this.image = event.target.result
		},
		submit () {
			$.post(API, {
				title: this.title,
				url: this.url,
				teacher: this.teacher,
				completed: this.completed,
				image: this.image
			}).then(() => {
				this.title = ''
				this.url = ''
				this.teacher = ''
				this.completed = ''
				this.image = ‘’
				this.loadLessons()
			})
		},
		loadLessons () {
			$.get(API).then(lessons => this.lessons = lessons)
		}
	},
	mounted () {
		this.loadLessons()
	}
})
```

tag: #Vue #Checkbox #Input #Radio #Select