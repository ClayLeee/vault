> 此功能是來自 [devops-ui-gitlab](http://gitlab-dev3.iiidevops.org/root/devops-ui) 的 [handleTime.js](http://gitlab-dev3.iiidevops.org/root/devops-ui/-/blob/develop/src/utils/handleTime.js)(@/utils/handleTime.js)，2022年 12月完成重構，主要是用來模組化專案裡面處理時間轉換的方法，建立一個前端的標準，同時也跟後端達成共識，讓 API 送來前端的時間格式都能固定。
> 這個模組用到了 dayjs 這個專門處理時間的套件，在使用這個模組前，請先參考以下三件你可能不知道但最好知道的事情(模組裡面已經有英文的說明，如果覺得還是不夠清楚的話，下面就是中文的詳細說明)~

- 世界協調時間(UTC time) vs 臺灣當地時間(Taiwan Local time) UTC time 是世界協調時間，簡單說就是世界上時間的標準，想瞭解更多的話可以去 google 看看，或是稍微看一下[維基](https://zh.wikipedia.org/zh-tw/%E5%8D%8F%E8%B0%83%E4%B8%96%E7%95%8C%E6%97%B6)怎麽解釋的，而所有來自後端的時間都是 UTC +00:00 的時間。
  至於臺灣所屬的時區則是歸類在 UTC +08:00，也就是說如果目前 UTC +00:00 的時間是 2022/11/2 09:30:00 的話，轉換成臺灣時間就應該是 2022/11/2 17:30:00 。 

- dayjs 的時間格式 系統裡面要顯示的時間可能會長成各種不同的樣子，有可能是完整的日期和時間(ex: 2022/11/2 09:30:00)，也有可能只需要時間(ex: 05:30 PM)，甚至是只需要相對時間(ex: 6個月前)。
  dayjs 提供了許多轉換時間的方法，像是要使用 UTC 時間的話可以用 [utc()](https://day.js.org/docs/en/manipulate/utc)，要用當地時間就可以用 [local()](https://day.js.org/docs/en/manipulate/local)，如果需要得到上面那些長成不同樣子的時間，我們就要用到 [format()](https://day.js.org/docs/en/display/format) 去轉換了，這邊舉一個例子，比如說我需要”05:30 PM”這樣的時間格式，那我在 format 裡面就要代入(”hh: mm A”，如同底下的範例，需要其他的格式的話請參考[官方文件](https://day.js.org/docs/en/display/format)。
	```js
		const time = dayjs.utc(utctime).local().format("hh:mm A")
	```

- dayjs 可以處理 [ISO-8601](https://en.wikipedia.org/wiki/ISO_8601) 格式 [dayjs](https://day.js.org/docs/en/parse/string) 官方文件表明不管我們後端給的 UTC 時間中間是用 “T”([YYYY-MM-DDTHH:mm:ss](YYYY-MM-DDTHH:mm:ss)) 或是一個空格(`<YYYY-MM-DD HH:mm:ss>`)，dayjs 都是可以接受並且正確的轉換成我們要的時間樣式，但是前面有提到後端有共識要把所有 API 裡面的時間都統一格式，所以前端一律只會拿到像是 [YYYY-MM-DDTHH:mm:ss](YYYY-MM-DDTHH:mm:ss) 這樣的 UTC 時間。
  另外補充一下中間有”T”的才是符合 ISO-8601 的格式，省略”T”用空格代替的格式曾經一度有列入 ISO-8061 的規範中(ISO-8601:2004)，但是自從 ISO 8601-1:2019 之後就從規範中移除了。

---
以下是 handleTime.js 的程式碼以及方法介紹

- getLocalTime: 用 UTC 時間取得當地時間
- getRelativeTime: 用 UTC 時間跟實際時間比較取得相對時間
- getFormatTime: 用 UTC 時間取得特定格式的 UTC 時間
- isTimeValid: 檢查輸入的 UTC 時間是否符合 dayjs 可以處理的格式
- getDurationTime: 比較兩個 UTC 時間取得相對時間

```js
import dayjs from 'dayjs'
import dayjsRelativeTime from 'dayjs/plugin/relativeTime'
import utc from 'dayjs/plugin/utc'
import duration from 'dayjs/plugin/duration'
import 'dayjs/locale/zh-tw'

dayjs.extend(dayjsRelativeTime)
dayjs.extend(utc)
dayjs.extend(duration)

/**
 * Get local time from utc
 * @param {string} utcTime - utc time
 * @param {string} format - default value would be 'YYYY-MM-DD HH:mm:ss'
 * @returns {string} localTime
 */
export const getLocalTime = (utcTime, format = 'YYYY-MM-DD HH:mm:ss') => {
  const localTime = dayjs
    .utc(utcTime)
    .local()
    .format(format)
  return isTimeValid(utcTime) ? localTime : '-'
}

/**
 * Get the time difference between the incoming time and the actual time
 * @param {string} utcTime - utc time
 * @returns {string} relativeTime
 */
export const getRelativeTime = (utcTime) => {
  const relativeTime = dayjs
    .utc(utcTime)
    .local()
    .fromNow()
  return isTimeValid(utcTime) ? relativeTime : '-'
}

/**
 * Get the utc time with format
 * @param {string} dateTime - date time
 * @param {string} format - default value would be 'YYYY-MM-DD HH:mm:ss'
 * @returns {string} formatTime
 */
export const getFormatTime = (dateTime, format = 'YYYY-MM-DD HH:mm:ss') => {
  const formatTime = dayjs
    .utc(dateTime)
    .format(format)
  return isTimeValid(dateTime) ? formatTime : '-'
}

/**
 * Check if time is valid
 * @param {string} time
 * @returns {boolean} - true or false
 */
export const isTimeValid = (time) => {
  return dayjs(time).isValid()
}

/**
 * Get the duration time by two point-in-time
 * @param {string} startTime
 * @param {string} endTime
 * @returns {string} durationTime - ex: 1分鐘、幾秒
 */
export const getDurationTime = (startTime, endTime) => {
  if (endTime === null || endTime === undefined) {
    return '-'
  }
  const s = dayjs.utc(startTime).unix()
  const e = dayjs.utc(endTime).unix()
  return dayjs.duration(e - s, 'seconds').humanize()
}
```

tag: #JS #Module #DayJS 
