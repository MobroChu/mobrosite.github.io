---
title: ERR! registry error parsing json
date: 2018-05-22 17:30:14
tags: npm 
---

#### 报错日志：
ERR! registry error parsing json  
ERR! registry error parsing json

#### 解决过程：
从github上克隆一个项目，在npm i的时候，一直报npm err! registry error parsing json。请注意，在这儿有【registry】这个关键字哦，所以猜测是registry的问题，而公司用的网download是完全没有问题的。所以接下来我就试了一下淘宝镜像。

``` npm
    npm config set registry http://registry.cnpmjs.org/
```

然而，天不随人意。依然没有解决问题，接着，我找了度娘，度娘也告诉我就是 registry 的问题，那肿么办呢？我原本用的是无镜像的，那我抱着试试的态度，再来了一次。但是我将registry的值改了一下：

``` npm 
npm config set registry https://registry.npm.taobao.org/
```

咦，这是一个什么鬼。居然成功了！！！
对头，mmp，他就是成功了。
希望能帮助到和我遇见同样问题的菜鸟。
并希望有料的大佬，能帮我解答一下，why...

#### 扩展：
``` 
// 查看当前配置
npm config list 

// 安装镜像
npm config set registry https://registry.npm.taobao.org/ 
npm config set disturl https://npm.taobao.org/dist

// 删除镜像
npm config delete registry 
npm config delete disturl  

```

