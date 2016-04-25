title: 通过NPM快速发布你的NodeJS模块（组件包）
date: 2015-12-21 16:15:06
tags: [分享, node.js]
---
1、更新 NPM - [ <code>npm install -g npm</code> | 该步骤可选；最好使用新版本]

楼主当前版本号 2.6.1 ，如果更新报错，可以尝试 [国内淘宝镜像][1]

	$ npm -v
	2.6.1

	//用淘宝镜像升级 npm 楼主版本号是 2.0.1 查看指令：$ cnpm -v 
	$ npm install -g cnpm --registry=https://registry.npm.taobao.org
	//再用镜像升级 npm
	$ cnpm install npm -g
	//发布模块之前 记得 把镜像地址注册回 npmjs
	npm config set registry http://registry.npmjs.org


<!--more-->


2、建立一个自己的项目 - [ <code>npm init</code> ]

这个此处略过，可参考： [NodeJS实战：Express+Mongoose+ejs][5] 
包括如何初始化项目 package.json 最好再添加一份 README.md
楼主以一次小练习为例：GitHub 库地址 : [nodejs-exercise][4]


3、[注册 npm 账号][2] - [ <code>npm adduser</code> ]

最好 [Github 也注册一个账号][3] 

	//再本地添加
	$ npm adduser
	Username: 
	Password:
	Email: (this IS public) 
	//登陆账号
	npm login  


4、发布模块（组件包）- [ <code>npm publish</code> ]

	$ cd firstblood
	$ npm publish  
	+ firstblood@0.0.1


记得检查 package.json 的 项目名称，以及版本号，如果后续修改要同步，那么版本号一定要修改；

去官网，查看自己的项目 <code>https://www.npmjs.com/package/你的包名称</code>

> 记得领取 下载"图片徽章"

> 常用的如 ： [david-dm][6] 、 [NodeICO][7] 、[shields.io][8]
> 比如 楼主刚刚发布的练习模块：

[![NPM](https://nodei.co/npm/firstblood.png?downloads=true&downloadRank=true&stars=true)](https://nodei.co/npm/firstblood/)


5、安装测试自己发布的模块

	$ npm install firstblood && cd node_modules/firstblood
	$ npm install && node app

完

----

[1]: http://npm.taobao.org/ "国内淘宝镜像"
[2]: https://www.npmjs.com/signup "注册NPM账号"
[3]: https://github.com/join "github"
[4]: https://github.com/highsea/nodejs-exercise "nodejs-exercise"
[5]: http://www.cnblogs.com/highsea90/p/4308794.html "NodeJS实战：Express+Mongoose+ejs"
[6]: https://david-dm.org/ "david-dm"
[7]: https://nodei.co/ "NodeICO"
[8]: http://shields.io/ "shields.io"