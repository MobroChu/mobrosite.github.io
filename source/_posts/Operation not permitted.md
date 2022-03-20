---
title: Operation not permitted
date: 2018-10-28 18:06:12
tags: [centos 7, operation not permitted, 安全防范]
description: centos7 下，修改文件夹的权限时，报了这么一个错误。linux 下，此法依然奏效。
---

centos7 下，修改文件夹的权限时，报了这么一个错误。linux 下，此法依然奏效。
_错误日志：  
`chmod: changing permissions of ‘/opt/apps/images/1.png’: Operation not permitted`_
![在这里插入图片描述](/images/201812/20181125223742836.png)

网上很多说看看有没有 i 属性的。
```bash
lsattr /opt/apps # 查看文件属性
```
![在这里插入图片描述](/images/201812/2018112522410050.png)
然后，也没有 i 属性呀。那么应该怎么解决这个问题呢？
若有 i 属性的，可以先按照网上删除 i 属性的方式来解决。命令如下：
```bash
chattr -i /opt/apps	# 删除 i 属性
chattr +i /opt/apps	# 添加 i 属性
```
若跟我一样，**没有 i 属性**，还是报 `Operation not permitted` 这个错误，那么，切换成 root 用户，我这儿的 Administrator 用户是完全复制 root 用户的，单也存在了改不了权限的问题。
![在这里插入图片描述](/images/201812/20181125225539349.png)

如果 root 还是改不了权限，则应该考虑，服务器上是否安装 `安全防范` 之类的工具包。

唉~~~ 菜鸟一枚，正挣扎在代码一线中...

----
恰恰是实现梦想的可能性，才使生活变得有趣。  一一 《牧羊少年的奇幻之旅》

