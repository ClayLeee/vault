> 爲了讓路由之後能夠在 UI 上面給使用者自定義，於是開始整理 IIIDevOps 上面的四種登入權限: `AD`、`PM`、`RD`、`QA`，形成了動態路由的最初版本，登入網站後由後端的 API 給出全網站的路由表，經由前端的處理後，將左邊的 Sidebar 顯示出來，也定義了網站內全部的跳轉情境。

當初整理後的路由表是存在`特定的規則`的，所以於此記錄當初的一些規則，避免大家都有自己各自的寫法，從而造成路由表很雜亂，如果之後有需要調整路由表的時候，可供參考，此外如果看到後來的路由表沒有按照此規則，可以請後端修正，或是前端同仁討論後有更好的規則可以再修改此文件。

## Routes

1.  想看到現在的動態路由的話可以登入後打開網站的 devtool 找到 ui_router 這個 request
	![](./photo/Pasted%20image%2020230317115730.png)

2.  想在程式碼中打印出路由的話可以看看 @/store/modules/permission.js 這隻檔案，在裡面搜尋 `getRouter()`
    
    ```jsx
    const result = (await getRouter()).data
    ```
    

---

## Rules of Dynamic Routes

除了 [Vue Router](https://router.vuejs.org/zh/introduction.html) 本身的規則以外，下列是我們專案裡的路由規則和重點

1.  `path` 使用 `Kebab-case`, `path` 裡的傳參數用 `camelCase`
    
    -   correct
        
        ```json
        // path
        path: "/test-report"
        path: "/overview"
        path: "milestone"
        
        // 傳參數
        path: "/plan/:projectName?"
        ```
        
    -   wrong
        
        ```json
        path: "/systemResource"
        path: "projectSettings"
        ```
        
2.  `name` 使用 `PascalCase`, 並且每個路由的名字爲唯一值(雖然這是廢話，但還是提醒一下大家，如果調整後的路由不正確，有一種可能就是路由名字重複了)
    
    -   correct
        
        ```json
        name: "LandingPage"
        ```
        
    -   wrong
        
        ```json
        name: "dashboard"
        ```
        
3.  `hidden` 定義該路由需不需要顯示在 Sidebar 上
    
    -   ex
        
        <aside> 💡 AD 權限下的收件匣, 它的 children 裡面有兩個路由項目，Inbox 是顯示在 Sidebar 上面的收件匣, MessageConsole 因爲有寫 `“hidden”: true` ，所以並不會顯示在 Sidebar 上。
        
        </aside>
        
        ```jsx
        {
            "path": "/inbox",
            "component": "layout",
            "name": "Inboxs",
            "children": [
                {
                    "path": "",
                    "component": "views/Inbox",
                    "name": "Inbox",
                    "meta": {
                        "title": "Inbox",
                        "roles": [
                            "Administrator"
                        ]
                    }
                },
                {
                    "path": "message-console",
                    "name": "MessageConsole",
                    "hidden": true,
                    "component": "views/Inbox/MessageConsole",
                    "meta": {
                        "title": "MessageConsole",
                        "roles": [
                            "Administrator"
                        ]
                    }
                }
            ],
        		...
        }
        ```
        
4.  `component` 只有`三種可能`，如同以下，順便介紹使用情境
    
    -   `layout`
        
        <aside> 💡 路由表第一層絕大多數路由的 component 都是用 layout, 通常第一層的路由就是放在 Sidebar 上面的，除了三種測試報告(TestReports、DockerReports、SbomReports) 並不在 Sidebar 上，然後屬於第一層而沒有用 layout 當做 component 的只有 LandingPage 和 ErrorPage。
        
        </aside>
        
        ```json
        {
            "path": "/inbox",
            "component": "layout",
            "name": "Inboxs",
            ...
        }
        ```
        
    -   `layout/components/parentBlank`
        
        <aside> 💡 非第一層的路由如果還需要有子路由，component 就要用 layout/components/parentBlank。
        
        </aside>
        
        ```jsx
        {
            "path": "/plan/:projectName?",
            "component": "layout",
            "name": "Management",
            "children": [
                {
                    "path": "projectSettings",
                    "name": "ProjectSettings",
                    "component": "layout/components/parentBlank",
                    "children": [
                        {
                            "path": "",
                            "name": "ProjectSetting",
                            "component": "views/Plan/Settings/index",
        										...
                        },
                        ...
                    ],
        						...
                }
            ],
        		...
        }
        ```
        
    -   其他
        
        <aside> 💡 如果都不是前面兩種情況的話，就使用 Vue Router 的普通用法就行了。
        
        </aside>
        
        ```jsx
        {
            "path": "overview",
            "name": "Overview",
            "component": "views/Plan/Overview",
            ...
        }
        ```
        
5.  `redirect` 是指拜訪到我們設定的某個路由時，網站會轉址到我們指定的路由，一定要使用 name 來指定跳轉的路由，不要直接使用路徑
    
    -   ex
        
        <aside> 💡 如果進入 `/overview` 的時候，就會自動轉址到 `/overview/dashboard` 。
        
        </aside>
        
        ```jsx
        {
            "path": "/overview",
            "component": "layout",
            "name": "Overviews",
            "redirect": {
                "name": "Dashboard"
            },
            "children": [
                {
                    "path": "dashboard",
                    "name": "Dashboard",
                    "component": "views/Overview/Dashboard/roles/admin",
        						...
                },
                {
                    "path": "projectList",
                    "name": "ProjectList",
                    "component": "views/Overview/ProjectList/ProjectListPM",
        						...
                }
            ],
        		...
        }
        ```
        
    -   correct
        
        ```jsx
        "redirect": {
        	  "name": "Dashboard"
        }
        ```
        
    -   wrong
        
        ```jsx
        "redirect": "/dashboard"
        "redirect": "dashboard"
        ```
        
6.  `redirect` 只會存在有 `children` (子路由) 的路由中，而第一層的路由跟其他層的路由的設定方法有點不同，這兩種情況分別就是我們的路由中僅可能出現`兩種`設定方法
    
    -   第一層路由
        
        <aside> 💡 底下這個例子是 AD 權限上面總覽的路由，可以搭配畫面來看比較清楚。
        
        Overviews 就是第一層的路由(Sidebar)，儀表版跟專案列表是第二層，這時候第一層路由裡面就需要定義一個轉址的路由，這裡是轉址到 Dashboard。
        
        如此一來如果有使用者訪問了 `/overview` ，他就會直接進到儀表板的畫面。
        
        </aside>
        
        ![](./photo/Pasted%20image%2020230317115907.png)
        
        ```jsx
         {
            "path": "/overview",
            "component": "layout",
            "name": "Overviews",
            "redirect": {
                "name": "Dashboard"
            },
            "children": [
                {
                    "path": "dashboard",
                    "name": "Dashboard",
                    "component": "views/Overview/Dashboard/roles/admin",
        						...
                },
                {
                    "path": "projectList",
                    "name": "ProjectList",
                    "component": "views/Overview/ProjectList/ProjectListPM",
        						...
                }
            ],
        		...
        }
        ```
        
    -   其他層路由
        
        <aside> 💡 底下的路由也是來自 AD 權限的路由，它是測試記錄下的 API 測試(Postman)，Postmans 本身屬於第二層路由，它底下還有其他的子路由。
        
        這時候會把第二層的路由名稱後面加上 “s”，變成 `Postmans`，然後會放一個預設路由 `Postman` ，並且在第二層路由中設定 redirect 到 這個預設路由。
        
        另外兩個路由 DevopsTestCase 和 PostmanTestCase 則是在 Postman 頁面底下可能進入的路由。
        
        </aside>
        
        ```jsx
        {
            "path": "postmans",
            "name": "Postmans",
            "component": "layout/components/parentBlank",
            "redirect": {
                "name": "Postman"
            },
            "children": [
                {
                    "path": "",
                    "name": "Postman",
                    "component": "views/Scan/Postman",
        						...
                },
                {
                    "path": "devops/:id",
                    "name": "DevopsTestCase",
                    "component": "views/Scan/TestCaseDevOps",
        						...
                },
                {
                    "path": "postman/:id",
                    "name": "PostmanTestCase",
                    "component": "views/Scan/TestCasePostman",
        						...
                }
            ],
        		...
        }
        ```
        
7.  `meta` 底下目前只有`三個`設定項目
    
    -   `roles`
        
        <aside> 💡 用來定義可以訪問此路由的權限，分別有四種: `Administrator` 、`Project Manager` 、`Engineer` 、`QA` 。
        
        </aside>
        
        ```jsx
        meta: {
        		roles: ["Administrator"]
        }
        ```
        
    -   `title`
        
        <aside> 💡 定義要顯示在 Sidebar 和 Navbar 標題(頁面左上方，toggle sidebar 按鈕的右邊)的名字，需要把相對的名稱寫在 i18n 的檔案裡面, `i18n 的 key 要和 title 定義的 value 相同`。
        
        ![](./photo/Pasted%20image%2020230317120116.png)
        
        </aside>
        
        ```jsx
        meta: {
        		title: "Zap"
        }
        ```
        
        ```jsx
        export default {
        		route: {
        				Zap: 'OWASP ZAP',
        				...
        		},
        		...
        }
        ```
        
        ```jsx
        export default {
        		route: {
        				Zap: '黑箱測試(OWASP ZAP)',
        				...
        		},
        		...
        }
        ```
        
    -   `icon`
        
        <aside> 💡 非必填，通常只有第一層路由會用到這個 icon ，主要用途是 Sidebar 上面第一層左邊的小 icon, 可以在 [ElementUI 的 icon](https://element.eleme.cn/#/zh-CN/component/icon) 尋找需要用的 icon。
        
        ![](./photo/Pasted%20image%2020230317120152.png)
        
        </aside>
        
        ```jsx
        meta: {
        		icon: "el-icon-edit-outline"
        }
        ```
        

---

## P.S.

從 API 送來的路由表前端其實也不是直接使用，而是經過一些轉換過後才變成前端可以用的路由表，如果想要了解中間做了什麼轉換的話，可以去看看 `@/store/modules/permission.js`。

tag: #Vue #VueRouter outer #DynamicRoute
