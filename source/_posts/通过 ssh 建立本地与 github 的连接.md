---
title: 通过 ssh 建立本地与 github 的连接
date: 2018-12-03 10:16:56
tags: [ssh, github, ssh-keygen, permission denied]
---
1. 查看 ssh key 
```bash
cd ~/.ssh
cat 名字.pub
```
如果本地没有 ssh key，则新建
```bash
ssh-keygen -t rsa -C "mobro_chu@163.com"
```
2. 在github上新建一个 ssh key，粘贴 ssh key 到相应位置
3. 若不成功，则查看邮箱密码之类的配置好了没有
```bash
git config user.name/user.email         # 查看用户名/邮箱
git config --global user.name
git config --global user.email mobro_chu@163.com
```
4. 检查连接成功与否
```bash
ssh -T git@github.com
```
5. 可能报错
A. [Permission denied (publickey).](https://blog.csdn.net/weixin_41610178/article/details/83996609)

---
散是伤，聚是殇。 一一 Mobro Zhu

