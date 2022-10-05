## 前端技术发展

html

css

css预处理器：

- less
- sass

javascript:

- 原生js开发：按照ECMAScript标准的开发方式，简称ES

Typescript ：微软的标准：

js框架：

- jqurery: 简化dom操作。
- angular
- react
- **vue：**
    - 计算属性--虚拟化DOM
    - MVVM+虚拟DOM
- Axio：前端通信模型，类似AJAX，因为Vue的边界很明确，就是为了处理DOM，并不具备通信能力，所以使用Axio，也可以使用AJAX，vue推荐使用Axio。

UI框架：

- Bootstrap:twitter推出的一个用于前端开发的开源工具包
- **ElementUI**，iview，Ice 基于vue的UI框架。

javascript构建工具

- Babel
- WebPack：模块打包工具，主要作用就是打包，压缩，合并以及按序加载。

后端技术Nodejs：

大前端模式：

- MVVM



## JavaScript

> https://zh.javascript.info/

### 模块

- `export` 关键字标记了可以从当前模块外部访问的变量和函数。
- `import` 关键字允许从其他模块导入功能。

**模块只通过 HTTP(s) 工作，而非本地**

如果你尝试通过 `file://` 协议在本地打开一个网页，你会发现 `import/export` 指令不起作用。你可以使用本地 Web 服务器，例如 [static-server](https://www.npmjs.com/package/static-server#getting-started)，或者使用编辑器的“实时服务器”功能，例如 VS Code 的 [Live Server Extension](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) 来测试模块。

换句话说，对于模块，我们使用导入/导出而不是依赖全局变量。

如果同一个模块被导入到多个其他位置，那么它的代码只会执行一次，即在第一次被导入时。然后将其导出（export）的内容提供给进一步的导入（importer）。

这里有一条规则：顶层模块代码应该用于初始化，创建模块特定的内部数据结构。如果我们需要多次调用某些东西 —— 我们应该将其以函数的形式导出，就像我们在上面使用 `sayHi` 那样。

### 基础

#### 变量：

- let用来声明变量，以前我们是用var来声明变量的。

- var和let的区别：

    - 现代脚本中一般不再使用var。

    - “var” 没有块级作用域

        - 用 `var` 声明的变量，不是函数作用域就是全局作用域。它们在代码块外也是可见的（译注：也就是说，`var` 声明的变量只有函数作用域和全局作用域，没有块级作用域）。

    - 举个例子：

        ```javascript
        if (true) {
          var test = true; // 使用 "var" 而不是 "let"
        }
        
        alert(test); // true，变量在 if 结束后仍存在
        ```

        由于 `var` 会忽略代码块，因此我们有了一个全局变量 `test`。

        如果我们在第二行使用 `let test` 而不是 `var test`，那么该变量将仅在 `if` 内部可见：

        ```javascript
        if (true) {
          let test = true; // 使用 "let"
        }
        
        alert(test); // ReferenceError: test is not defined
        ```

        对于循环也是这样的，`var` 声明的变量没有块级作用域也没有循环局部作用域：

        ```javascript
        for (var i = 0; i < 10; i++) {
          var one = 1;
          // ...
        }
        
        alert(i);   // 10，"i" 在循环结束后仍可见，它是一个全局变量
        alert(one); // 1，"one" 在循环结束后仍可见，它是一个全局变量
        ```

        如果一个代码块位于函数内部，那么 `var` 声明的变量的作用域将为函数作用域：

        ```javascript
        function sayHi() {
          if (true) {
            var phrase = "Hello";
          }
        
          alert(phrase); // 能正常工作
        }
        
        sayHi();
        alert(phrase); // ReferenceError: phrase is not defined
        ```

        可以看到，`var` 穿透了 `if`，`for` 和其它代码块。这是因为在早期的 JavaScript 中，块没有词法环境，而 `var` 就是这个时期的代表之一。

    - **var 允许重新声明**

    - **“var” 声明的变量，可以在其声明语句前被使用**

        - 当函数开始的时候，就会处理 `var` 声明（脚本启动对应全局变量）。
        - 换言之，`var` 声明的变量会在函数开头被定义，与它在代码中定义的位置无关（这里不考虑定义在嵌套函数中的情况）。

#### 常量：`const`

## TypeScript

### tsconfig.json 简介

> 了不起的 tsconfig.json 指南 - 王平安的文章 - 知乎 https://zhuanlan.zhihu.com/p/145210784



## Jquery

jquery使用了简化dom操作的一个js库，不仅简化了你获取DOM的方式，而且还给你在里面增加了无数个直接操作DOM的方法，而且还给你增加了针对JS对象的方法和AJAX的相关的简化封装。

早期前端都是通过jQuery来实现页面数据的绑定和交互操作，因为那时候前端生态还是处于很匮乏的时代，等angular vue react 出来的时候已经百花齐放了， vue是通过Vue 对象将数据和视图分离，通过数据来驱动视图变化；

| jQuery                                                 | Vue                           |
| ------------------------------------------------------ | ----------------------------- |
| jQuery 直接可以操作DOM                                 | Vue不直接操作dom，采用虚拟dom |
| jquery通过选择器选取元素，进行取值赋值、事件绑定等操作 | Vue通过数据驱动界面           |

jQuery只是对JS API的一种封装，让你写JS代码量减少的一个工具库。

Vue则是更上层封装的一门框架，其中借鉴了MVVM的架构，使用它完全可以抛开JS中对DOM的操作。

**AJAX**

## VUE

mvvm

## npm/yarn

### npm

> https://chinese.freecodecamp.org/news/what-is-npm-a-node-package-manager-tutorial-for-beginners/

npm（“Node 包管理器”）是 JavaScript 运行时 Node.js 的默认程序包管理器。类似python的pip。

在程序开发中我们常常需要依赖别人提供的框架，写 JS 也不例外。这些可以重复的框架代码被称作包（package）或者模块（module），一个包可以是一个文件夹里放着几个文件，同时有一个叫做 package.json 的文件。

- npm 由三个独立的部分组成：
    - 网站
    - 注册表（registry）
    - 命令行工具 (CLI)
- [*网站*](https://npmjs.com/) 是开发者查找包（package）、设置参数以及管理 npm 使用体验的主要途径。
- *注册表* 是一个巨大的数据库，保存了每个包（package）的信息。
- [*CLI*](https://docs.npmjs.com/cli/npm) 通过命令行或终端运行。开发者通过 CLI 与 npm 打交道。

#### npm安装

npm 是依附于 node.js 的，我们可以去它的官网 https://nodejs.org/en/download/ 下载安装 node.js。

```
npm -v    查看npm版本
npm install npm@latest -g   升级最新的npm版本 
			<packageName>@<version> 也可以下载其他版本
```



#### package.json

每个 JavaScript 项目（无论是 Node.js 还是浏览器应用程序）都可以被当作 npm 软件包，并且通过  `package.json` 来描述项目和软件包信息。我们可以将  `package.json` 视为快递盒子上的运输信息。

 **package.json**

使用 `npm init` 即可在当前目录创建一个 `package.json` 文件：

文件内的内容(基本元数据)由开发人员提供：

- `name`：JavaScript 项目或库的名称。
- `version`：项目的版本。通常，在应用程序开发中，由于没有必要对开源库进行版本控制，因此经常忽略这一块。但是，仍可以用它来定义版本。
- `description`：项目的描述。
- `license`：项目的许可证。
- 1.`dependencies`：在生产环境中需要用到的依赖
    2.`devDependencies`：在开发、测试环境中用到的依赖
    - 开发依赖是仅用于开发的程序包，在生产环境中并不需要。 例如测试的软件包、webpack 或 Babel。
    

**package-lock.json**

该文件描述了 npm JavaScript 项目中使用的依赖项的确切版本。如果  `package.json` 是通用的描述性标签，则  `package-lock.json` 是成分表。

就像我们通常不会读取食品包装袋上的成分表（除非你太无聊或需要知道）一样，`package-lock.json` 并不会被开发人员一行一行进行读取.

#### 用户如何使用 NPM

**npm install**

这是现在我们开发 JavaScript/Node.js 应用程序时最常用的命令。

默认情况下，`npm install` 默认会安装`package.json` 中 `dependencies` 和 `devDependencies` 里的所有模块。

npm 项目上下文中的  `npm install` 将根据  `package.json` 规范将软件包下载到项目的  `node_modules` 文件夹中，从而升级软件包的版本（并重新生成  `package-lock.json` ）。

 `npm install <package-name>` 可以基于  `^` 和  `〜` 版本匹配。

如果要在全局上下文中安装程序包，可以在机器的任何地方使用它，则可以指定全局标志  `-g`（例如  [live-server](https://github.com/tapio/live-server)）。

如果想只安装 `dependencies` 中的内容，可以使用 `--production` 字段：`npm install --production`不应该将  `devDependencies` 引入生产环境！

**npm ci**

因此，如果  `npm install --production` 对于生产环境是最佳选项，那么是否必须有一个对本地环境，测试环境最合适的选项？

答案是  `npm ci`。

就像如果  `package_lock.json` 尚不存在于项目中一样，无论何时调用  `npm install` 都会生成它，`npm ci` 会消耗该文件来下载项目所依赖的每个软件包的确切版本。

这样，无论是用于本地开发的笔记本电脑还是 Github Actions 等 CI（持续集成）构建环境，我们都可以确保项目上下文在不同机器上保持完全相同。

**npm run serve 启动网站**



**[nvm](https://github.com/coreybutler/nvm-windows) 用来切换不同的nodejs版本**

例如，需要安装 v6.9.1 版本的 Node.js，那可以通过以下命令完成。

nvm换源

我们可以找到 nvm-windows 软件的安装目录中的 `settings.txt` 文件，增加以下内容:

```awk
node_mirror=http://npm.taobao.org/mirrors/node/
```

通过 nvm 可以同时安装多个版本的 Node.js，我们可以指定某个版本的使用。

```apache
nvm install v8.17.0   #安装
nvm use v8.17.0			#使用
```

查看当前安装的 Node.js 版本列表

```bash
nvm ls
```

### yarn

#### yarn 介绍

Yarn 是 Facebook, Google, Exponent 和 Tilde 开发的一款新的 JavaScript 包管理工具。 

Yarn 是为了弥补 npm 的一些缺陷而出现的。

这句话让我想起了使用npm时的坑了：
\- `npm install`的时候**巨慢**。特别是新的项目拉下来要等半天，删除node_modules，重新install的时候依旧如此。
\- 同一个项目，安装的时候**无法保持一致性**。由于package.json文件中版本号的特点，下面三个版本号在安装的时候代表不同的含义。

```shell
"5.0.3",
"~5.0.3",
"^5.0.3"
“5.0.3”表示安装指定的5.0.3版本，“～5.0.3”表示安装5.0.X中最新的版本，“^5.0.3”表示安装5.X.X中最新的版本。 
```

**Yarn的优点？**

- **速度快** 。速度快主要来自以下两个方面：

1. 并行安装：无论 npm 还是 Yarn 在执行包的安装时，都会执行一系列任务。npm 是按照队列执行每个 package，也就是说必须要等到当前 package 安装完成之后，才能继续后面的安装。而 Yarn 是同步执行所有任务，提高了性能。
2. **离线模式**：如果之前已经安装过一个软件包，用Yarn再次安装时之间从缓存中获取，就不用像npm那样再从网络下载了。

- 安装**版本统一**：为了防止拉取到不同的版本，Yarn 有一个锁定文件 (lock file) 记录了被确切安装上的模块的版本号。每次只要新增了一个模块，Yarn 就会创建（或更新）yarn.lock 这个文件。这么做就保证了，每一次拉取同一个项目依赖时，使用的都是一样的模块版本。npm 其实也有办法实现处处使用相同版本的 packages，但需要开发者执行 npm shrinkwrap 命令。这个命令将会生成一个锁定文件，在执行 npm install 的时候，该锁定文件会先被读取，和 Yarn 读取 yarn.lock 文件一个道理。npm 和 Yarn 两者的不同之处在于，Yarn 默认会生成这样的锁定文件，而 npm 要通过 shrinkwrap 命令生成 npm-shrinkwrap.json 文件，只有当这个文件存在的时候，packages 版本信息才会被记录和更新。

- **更简洁的输出**：npm 的输出信息比较冗长。在执行 npm install 的时候，命令行里会不断地打印出所有被安装上的依赖。相比之下，Yarn 简洁太多：默认情况下，结合了 emoji直观且直接地打印出必要的信息，也提供了一些命令供开发者查询额外的安装信息。

- **多注册来源处理：**所有的依赖包，不管他被不同的库间接关联引用多少次，安装这个包时，只会从一个注册来源去装，要么是 npm 要么是 bower, 防止出现混乱不一致。

- **更好的语义化**： yarn改变了一些npm命令的名称，比如 yarn add/remove，感觉上比 npm 原本的 install/uninstall 要更清晰。

- **Yarn和npm命令对比**

    ```text
    npm install === yarn 
    npm install taco --save === yarn add taco
    npm uninstall taco --save === yarn remove taco
    npm install taco --save-dev === yarn add taco --dev
    npm update --save === yarn upgrade
    npm run serve   === yarn run serve
    yarn安装全局包的命令是yarn global add 【包名】
    
    查看 yarn 全局包的可执行文件安装位置：yarn global bin
    
    查看 yarn 全局包的源文件安装位置：yarn global dir
    
    修改 yarn 全局包 bin 安装位置：yarn config set global-folder "你的磁盘路径"
    
    修改环境变量：export PATH=$PATH:【yarn 全局包 bin 安装位置】
    
    改变 yarn 缓存位置：yarn config set cache-folder "你的磁盘路径"
    ```

#### yarn安装

```shell
windows: winget install yarn 或者直接下载msi文件
跨平台： npm install --gobal yarn

```

安装后检查：`yarn --version`

```shell
安装yrm：yrm 不仅可以快速切换镜像源，还可以测试自己网络访问不同源的速度
npm install -g yrm
yrm ls 列出当前可用的所有镜像源
    (base) PS C:\Users\lambo> yrm ls
      npm ---- https://registry.npmjs.org/
      cnpm --- http://r.cnpmjs.org/
    * taobao - https://registry.npm.taobao.org/
      nj ----- https://registry.nodejitsu.com/
      rednpm - http://registry.mirror.cqupt.edu.cn/
      npmMirror  https://skimdb.npmjs.com/registry/
      edunpm - http://registry.enpmjs.org/
      yarn --- https://registry.yarnpkg.com
使用淘宝镜像源：yrm use taobao 
测试访问速度：yrm test taobao
```

#### 创建项目

```shell
yarn init   初始化一个项目，类似于git init,填写下项目详情就行。

就会在对应目录生成一个package.json文件
```

添加依赖包：

```shell
yarn add [package]
yarn add [package]@[version]
yarn add [package]@[tag]
```

**将依赖项添加到不同依赖项类别中**:

```shell
分别添加到 devDependencies、peerDependencies 和 optionalDependencies 类别中：

yarn add [package] --dev
yarn add [package] --peer
yarn add [package] --optional

升级依赖包:
yarn upgrade [package]
yarn upgrade [package]@[version]
yarn upgrade [package]@[tag]
```

**移除依赖包**：`yarn remove [package]：`

**安装项目的全部依赖**：`yarn或者 yarn install`

## Electron

> Electron是一个使用 JavaScript、HTML 和 CSS 构建桌面应用程序的框架。 嵌入 [Chromium](https://www.chromium.org/) 和 [Node.js](https://nodejs.org/) 到 二进制的 Electron 允许您保持一个 JavaScript 代码代码库并创建 在Windows上运行的跨平台应用 macOS和Linux——不需要本地开发 经验。
>
> 参考：https://www.electronjs.org/zh/docs/latest/tutorial/
>
> ​			Eletron实战



### 一个简单的Electron项目

安装nodejs，安装npm。

**注意** 因为 Electron 将 Node.js 嵌入到其二进制文件中，你应用运行时的 Node.js 版本与你系统中运行的 Node.js 版本无关。

创建Electron项目：

```
yarn init
默认会生成一个package.json，记得入口点改为main.js,默认是index.js
author 与 description 可为任意值，但对于应用打包是必填项。

package.json

{
  "name": "test",
  "version": "1.0.0",
  "description": "test",
  "main": "main.js",
  "author": "lambo",
  "license": "MIT"
}
```

然后，将 `electron` 包安装到应用的开发依赖中。

`yarn add --dev electron`

在您的 [`package.json`](https://docs.npmjs.com/cli/v7/using-npm/scripts)配置文件中的`scripts`字段下增加一条`start`命令：

```
{
  "scripts": {
    "start": "electron ."
  }
}
```



`start`命令能让您在开发模式下打开您的应用`yarn start`

运行主进程

任何 Electron 应用程序的入口都是 `main` 文件。 这个文件控制了**主进程**，它运行在一个完整的Node.js环境中，负责控制您应用的生命周期，显示原生界面，执行特殊操作并管理渲染器进程。

创建页面

写个普通的html页面。 在Electron中，各个窗口显示的内容可以是本地HTML文件，也可以是一个远程url。

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <!-- https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP -->
    <meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src 'self'">
    <title>Hello World!</title>
  </head>
  <body>
    <h1>Hello World!</h1>
    We are using Node.js <span id="node-version"></span>,
    Chromium <span id="chrome-version"></span>,
    and Electron <span id="electron-version"></span>.
  </body>
</html>
```

在窗口中打开我们写的页面

现在您有了一个页面，将它加载进应用窗口中。 要做到这一点，你需要 两个Electron模块：

- [`app`](https://www.electronjs.org/zh/docs/latest/api/app) 模块，它控制应用程序的事件生命周期。
- [`BrowserWindow`](https://www.electronjs.org/zh/docs/latest/api/browser-window) 模块，它创建和管理应用程序 窗口。

因为主进程运行着 Node.js，您可以在 main.js 文件头部将它们导入作为 [CommonJS](https://nodejs.org/docs/latest/api/modules.html#modules_modules_commonjs_modules) 模块：

```javascript
const { app, BrowserWindow } = require('electron')
```

然后，添加一个`createWindow()`方法来将`index.html`加载进一个新的`BrowserWindow`实例。

```javascript
const createWindow = () => {
  const win = new BrowserWindow({
    width: 800,
    height: 600
  })

  win.loadFile('index.html')
}
```

接着，调用`createWindow()`函数来打开您的窗口。

在 Electron 中，只有在 `app` 模块的 [`ready`](https://www.electronjs.org/zh/docs/latest/api/app#event-ready) 事件被激发后才能创建浏览器窗口。 您可以通过使用 [`app.whenReady()`](https://www.electronjs.org/zh/docs/latest/api/app#appwhenready) API来监听此事件。 在`whenReady()`成功后调用`createWindow()`。

```javascript
app.whenReady().then(() => {
  createWindow()
})
```

注意：此时，您的电子应用程序应当成功 打开显示您页面的窗口！





`yarn run make`生成程序

Electron-forge 会创建 `out` 文件夹，您的软件包将在那里找到：

```
// Example for macOS
out/
├── out/make/zip/darwin/x64/my-electron-app-darwin-x64-1.0.0.zip
├── ...
└── out/my-electron-app-darwin-x64/my-electron-app.app/Contents/MacOS/my-electron-app
```

### 引入vue

创建Vue项目之前我们需要先安装Vue CLI。它是一个命令行工具，可以辅助开发人员创建Vue项目，安装指令如下：

```shell
yarn global add @vue/cli
```

创建一个vue项目

```
 vue create demo
```



Vue CLI Plugin Electron Builder是能将Vue引入Electron项目的工具。安装Vue插件electron-builder（也就是Vue CLI Plugin Electron Builder）

```
vue add electron-builder
```

安装完成后通过如下指令启动程序：

```
yarn electron:serve
```

![image-20220808191525638](javascript.assets/image-20220808191525638.png)

项目目录结构如下所示：

```
project/
├─ src/
├ ├ background.js
├ └─ main.js
├ dist_electron
└─ public/
```

dist_electron目录存放应用打包后的安装程序。
public目录存放项目的静态资源，此目录下的程序不会被webpack处理。
src/background.js是主进程入口程序。
src/main.js是渲染进程入口程序。

## vscode插件开发

> https://liiked.github.io/VS-Code-Extension-Doc-ZH/#/
>
> https://www.cnblogs.com/liuxianan/p/vscode-plugin-overview.html
>
> https://code.visualstudio.com/api 官方文档

vscode是使用Electron技术。vscode插件可以使用TypeScript来编写（官方推荐），也可以使用JavaScript。

### 安装脚手架

```cmd
npm install -g yo generator-code
#然后创建一个项目
yo code
```

### package

- 配置扩展激活卸载的事件，入口文件路径。
- 添加右键菜单和快捷键

```json
//常用设置
{
	// 插件的名字，应全部小写，不能有空格
    "name": "vscode-plugin-demo",
	// 插件的友好显示名称，用于显示在应用市场，支持中文
    "displayName": "VSCode插件demo",
	// 描述
    "description": "VSCode插件demo集锦",
	// 关键字，用于应用市场搜索
    "keywords": ["vscode", "plugin", "demo"],
	// 版本号
    "version": "1.0.0",
	// 发布者，如果要发布到应用市场的话，这个名字必须与发布者一致
    "publisher": "sxei",
	// 表示插件最低支持的vscode版本
    "engines": {
        "vscode": "^1.27.0"
    },
	// 插件应用市场分类，可选值： [Programming Languages, Snippets, Linters, Themes, Debuggers, Formatters, Keymaps, SCM Providers, Other, Extension Packs, Language Packs]
    "categories": [
        "Other"
    ],
	// 插件图标，至少128x128像素
    "icon": "images/icon.png",
	// 扩展的激活事件数组，可以被哪些事件激活扩展，后文有详细介绍
    "activationEvents": [
        "onCommand:extension.sayHello"
    ],
	// 插件的主入口
    "main": "./src/extension",
	// 贡献点，整个插件最重要最多的配置项
    "contributes": {
		// 插件配置项
		"configuration": {
            "type": "object",
			// 配置项标题，会显示在vscode的设置页
            "title": "vscode-plugin-demo",
            "properties": {
				// 这里我随便写了2个设置，配置你的昵称
                "vscodePluginDemo.yourName": {
                    "type": "string",
                    "default": "guest",
                    "description": "你的名字"
                },
				// 是否在启动时显示提示
                "vscodePluginDemo.showTip": {
                    "type": "boolean",
                    "default": true,
                    "description": "是否在每次启动时显示欢迎提示！"
                }
            }
        },
		// 命令
        "commands": [
            {
                "command": "extension.sayHello",
                "title": "Hello World"
            }
        ],
		// 快捷键绑定
        "keybindings": [
            {
                "command": "extension.sayHello",
                "key": "ctrl+f10",
                "mac": "cmd+f10",
                "when": "editorTextFocus"
            }
        ],
		// 菜单
        "menus": {
			// 编辑器右键菜单
            "editor/context": [
                {
					// 表示只有编辑器具有焦点时才会在菜单中出现
                    "when": "editorFocus",
                    "command": "extension.sayHello",
					// navigation是一个永远置顶的分组，后面的@6是人工进行组内排序
                    "group": "navigation@6"
                },
                {
                    "when": "editorFocus",
                    "command": "extension.demo.getCurrentFilePath",
                    "group": "navigation@5"
                },
                {
					// 只有编辑器具有焦点，并且打开的是JS文件才会出现
                    "when": "editorFocus && resourceLangId == javascript",
                    "command": "extension.demo.testMenuShow",
                    "group": "z_commands"
                },
                {
                    "command": "extension.demo.openWebview",
                    "group": "navigation"
                }
            ],
			// 编辑器右上角图标，不配置图片就显示文字
            "editor/title": [
                {
                    "when": "editorFocus && resourceLangId == javascript",
                    "command": "extension.demo.testMenuShow",
                    "group": "navigation"
                }
            ],
			// 编辑器标题右键菜单
            "editor/title/context": [
                {
                    "when": "resourceLangId == javascript",
                    "command": "extension.demo.testMenuShow",
                    "group": "navigation"
                }
            ],
			// 资源管理器右键菜单
            "explorer/context": [
                {
                    "command": "extension.demo.getCurrentFilePath",
                    "group": "navigation"
                },
                {
                    "command": "extension.demo.openWebview",
                    "group": "navigation"
                }
            ]
        },
		// 代码片段
        "snippets": [
            {
                "language": "javascript",
                "path": "./snippets/javascript.json"
            },
            {
                "language": "html",
                "path": "./snippets/html.json"
            }
        ],
		// 自定义新的activitybar图标，也就是左侧侧边栏大的图标
        "viewsContainers": {
            "activitybar": [
                {
                    "id": "beautifulGirl",
                    "title": "美女",
                    "icon": "images/beautifulGirl.svg"
                }
            ]
        },
		// 自定义侧边栏内view的实现
        "views": {
			// 和 viewsContainers 的id对应
            "beautifulGirl": [
                {
                    "id": "beautifulGirl1",
                    "name": "国内美女"
                },
                {
                    "id": "beautifulGirl2",
                    "name": "国外美女"
                },
                {
                    "id": "beautifulGirl3",
                    "name": "人妖"
                }
            ]
        },
		// 图标主题
        "iconThemes": [
            {
                "id": "testIconTheme",
                "label": "测试图标主题",
                "path": "./theme/icon-theme.json"
            }
        ]
    },
	// 同 npm scripts
    "scripts": {
        "postinstall": "node ./node_modules/vscode/bin/install",
        "test": "node ./node_modules/vscode/bin/test"
    },
	// 开发依赖
    "devDependencies": {
        "typescript": "^2.6.1",
        "vscode": "^1.1.6",
        "eslint": "^4.11.0",
        "@types/node": "^7.0.43",
        "@types/mocha": "^2.2.42"
    },
	// 后面这几个应该不用介绍了
    "license": "SEE LICENSE IN LICENSE.txt",
    "bugs": {
        "url": "https://github.com/sxei/vscode-plugin-demo/issues"
    },
    "repository": {
        "type": "git",
        "url": "https://github.com/sxei/vscode-plugin-demo"
    },
	// 主页
    "homepage": "https://github.com/sxei/vscode-plugin-demo/blob/master/README.md"
}

```

### contributes

```
configuration：设置
commands：命令
menus：菜单
keybindings：快捷键绑定
languages：新语言支持
debuggers：调试
breakpoints：断点
grammars
themes：主题
snippets：代码片段
jsonValidation：自定义JSON校验
views：左侧侧边栏视图
viewsContainers：自定义activitybar
problemMatchers
problemPatterns
taskDefinitions
colors
```

### 语言插件

> https://zhuanlan.zhihu.com/p/388801724

#### 新语言支持：languages

```json
"contributes": {  
    "languages": [
                {
                    //新语言的名字
                    "id": "cna",
                    //新语言的别名
                    "aliases": [
                        "Aggressor Script",
                        "cna"
                    ],
                    //新语言的后缀名
                    "extensions": [
                        ".cna"
                    ],
                    //新语言的配置文件路径
                    "configuration": "./language-configuration.json"
                }
            ],
}
```

**注意**：如果你的语言配置文件以**`language-configuration.json`**结尾，那么VS Code会帮你添加代码补全和校验功能。

**以下均为language-configuration.json文件设置**

##### 启用/关闭注释

- VS Code提供了切换注释开关的命令：

    - **Toggle Line Comment**
    - **Toggle Block Comment**

- 分别来配置`comments.lineComment`控制块注释和`comments.blockComment`控制行注释。

    - ```
        {
        	"comments": {
        		"lineComment": "//",
        		"blockComment": ["/*", "*/"]
        	}
        }
        ```

##### 定义括号

- 你在VS Code中将鼠标移动到一个括号边上时，VS Code会自动高亮对应的括号。

- ```json
    {
    	"brackets": [["{", "}"], ["[", "]"], ["(", ")"]]
    }
    ```

- 另外，当你运行**Go to Bracket**或**Select to Bracket**时，VS Code会自动使用你的定义找到最近、最匹配的括号。

##### 自动闭合符号

- 当你输入一个字符的时候时，VS Code会自动帮你补全另一个单引号然后将光标放在引号中间，我们来看看是怎么做的：

- ```json
    {
    	"autoClosingPairs": [
    		{ "open": "{", "close": "}" },
    		{ "open": "[", "close": "]" },
    		{ "open": "(", "close": ")" },
    		{ "open": "'", "close": "'", "notIn": ["string", "comment"] },
    		{ "open": "\"", "close": "\"", "notIn": ["string"] },
    		{ "open": "`", "close": "`", "notIn": ["string", "comment"] },
    		{ "open": "/**", "close": " */", "notIn": ["string"] }
    	]
    }
    ```

##### 自动环绕符号

- 当你选择了一堆文本然后输入左括号时，VS Code会对选中内容外围加上对应的括号。这个功能叫做*自动环绕符号*，你可以参考下面的代码指定这项功能：

- ```
    {
    	"surroundingPairs": [
    		["{", "}"],
    		["[", "]"],
    		["(", ")"],
    		["'", "'"],
    		["\"", "\""],
    		["`", "`"]
    	]
    }
    ```

- 注意用户可以通过`editor.autoSurround`设置*自动环绕符号*的行为。

##### 代码折叠

- 在VS Code中有三种代码折叠类型：
- **缩进折叠：这是VS Code中默认的缩进行为，当两行内容有着相同的缩进级别时，你就可以看到折叠标记了。**
- 语言配置折叠：当VS Code发现`folding.markers`同时定义了`start`和`end`时，对应区域内就会出现折叠标记。下述配置会对`//#region`和`//#endregionJSON`区域创建代码折叠标记：

##### 单词匹配

##### 缩进规则

#### 代码片段：snippets

代码片段，也叫`snippets`，相信大家都不陌生，就是输入一个很简单的单词然后一回车带出来很多代码。平时大家也可以直接在vscode中创建属于自己的`snippets`：

```json
"contributes": {
    "snippets": [
		{
			// 代码片段作用于那种语言
			"language": "javascript",
			// 片段文件路径
			"path": "./snippets/test.json"
    	}
	]
}
```

比如创建一个for循环，输入for就快速生成代码片段

```json
//./snippets/test.json

{
    "for循环": {
        "prefix": "for",
        "body": [
          "for (const ${2:item} of ${1:array}) {",
          "\t$0",
          "}"
        ],
        "description": "for循环"
    }
}


for循环：snippets的名字；
prefix：输入什么单词触发代码片段；
body：一个数组，存放代码片段的内容，每一行一个字符串；
description：片段的描述；
${1:xxx}占位符，数字表示光标聚焦的顺序，1表示默认光标落在这里，按下回车或者tab跳到2的位置，以此类推，xxx表示此位置的默认值，可省略，比如直接写$3；$0最后一个聚焦的位置

```

```
//为加深印象我们再来一个ajax的例子：
{
	"ajax": {
        "prefix": "ajax",
        "body": [
            "$.ajax({",
            "    url: '$1',",
            "    method: '${2:POST}',",
            "    datatype: 'json',",
            "    success: data => {",
            "        $3;",
            "    },",
            "    error: err => {",
            "        $4;",
            "    }",
            "})"
        ],
        "description": "ajax模块"
    }
}

```



#### 语法高亮：grammars

语法高亮决定源代码的颜色和样式，它主要负责关键字（如javascript中的`if`，`for`）、字符串、注释、变量名等等语法的着色工作。

语法高亮由两部分工作组成：

- [分词](#分词)：将文本分割为一系列符号（包括单词和标点）
- [主题化](#主题化)：然后根据主题或用户设置，对符号进行着色添加样式

### Language Server Protocol

> 

#### lsp介绍

- LSP —— Language Server Protocol 本质上是一种基于 JSON-RPC 的进程间通讯协议，LSP 本身包含两大块内容：

    - 定义 client 与 server 之间的通讯模型，也就是谁、在什么时候、以什么方式向对方发送什么格式的信息，接收方又以什么方式返回响应信息

    - 定义通讯信息体，也就是以什么格式、什么字段、什么样的值表达信息状态

- 作为类比，HTTP 协议专门用于描述网络节点间如何传输、理解超媒体文档的网络通讯协议；而 LSP 协议则专门用于描述 IDE 中，用户行为与响应之间的通讯方式与信息结构。

- 总结一下，LSP 架构的工作流程如下：

    - 编辑器如 VSCode 跟踪、计算、管理用户行为模型，在发生某些特定的行为序列时，以 LSP 协议规定的通讯方式向 Language Server 发送动作与上下文参数

    - Language Server 根据这些参数异步地返回响应信息

    - 编辑器再根据响应信息处理交互反馈

- ![image-20221005100717643](./javascript.assets/image-20221005100717643.png)

- 简单说，编辑器负责与用户直接交互， Language Server 负责在背后默默计算如何响应用户的交互动作，两者以进程粒度分离、解耦，在 LSP 协议框架下各司其职又协作共生。就好像我们通常开发的 Web 应用中，前端负责与用户交互，服务端负责管理诸如权限、业务数据、业务状态流转等不可见的部分。
- 目前，LSP 协议已经发展到 3.16 版本，覆盖大多数语言特性，包括：
    - 代码补全
    - 代码高亮
    - 定义跳转
    - 类型推断
    - 错误检测
    - 等等
- **得益于 LSP 清晰的设计，这些语言特性的开发套路都很相似，学习曲线很平滑，开发的时候基本上只需要关心监听那个函数，返回什么格式的结构，可以说掌握几个示例之后就可以很简单地上手了。**
- 过去，IDE 对语言特性的支持是集成在 IDE 或者以同构插件形式实现的，在 VSCode 中这种同构扩展能力以 **「Language API」** 或 **「Sematic Tokens Provider」** 接口方式提供，这两种方式在上一篇文章《[你不知道的 VSCode 代码高亮原理](https://link.zhihu.com/?target=https%3A//mp.weixin.qq.com/s/4TNh0sTyT49Flxs2sdr0uw)》都有过介绍了，虽然架构上比较简单，容易理解，但有一些明显硬伤：
    - 插件开发者必须复用 VSCode 本身的开发语言、环境，例如 Python 语言插件就必须用 JavaScript 写
    - 同一个编程语言需要为不同 IDE 重复开发相似的扩展插件，重复投入
- ![image-20221005100824245](./javascript.assets/image-20221005100824245.png)

- LSP 最大的优势就是将 IDE 客户端与实际计算交互特性的服务端隔离开来，同一个 Language Service 可以重复应用在多个不同 Language Client 中。
- 此外，LSP 协议下客户端、服务器分别在各自进程运行，在性能上也会有正向收益：
    - 确保 UI 进程不卡顿
    - Node 环境下，充分利用多核 CPU 能力
    - 由于不再限定 Language Server 的技术栈，开发者可以选择更高性能的语言，例如 Go

- 总的来说，就是很强。

如果你选择使用 TS 编写 LSP，事情会变得更简单。`vscode-languageserver` 包提供了非常完善的 Typescript 类型定义，我们完全可以借助 ts + VSCode 的代码提示找到需要使用的监听函数，和函数的类型，返回值，说明。

LSP 稍微有一点点复杂，建议读者先拉下 vscode 官方示例对比学习：

```
git clone https://github.com/microsoft/vscode-extension-samples.git
cd vscode-extension-samples/lsp-sample
yarn
yarn compile
code .
```

vscode-extension-samples/lsp-sample 的主要代码文件有：

```
.
├── client // Language Client
│   ├── src
│   │   └── extension.ts // Language Client 入口文件
├── package.json 
└── server // Language Server
    └── src
        └── server.ts // Language Server 入口文件
```

样例代码中有几个关键点：

1. `server/src/server.ts`：LSP 服务端代码，提供代码补全、错误诊断、代码提示等常见语言功能的示例
2. `client/src/extension.ts`：提供一系列 LSP 参数，包括 Server 的调试端口、代码入口、通讯方式等。

3. `packages.json`：主要提供了语法插件所需要的配置信息，包括：

4. - `activationEvents`： 声明插件的激活条件，代码中的 `onLanguage:plaintext` 意为打开 txt 文本文件时激活
    - `main`： 插件的入口文件

逻辑上，vscode 会在加载插件时根据 `package.json` 的配置判断激活条件，之后加载、运行插件入口，启动 LSP 服务器。插件启动后，后续用户在 vscode 的交互行为会以标准事件，如 hover、completion、signature help 等方式触发插件的 client ，client 再按照 LSP 协议转发到 server 层。

其中，`client/src/extension.ts` 与 `packages.json` 都比较简单，本文过多介绍，重点在于 `server/src/server.ts` 文件，接下来我们逐步拆解，解析不同语言特性的实现细节。

#### 入口配置

示例 vscode-extension-samples/lsp-sample 中的 `package.json` 有两个关键配置：

```json
{
    "activationEvents": [
        "onLanguage:plaintext"
    ],
    "main": "./client/out/extension",
}
```

其中：

- `activationEvents`：声明插件的激活条件，代码中的 `onLanguage:plaintext` 意为打开 txt 文本文件时激活
- `main`：插件的入口文件

#### Client 样例

- 示例 vscode-extension-samples/lsp-sample 中的 Client 入口代码，关键部分如下：

    ```typescript
    export function activate(context: ExtensionContext) {
        // Server 配置信息
        const serverOptions: ServerOptions = {
            run: { 
                // Server 模块的入口文件
                module: context.asAbsolutePath(
                    path.join('server', 'out', 'server.js')
                ), 
                // 通讯协议，支持 stdio、ipc、pipe、socket
                transport: TransportKind.ipc 
            },
        };
    
        // Client 配置
        const clientOptions: LanguageClientOptions = {
            // 与 packages.json 文件的 activationEvents 类似
            // 插件的激活条件
            documentSelector: [{ scheme: 'file', language: 'plaintext' }],
            // ...
        };
    
        // 使用 Server、Client 配置创建代理对象
        const client = new LanguageClient(
            'languageServerExample',
            'Language Server Example',
            serverOptions,
            clientOptions
        );
    
        client.start();
    }
    ```

    代码脉络很清晰，先是定义 Server、Client 配置对象，之后创建并启动了 `LanguageClient` 实例。从实例可以看到，Client 这一层可以做的很薄，在 Node 环境下大部分转发逻辑都被封装在 `LanguageClient` 类中，开发者无需关心细节。



#### Server 样例

示例 vscode-extension-samples/lsp-sample 中的 Server 代码实现了错误诊断、代码补全功能，作为学习样例来说稍显复杂。

LSP 客户端服务器之间的通讯过程都已经封装在 `LanguageClient` 、`connection` 等对象中，插件开发者并不需要关心底层实现细节，也不需要深入理解 LSP 协议即可基于这些对象暴露的接口、事件等实现简单的代码高亮效果。

```typescript
// 要素1： 初始化 LSP 连接对象
const connection = createConnection(ProposedFeatures.all);

// 要素2： 创建文档集合对象，用于映射到客户端正在编辑的文件
const documents: TextDocuments<TextDocument> = new TextDocuments(TextDocument);

connection.onInitialize((params: InitializeParams) => {
  // 要素3： 显式声明插件支持的语言特性
  const result: InitializeResult = {
    capabilities: {
      hoverProvider: true
    },
  };
  return result;
});

// 要素4： 将文档集合对象关联到连接对象
documents.listen(connection);

// 要素5： 开始监听连接对象
connection.listen();
```

从示例代码可以总结出 Language Server 的 5 个必要步骤：

- 创建 `connection` 对象，用于实现客户端与服务器之间的信息互通
- 创建 `documents` 文档集合对象，用于映射客户端正在编辑的文件
- 在 `connection.onInitialize` 事件中，显式声明插件支持的语法特性，例如上例中返回对象包含 `hoverProvider: true` 声明，表示该插件能够提供代码悬停提示功能
- 将 `documents` 关联到 `connection` 对象
- 调用 `connection.listen` 函数，开始监听客户端消息

上述 `connection` 、`documents` 等对象定义在 npm 包：

- `vscode-languageserver/node`
- `vscode-languageserver-textdocument`

这是一个基本模板，主要完成了 Language Server 各种初始化操作，后续就可以使用 `connection.onXXX` 或 `documents.onXXX` 监听各类交互事件，并在事件回调中返回符合 LSP 协议的结果，或者显式调用通讯函数如 `connection.sendDiagnostics` 发送交互信息。

接下来我们通过几个简单实例，分析各项语言特性的实现逻辑。

##### 悬停提示

- 当鼠标停留在语言元素如函数、变量、符号等 token 时，VSCode 会显示 token 对应描述与帮助信息：

- 要实现悬停提示功能，首先需要声明插件支持 `hoverProvider` 特性：

    ```text
    connection.onInitialize((params: InitializeParams) => {
      return {
        capabilities: {
          hoverProvider: true
        },
      };
    });
    ```

    之后，需要监听 `connection.onHover` 事件，并在事件回调中返回提示信息：

    ```text
    connection.onHover((params: HoverParams): Promise<Hover> => {
      return Promise.resolve({
        contents: ["Hover Demo"],
      });
    });
    ```

    OK，这就是一个很简单的语言特性示例了，本质上就是监听事件 + 返回结果，非常简单。

##### 代码格式化

- 代码格式化是一个特别有用的功能，能够帮助用户快速、自动完成代码的美化处理，实现效果如：

- 实现代码格式化功能，首先需要声明插件支持 `documentFormattingProvider` 特性：

    ```text
    {
        ...
        capabilities : {
            documentFormattingProvider: true
            ...
        }
    }
    ```

    之后，监听 `onDocumentFormatting` 事件：

    ```text
    connection.onDocumentFormatting(
      (params: DocumentFormattingParams): Promise<TextEdit[]> => {
        const { textDocument } = params;
        const doc = documents.get(textDocument.uri)!;
        const text = doc.getText();
        const pattern = /\b[A-Z]{3,}\b/g;
        let match;
        const res = [];
        // 查找连续大写字符串
        while ((match = pattern.exec(text))) {
          res.push({
            range: {
              start: doc.positionAt(match.index),
              end: doc.positionAt(match.index + match[0].length),
            },
            // 将大写字符串替换为 驼峰风格
            newText: match[0].replace(/(?<=[A-Z])[A-Z]+/, (r) => r.toLowerCase()),
          });
        }
    
        return Promise.resolve(res);
      }
    );
    ```

    示例代码中，回调函数主要实现将连续大写字符串格式化为驼峰字符串，

##### 函数签名

- 函数签名特性在用户输入函数调用语法时触发，此时 VSCode 会根据 Language Server 返回的内容，显示该函数的帮助信息。

- 实现函数签名功能，需要首先声明插件支持 `documentFormattingProvider` 特性：

    ```text
    {
        ...
        capabilities : {
            signatureHelpProvider: {
                triggerCharacters: ["("],
            }
            ...
        }
    }
    ```

    之后，监听 `onSignatureHelp` 事件：

    ```text
    connection.onSignatureHelp(
      (params: SignatureHelpParams): Promise<SignatureHelp> => {
        return Promise.resolve({
          signatures: [
            {
              label: "Signature Demo",
              documentation: "帮助文档",
              parameters: [
                {
                  label: "@p1 first param",
                  documentation: "参数说明",
                },
              ],
            },
          ],
          activeSignature: 0,
          activeParameter: 0,
        });
      }
    );
    ```

##### 错误提示

注意，错误提示的实现逻辑与上述事件 + 响应的模式有一点点不同：

- 首先不需要通过`capabilities` 做额外声明；
- 监听的是 `documents.onDidChangeContent` 事件，而不是 `connection` 对象上的事件
- 不是在事件回调中用 `return` 语句返回错误信息，而是调用 `connection.sendDiagnostics` 发送错误消息

```text
// 增量错误诊断
documents.onDidChangeContent((change) => {
  const textDocument = change.document;

  // The validator creates diagnostics for all uppercase words length 2 and more
  const text = textDocument.getText();
  const pattern = /\b[A-Z]{2,}\b/g;
  let m: RegExpExecArray | null;

  let problems = 0;
  const diagnostics: Diagnostic[] = [];
  while ((m = pattern.exec(text))) {
    problems++;
    const diagnostic: Diagnostic = {
      severity: DiagnosticSeverity.Warning,
      range: {
        start: textDocument.positionAt(m.index),
        end: textDocument.positionAt(m.index + m[0].length),
      },
      message: `${m[0]} is all uppercase.`,
      source: "Diagnostics Demo",
    };
    diagnostics.push(diagnostic);
  }

  // Send the computed diagnostics to VSCode.
  connection.sendDiagnostics({ uri: textDocument.uri, diagnostics });
});
```

这段逻辑诊断代码中是否存在连续大写字符串，通过 `sendDiagnostics` 发送相应的错误信息

### 打包发布

插件开发完了，如何发布出去分享给他人呢？主要有3种方法：

- 方法一：直接把文件夹发给别人，让别人找到vscode的插件存放目录并放进去，然后重启vscode，一般不推荐；
- 方法二：打包成vsix插件，然后发送给别人安装，如果你的插件涉及机密不方便发布到应用市场，可以尝试采用这种方式；
- 方法三：注册开发者账号，发布到官网应用市场，这个发布和npm一样是不需要审核的。

无论是本地打包还是发布到应用市场都需要借助`vsce`这个工具。

```cmd
npm i vsce -g
```

**本地打包：**

- 打包成`vsix`文件：

    - ```
        vsce package
        ```

- 打包的时候如果没有设置`repository`会有提示，所以最好设置一下。
- 生成好的vsix文件不能直接拖入安装，只能从扩展的右上角选择`Install from VSIX`安装：

**发布应用市场:**

- 







