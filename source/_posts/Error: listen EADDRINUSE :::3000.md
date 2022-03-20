---
title: 'Error: listen EADDRINUSE :::3000'
date: 2018-11-5 21:46:59
tags: [listen eaddrinuse, 3000, lsof]
description: PID 为上调命令查看的 3000 被占用时的 PID，如下图所示
---

解释：端口已被占用
解决方法：
1. 修改本服务要用的端口。如 3001
2. 停止 3000 端口。
```bash
lsof -i:3000 # 查看 3000 端口被占用情况
kill -9 xxx
```
PID 为上调命令查看的 3000 被占用时的 PID，如下图所示
![在这里插入图片描述](/images/201812/20181113104748419.png)

bingo... 

---
_十年饮冰，难凉热血。_
_十年追梦，岂忘初心？_
	一一 Mobro Zhu
