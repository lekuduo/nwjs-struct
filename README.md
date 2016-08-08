### nwjs系列一基本结构和配置

* 环境安装
 * windows下的安装
 * linux环境下的安装
* 初始化工程
* 基本结构和配置

#### 1.1 环境安装

node-webkit是开源项目，项目地址为[node-webkit](https://github.com/rogerwang/node-webkit)

下载地址：https://github.com/nwjs/nw.js#downloads

#### 1.1.1 windows下的安装

下载windows版本的安装包，解压到磁盘，工程示例：

![Alt-text](http://res.imtt.qq.com/tbs_inspect/image/tbs-project.png)

执行./nw.exe package.nw

#### 1.1.2 linux环境下的安装

以ubuntu为例，首先下载安装包。

```
wget http://dl.node-webkit.org/v0.8.5/node-webkit-v0.8.5-linux-ia32.tar.gz
```

解压：

```
tar -xzf node-webkit-v0.8.5-linux-ia32.tar.gz
```

运行nw，看是否正常，出现错误：

```
./nw: error while loading shared libraries: libudev.so.0: cannot open shared object file:
```

解决方式：

* 下载安装ghex：sudo apt-get install ghex

* 在nw可执行文件目录中用ghex打开nw：ghex nw

* 在ghex中，ctrl+f,打开搜索工具，查找libudev.so.0

* 关闭搜索框，在右侧字符窗口，修改0为1。

* ctrl+s保存后退出ghex，现在再打开nw就会看到一个小窗口了，这就成功了

![Alt-text](http://res.imtt.qq.com/tbs_inspect/image/linux-node-webkit.png)

#### 初始化工程

以打印本地日志项目工程

工程目录结构：

project
|--app
   |--build
   |--css
   |--html
   |--images
   |--js
   |--jsx
   |--package.json
|--tool

在html目录下创建入口页index.html，内容如下：

```
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="utf-8">
    <link rel="stylesheet" type="text/css" href="../css/index.css">
    <link rel="shortcut icon" href="../images/x5_icon.ico">
</head>
<body onload="process.mainModule.exports.callback0()">
<div id="container">日志打印</div>
<div id="platform"></div>
<script src="../js/index.js"></script>
</body>
</html>
```

创建package.json文件：

```
{
  "main": "html/index.html",
  "name": "tbs_debug",
  "version": "0.1.0",
  "node-main": "js/front-filter.js",
  "window": {
    "title": "TBS调试工具 v0.1.0",
    "icon": "images/x5_icon.png",
    "width": 799,
    "height": 799
  },
  "chromium-args": "-ignore-certificate-errors"
}
```

可以使用gulp打包app目录生成一个.nw文件。

```
./nw ./package.nw
```

![Alt-text](http://res.imtt.qq.com///tbs_inspect/image/tbs_debug1.png)

#### 基本结构和package.json配置

* 2.1 基本程序结构
* 2.2 package.json
 * 2.2.1 必须的配置
 * 2.2.2 特性控制字段
* 2.3 小结

#### 2.1 基本程序结构

![Alt text](http://res.imtt.qq.com///tbs_inspect/image/tbs-project-app.png)

如上图，是一个nw程序的基本组织结构

在根目录下package.json程序的配置文件；

index.html（可以是任意名称），应用的启动页面；

js/css/resources，应用的样式、脚本、html、图片等资源文件；

node_modules存放node.js的扩展组件。

nw在启动应用程序时，首先要读取package.json文件，初始化基本属性，详解package.json参数。

#### 2.2 package.json

一个完整的package.json实例如下：

```
{
	"main": "index.html",
	"name": "nw-demo",
	"description": "demo app of node-webkit",
	"version": "0.1.0",
	"keywords": [ "demo", "node-webkit" ],
	"window": {
		"title": "node-webkit demo",
		"icon": "link.png",
		"toolbar": true,
		"frame": false,
		"width": 800,
		"height": 500,
		"position": "mouse",
		"min_width": 400,
		"min_height": 200,
		"max_width": 800,
		"max_height": 600
	},
	"webkit": {
		"plugin": true
	}
}
```

#### 2.2.1 必须的配置

在上面的配置中main和name是必须的属性。

main：指定程序的起始页面。

name：字符串，必须是小写字母或者数字，可以包含"."或者"_"或者"-"，不允许带空格。且全局唯一

#### 2.2.2 特性控制字段

nodejs：布尔值，如果设置为false，禁用webkit的node支持。

node-main：字符串，指定一个node.js文件，当程序启动时，该文件会被运行，启动时间要早于node-webkit加载html的时间。它在node上下文中运行，可以用它来实现类似后台线程的功能。

在node-main脚本中还可以访问全局的"window"对象，它指向DOM窗口，但是如果页面导航发生变化，访问到的window对象也会发生变化。因为它执行时间要早于DOM加载，所以要页面加载完毕，才能使用"window"对象。

同时，在DOM页面中，可以通过process.mainModule来获取node-main信息。

创建js/front-filter.js，内容如下：

```
var i = 0;
exports.callback0 = function () {
    console.log(i + ": " + window.location);
    window.alert ("i = " + i);
    i = i + 1;
};
```

修改html/index.html，如下：

```
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="utf-8">
    <link rel="stylesheet" type="text/css" href="../css/index.css">
    <link rel="shortcut icon" href="../images/x5_icon.ico">
</head>
<body onload="process.mainModule.exports.callback0()">
<div id="container">日志打印</div>
<div id="platform"></div>
<script src="../js/index.js"></script>
</body>
</html>
```

重新构建打包，运行

![Alt-text](http://res.imtt.qq.com///tbs_inspect/image/node-main.png)

single-instance：布尔值，默认情况下，如果将nw程序打包发布，那么只运行同时启动一个该应用的实例，如果你希望允许同时启动多个实例，将该值设置为false。

window

设置窗口外观。由一组子属性构成，分别如下：

title：字符串，设置默认title。

width/height：主窗口的大小

toolbar：布尔值。是否显示导航栏。

icon：窗口的icon

position：字符串。窗口打开时的位置，可以设置为“null”、“center”或者“mouse”

min_width/min_height：窗口的最小值

max_width/max_height：窗口的最大值

as_desktop：布尔值，作用待确认

resizable：布尔值，是否允许调整窗口大小。

always-on-top：布尔值，窗口置顶。

fullscreen：是否全屏显示。

show_in_taskbar：是否在任务栏显示图标

frame：布尔值，如果设置为false，程序将无边框显示

show：布尔值，如果设置为false，启动时窗口不可见

kiosk：布尔值，是否使用kiosk模式。

webkit：用来控制webkit一些特性的打开或者关闭，由一组属性组成

plugin：布尔值，是否加载插件，如flash，默认值为false

java：bool值，是否加载Java applets，默认为false

page-cache：bool值，是否启用页面缓存，默认为false

user-agent：应用发起http请求时，使用的user-agent头信息。下列占位符可以被替换

* %name：替换配置中的name属性
* %ver：替换配置中的version属性
* %nwver：被node-webkit版本信息替换
* %webkit_ver：被webkit引擎的版本信息替换。
* %osinfo：被操作系统和CPU信息替换，在浏览器的user agent字符串中可以被看到。

示例配置：

```
{
  "main": "html/index.html",
  "name": "tbs_debug",
  "version": "0.1.0",
  "node-main": "js/front-filter.js",
  "window": {
    "title": "TBS调试工具 v0.1.0",
    "icon": "images/x5_icon.png",
    "width": 799,
    "height": 799
  },
  "user-agent": "测试 %ver %nwver %webkit_ver windows7",
  "chromium-args": "-ignore-certificate-errors"
}
```

chromium-args 字符串，自定义chromium启动参数。参考地址：https://src.chromium.org/svn/trunk/src/content/public/common/content_switches.cc

js-flags 字符串，传递给js引擎(V8)的参数。例如，想启用Harmony Proxies和Collections功能，可以使用如下配置方式：

```
{
	"js-flags": "--harmony_proxies --harmony_collections"
}
```

inject-js-start / inject-js-end：字符串，指定一个js文件

inject-js-start：该js文件会在所有css文件加载完毕，dom初始化之前执行；
inject-js-end：该js文件会在页面加载完毕，onload事件触发之前执行。

snapshot：string类型，应用程序的快照文件路径。包含编译的js代码。使用快照文件可以有效保护js代码。

dom_storage_quota：int类型，dom 存储的限额（以自己为单位）。建议限制为你预想大小的2倍

no-edit-menu：布尔值，Edit菜单是否显示。仅在Mac系统下有效

description：简要描述

version：版本信息

keywords：关键词

maintainers：软件维护者信息，是一个数组，每个维护人的信息中，name字段是必须字段，其他两个（email和web）是可选字段，示例如下：

```
"maintainers":[ {
	"name": "Bill Bloggs",
	"email": "billblogs@bblogmedia.com",
	"web": "http://www.bblogmedia.com",
}]
```

contributors：贡献者信息，格式同maintainers，按照约定，第一个contributor是该应用的作者

bugs：提交bug的url。可以是“mailto：”或者“http://”格式

licenses：一个数组，可以包含多个声明。每个声明包含“type”和“url”两个属性，分别指定声明的类型和文本。

示例如下：

```
"licenses": [
{
	"type": "GPLv2",
	"url": "http://www.example.com/licenses/gpl.html",
}
]
```

repositories：程序包的存储地址数组。type和url指定可以下载或者克隆程序包的地址，如果程序包不在根目录中，需要在path属性指定相对目录

#### 小结

基本涵盖了package.json的所有字段的说明，还有些字段现阶段node-webkit也没有使用（description，version，keywords，maintainers，contributors，bugs，licenses，repositories）。

















