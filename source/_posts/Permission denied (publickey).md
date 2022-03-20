---
title: Permission denied (publickey)
date: 2018-12-03 10:16:56
tags: [ssh, permission denied]
---
这个错误的意思权限不够。呐~
先执行以下以下命令，看看 git 在连接的哪一步除了问题
```bash
ssh -v git@github.com
```
日志若如下，则继续：
![在这里插入图片描述](/images/201812/20181112183240688.png)

这说明 git 在连接的时候，去 `~/.ssh/` 下找了默认的 id_rsa，而我的 `~/.ssh/` 目录下为 github.com 生成的 ssh key 起名为 Mobro_Chu 了，所以就找不到。
解决方案：
1. 删除原有的 ssh key，重新生成。
按照 [通过 ssh 建立本地与 github 的连接](https://blog.csdn.net/weixin_41610178/article/details/83996863) 这篇文章来实现。
2. 增加配置配置文件 config
```bash
cd ~/.ssh
touch config	# 注意文件 config 是没有后缀的
```
修改 config 文件。为 config 增加 如下代码
> Host github.com
> HostName github.com		# github.com 可变的
> User git
> IdentityFile ~/.ssh/Mobro_Chu	# 这里的 Mobro_Chu 根据自己的 ssh key 文件不同而变

检查连接是否成功
```bash
ssh -T git@github.com
```
若未成功，则继续查看连接状态。`ssh -v git@github.com`，然后继续找关键字，看看 ssh 连接的时候，是否访问了 Mobro_Chu 这个 ssh key。若向下图一样，还是访问的 rsa_id ，则说明，我们的 Mobro_Chu 这个 ssh key 并未添加到 ssh 连接队列中去。所以
```bash
ssh-add -K ~/.ssh/Mobro_Chu
```
如果报了一个含有 `Could not open a connection to your authentication agent. ` 的错误，则表示没有 代理权限。增加代理权限即可。
```bash
ssh-agent bash # 非 windows 系统
eval `ssh-agent` # windows 系统

顺便谢谢其他命令
ssh-add -l # 查看 ssh key 队列中有哪些 key
ssh-add -D # 删除所有的 ssh key
```

这种方式可以继续使用原来的 ssh key，且可解决 __<span style="font-size: 18px; color: green;">同一电脑连接多个代码库</span>__ 的问题

---
_不乱于心,不困于情。_
_不畏将来,不念过往。_
_如此,安好！_
_无愧于天,无愧于地。_
_这样,人生!_
一一  丰子恺《不宠无惊过一生》

