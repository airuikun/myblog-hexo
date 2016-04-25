title: hexo快速入门 —— 静态博客框架
date: 2015-2-21 16:31:35
tags: [node.js, javascript, 分享]
---


[hexo](https://github.com/hexojs/hexo)是一款基于Node.js的静态博客框架。目前在GitHub上已有1375 star 和 219 fork

{% img  http://i.imgur.com/40XuB.png  %}


## 特性

* 风一般的速度
Hexo基于Node.js，支持多进程，几百篇文章也可以秒生成。

* 流畅的撰写
支持GitHub Flavored Markdown和所有Octopress的插件。



* 扩展性
Hexo支持EJS、Swig和Stylus。通过插件支持Haml、Jade和Less.



<!--more-->


## 快速入门

***

### 安装

前提是必须先安装 Node.js，至于怎么安装自己 Google 吧


``` bash
npm install hexo -g
```

仅需一步就把 Hexo 本体和所有相依套件安装完毕，很简单吧？



### 升级


更新hexo到最新版

``` bash
npm update hexo -g
```



### 初始化



``` bash
hexo init <folder>
```

如果指定 folder，便会在目前的资料夹建立一个名为 folder的新资料夹；否则会在目前资料夹初始化。




### 创建新博客



``` bash
hexo new "Hello World"
```




### 生成网站



``` bash
hexo generate
```


### 服务器



``` bash
hexo server
```


伺服器会跑在 http://localhost:port （port 预设为 4000，可在 _config.yml 设定）












