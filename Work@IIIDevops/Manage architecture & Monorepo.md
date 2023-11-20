## Front-end development
- The front-end 20 years ago: pure html/css/js
- Recent 10 years: framework with a lot of dependencies(SCSS preprocessors、typescript、npm......)
- Maintain projects become more and more difficult

## Different manage ways
![](./photo/Pasted%20image%2020230419171425.png)
### Single-repo Monolith
![](./photo/Pasted%20image%2020230419175616.png)
- advantages: easy to manage, only a git repository
- disadvantages: complex projects have low code reusability and are not conducive to team collaboration
### Multi-repo
![](./photo/Pasted%20image%2020230419175040.png)
- advantages: modules are clearly divided, and each module is an independent repo, which is conducive to team collaboration
- disadvantages: many logics between projects are repeated causing the increased difficulty of code management
### Mono-repo
![](./photo/Pasted%20image%2020230419175822.png)
- advantages: easy to manage(only a git repository), sharing of common code, better developer testing
- disadvantages: codebase becomes bigger

## IIIDevOps Full & Lite Version
- Required
	- [Lerna](https://github.com/lerna/lerna): modern ==build system== for managing and publishing ==multiple== JavaScript/TypeScript packages from the ==same repository==
	- [Yarn](https://yarnpkg.com/) or [Npm](https://www.npmjs.com/)
- Optional
	- [Nx.js](https://nx.dev/) & [Nx Plugins](https://nx.dev/community#plugin-directory): next generation build system with first class ==monorepo support== and powerful integrations
	- [Storybook](https://storybook.js.org/): a frontend workshop for building UI components and pages in isolation
	- [Gitlab Packages & Registries](https://about.gitlab.com/): create reusable libraries & publish your own packages
### Folders
```shell
.
├── ./apps
│   ├── ./apps/full -> full IIIdevOps
│   └── ./apps/lite -> lite IIIdevOps
│   └── ./apps/shared -> share components
├── ./lerna.json
├── ./node_modules
├── ./nx.json
├── ./package.json
├── ./postcss.config.js
└── ./yarn.lock
```

./package.json
```json
{
+	"name": "@iiidevops/root",
	...
	"scripts": {
		"init": "yarn && yarn clean && yarn refresh",
		"clean": "lerna clean --yes && lerna exec -- rm -rf dist",
		"refresh": "lerna boostrap",
	+	"dev:lite": "lerna run dev --stream --scope=@iiidevops/lite-ui",
	+	"dev:full": "lerna run dev --stream --scope=@iiidevops/full-ui",
	+	"build:lite": "lerna run build:prod --stream --scope=@iiidevops/lite-ui",
	+	"build:full": "lerna run build:prod --stream --scope=@iiidevops/full-ui"
	},
	"devDependencies": {
		"lerna": "^6.6.1",
		"nx": "15.8.9"
	}
}
```
./apps/full/package.json
```json
{
+	"name": "@iiidevops/full-ui",
	...
	"scripts": {
		...
		"build:prod": "yarn run build:css && vue-cli-service build",
		"dev": "yarn run build:css && vue-cli-service serve"
	}
	...
},
```
./apps/lite/package.json
```json
{
+	"name": "@iiidevops/lite-ui",
	...
	"scripts": {
		...
		"build:prod": "yarn run build:css && vue-cli-service build",
		"dev": "yarn run build:css && vue-cli-service serve"
	}
	...
},
```
### Lerna Commands
Basic usage
```shell 
# full version for development
> yarn dev:full

# lite version for development
> yarn dev:lite

# full version for production
> yarn build:full

# lite version for development
> yarn build:lite
```

Add a package(install only `one package` each time)
```shell
# install module_1 to module_2
> lerna add <module_1> --scope=<module_2>
 
# install module_1 to module_2 devDependencies
> lerna add <module_1> --scope=<module_2> --dev

# install module_1 to module_2 peerDependencies
> lerna add <module_1> --scope=<module_2> --peer

# install module_1 to all the chunks without module_1
> lerna add <module_1>

# use yarn to install module_1 to module_2
> yarn workspace <module_2> add <module_1>@version
```

Remove a package
```shell
# remove module_1 from module_2
> yarn workspace <module_2> remove <module_1>
```

Common Commands
```shell
# publish a module
> lerna publish

# show local modules
> lerna list

# remove the node_modules from all of the chunks
> lerna clean
```

Others
[Lerna Commands](https://lerna.js.org/docs/api-reference/commands)

### How to use Shared Components
- Add alias in Vue config file of every Project in order to use Shared Components more convenient    
	vue.config.js
	```js
	configureWebpack: {
		name: name,
		resolve: {
			alias: {
				'@': resolve('src'),
				'themePath': resolve('src/styles/theme/', theme),
	+			'@shared': resolve('../shared')
			}
		}
	}
	```
- Use a Shared Component like the example    
	Navbar.vue
	```js
	<template>
		<LangSelect />
	</template>
	
	<script>
	import LangSelect from '@shared/LangSelect'
	
	export default {
		components: {
			LangSelect
		}
	}
	</script>
	```

### Other modifications
- Change Style `>>>` to `v-deep`

## P.S. Publish our own Package
[Example](http://gitlab-dev3.iiidevops.org/sharedlib/download-file)

1. Create a new Repository
2. `npm init`
3. Generate a Access Token & Copy: Gitlab > `Settings` > `Access Tokens`
   ![](./photo/Pasted%20image%2020230505102525.png)
	![](./photo/Pasted%20image%2020230505102835.png)
4. [Authenticating via the `.npmrc`](https://docs.gitlab.com/ee/user/packages/npm_registry/)
   ![](./photo/Pasted%20image%2020230505103302.png)
   ./.npmrc
	```
	@scope:registry=https://your_domain_name/api/v4/projects/your_project_id/packages/npm/ 
	//your_domain_name/api/v4/projects/your_project_id/packages/npm/:_authToken="${NPM_TOKEN}"
	```
5. Modify package.json
   - Remember to ==change version== if you want to republish, or it'll fail
	```json
	{
	  "name": "@sharedlib/download-file",
	  "version": "1.0.1",
	  "description": "download file from binary",
	  "main": "src/index.js",
	  "repository": {
	    "type": "git",
	    "url": "http://gitlab-dev3.iiidevops.org/sharedlib/download-file.git"
	  },
	  "publishConfig": {
	    "@sharedlib:registry": "https://gitlab-dev3.iiidevops.org/api/v4/projects/3144/packages/npm/"
	  }
	}
	```
6. Folder structure now will be like this
	```shell
	.
	├── ./src
	│	├── ./src/index.js
	├── ./.npmrc
	├── ./package.json
	```
7. Push and Publish it
	```shell 
    > git add *
    > git commit "..."
    > git push
    > npm publish
	```
8. Check your package: Gitlab > `Packages & Registries`
	![](./photo/Pasted%20image%2020230505105056.png)
9. Use your packages
```shell
> yarn add @sharedlib/download-file
or
> npm install @sharedlib/download-file
```

## P.S. Docker File with Lerna
[Managing Your Typescript Monorepo With Lerna and Codefresh](https://devpress.csdn.net/cloudnative/62f2e924c6770329307f737a.html)
```dockerfile
FROM dockerhub/library/node:16 AS build-stage

WORKDIR /app

# Install lerna globally using npm
RUN npm i lerna -g
 
# Copy your packages
COPY app/apps/full ./apps/full
COPY app/apps/shared ./apps/shared
 
# Copies package.json and package-lock.json to Docker environment
COPY app/package.json app/yarn.lock app/lerna.json ./

# Installs all node packages across packages
RUN yarn refresh
RUN yarn 

# Copies everything over to Docker environment
COPY .git .
COPY app .
RUN git rev-parse HEAD > git_commit && echo "1.29.0-dev" > git_tag && git log -1 --date=iso8601 --format="%ad" > git_date
RUN echo -e "\nVUE_APP_COMMIT='$(cat git_commit)'\nVUE_APP_TAG='$(cat git_tag)'\nVUE_APP_DATE='$(cat git_date)'" >> apps/full/.env.production

# Installs all node packages
# RUN yarn
RUN yarn build:full

FROM dockerhub/library/nginx:1.24-alpine-perl
COPY --from=build-stage /app/apps/full/dist /usr/share/nginx/html
COPY --from=build-stage /app/apps/full/default.conf.template /etc/nginx/templates/default.conf.template
ENV API_URL=http://devopsapi-service:10009/
RUN chmod -R 775 /usr/share/nginx/html
EXPOSE 80
```


## P.S. useful Lerna article
[lerna - JS package 管理工具](https://medium.com/lion-f2e/lerna-js-package-%E7%AE%A1%E7%90%86%E5%B7%A5%E5%85%B7-e9ed360d1143)
[Monorepo 的這些坑，我們幫你踩過了](https://blog.csdn.net/LuckyWinty/article/details/118485344)


