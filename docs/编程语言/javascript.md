## 前端技术发展

html

css

css预处理器：

- less
- sass

javascript:

- 原生js开发：按照ECMAScript标准的开发方式，简称ES

Typescript 微软的标准：

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

## npm

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

### npm安装

npm 是依附于 node.js 的，我们可以去它的官网 https://nodejs.org/en/download/ 下载安装 node.js。

```
npm -v    查看npm版本
npm install npm@latest -g   升级最新的npm版本 
			<packageName>@<version> 也可以下载其他版本
```



### package.json

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

### 用户如何使用 NPM

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

## yarn

### yarn 介绍

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
    ```

### yarn安装

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

### 创建项目

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

# Electron

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









