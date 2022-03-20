---
title: 常用的linux命令
date: 2018-06-05 17:02:57
tags: [linux, ques]
description: 个人工作中遇到的一些linux命令，简单汇总，方便以后查阅。
---

## 查找某目录下某类文件
``` bash
find . -name ".DS_Store" -type f -print

# find: 主命令  
# . : 当前目录下（可变）  
# -name: 通过名查找  
# ".DS_Store": 后缀  
# -type f: 一般正规文件  
# -print: 查询结果打印
```
  

eg： 查找当前目录下所有的`.html`文件， 并打印就应该使用 
`find . -name ".html" -type f -print`  

``` bash
find . -name ".DS_Store" -type f -print -exec command {} \;

# -exec: 命令扩展，查询结束后要执行 command 命令
# {}: 查询结果放到 {} 中
# \;: 扩展命令结束符，表示 到 ; 结束
```

eg: 找到`.html`后,删除所有的查询结果，应使用：
`find . -name ".DS_Store" -type f -print -exec rm -rf {} \;`

## 不让 mac os 生成 .DS_Store 文件 
``` bash
defaults write com.apple.desktopservices DSDontWriteNetworkStores true
```
